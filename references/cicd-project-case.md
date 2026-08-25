# 示例案例库：GitLab + Jenkins + K8s CI/CD 自动部署项目

> **定位说明**：本文件是"项目驱动学习法"的**第一个落地案例**（2026-08 完成，运维/DevOps 方向），用于示范 7 阶段流程如何执行，并提供可复用的排障方案。
>
> 方法论本身**不限于运维**——学 Python、React、数据分析、AI 应用等任何方向都可套用同样流程。每完成一个新项目，建议以同样格式把案例追加到本文件（或新建 `references/<新项目>.md`），让案例库随学习不断扩充。

## 案例概览

- **学习目标**：云原生 CI/CD 自动化部署（GitLab + Jenkins + GitLab Container Registry + Kubernetes）
- **环境**：VMware Workstation + Ubuntu 22.04 + 1 Master（192.168.152.131，16GB 内存），国内网络环境
- **最终成果**：push 代码 → Jenkins 自动构建 → 推送镜像 → K8s 滚动部署，全流程 SUCCESS，零人工干预

## 架构

```text
开发者 push 代码
    ↓
GitLab (192.168.152.131:8000, Docker 容器)
    ↓ Webhook (GitLab 插件端点 /project/demo-app)
Jenkins (192.168.152.131:8080, Docker 容器, 挂载宿主机 docker.sock)
    ↓ 1. checkout 拉代码  2. docker build  3. docker login + push
GitLab Container Registry (192.168.152.131:5000, GitLab 内置)
    ↓
Kubernetes (kubeadm v1.28.15 + flannel, containerd 2.x)
    ↓ kubectl apply + rollout
demo-app Deployment (2 副本, NodePort 30081) ✅
```

## 问题速查表（24 个真实问题）

| # | 问题现象 | 根因 | 解决方案 |
|---|---|---|---|
| 1 | apt update 被劫持到 `1.1.1.3 disable.htm` | 运营商 HTTP 劫持 | 改用 HTTPS 清华源 |
| 2 | `raw.githubusercontent.com` 无法下载 | GitHub 被墙 | jsdelivr CDN 下载 flannel |
| 3 | 业务镜像拉取不走 mirror 加速 | `ctr` 不走 CRI mirror 配置 | 镜像直接用完整加速地址 `docker.m.daocloud.io/xxx` |
| 4 | Pod 无法调度到 master | master 有 `NoSchedule` 污点 | `kubectl taint` 移除污点 |
| 5 | ingress-nginx 镜像拉取 TLS 超时 | 镜像源不稳定 | 换 `k8s.m.daocloud.io` + 重试 |
| 6 | 坏版本 9.9.9 更新卡住 | 镜像不存在拉取失败 | `kubectl rollout undo` 秒级回滚 |
| 7 | Puma worker 启动超时被杀，CPU 100% | 容器 `--memory=2g` 硬限制 | `docker update --memory=4g` + restart |
| 8 | `--memory=0` 无法取消内存限制 | Docker 特性 | 必须显式给更大值 |
| 9 | 创建 PAT 的 UI 按钮缺失 | 版本 UI 问题 | `gitlab-rails runner` 命令创建 |
| 10 | Harbor 安装包下载全部失败 | GitHub Release 全被墙 | 放弃 Harbor，改用 GitLab 内置 Container Registry |
| 11 | registry 监听 127.0.0.1:5000 外部连不上 | `registry['listen_addr']` 在 GitLab 17 不生效 | sed 改 config.yml addr + `gitlab-ctl restart registry` |
| 12 | reconfigure 后 registry 又变回 127.0.0.1 | 每次 reconfigure 重置 config.yml | reconfigure 后必须重新 sed + restart |
| 13 | nginx 与 registry 抢 5000 端口 | registry_nginx 默认启用 | `registry_nginx["enable"] = false` |
| 14 | docker login 认证 realm 指向 :80 连不上 | external_url 未带端口 | `external_url "http://...:8000"` + `nginx["listen_port"] = 80` |
| 15 | 容器没有 5000 端口映射 | 创建时未加 `-p 5000:5000` | 重建容器加端口映射 |
| 16 | `http: server gave HTTP response to HTTPS client` | containerd 2.x config_path 只接受单目录 | sed 改 config_path 为单目录 + restart containerd |
| 17 | 节点 disk-pressure | swap.img (2.9G) 占用 | 删除 swap.img |
| 18 | flannel subnet.env 丢失 | 内核更新后 br_netfilter 未加载 | `modprobe br_netfilter` |
| 19 | GitLab 网页创建 webhook 报 `Invalid url given` | SSRF 保护拦截内网 IP | Admin → Network → Outbound requests 放行本地网络 |
| 20 | rails runner 报 `event not found` | bash 历史扩展把 `!` 当命令引用 | Ruby 代码用 `h.save` 代替 `h.save!` |
| 21 | Jenkins 403 `anonymous is missing the Job/Build permission` | GitLab 插件端点需匿名 Build 权限 | 项目授权矩阵给 Anonymous → Job → Build |
| 22 | Jenkins 403 `No valid crumb was included` | 远程触发端点绕不开 CSRF crumb | 改用 GitLab 插件端点 `/project/demo-app`（免 crumb） |
| 23 | registry connection refused | registry 进程"假活"，容器内 5000 无监听 | `gitlab-ctl restart registry` 恢复监听 |
| 24 | Docker push 报 HTTPS 错误 | HTTP registry 需声明 | daemon.json 配 `insecure-registries` |

## 详细排障记录（重点问题）

### 问题 19：GitLab webhook `Invalid url given`（SSRF）

- 用户 URL 输入完全正确，网页仍拒绝 → 怀疑全角字符 → 实际是 **SSRF 防护拦内网 IP**
- 诊断：命令行创建 webhook 拿真实报错
  ```bash
  sudo docker exec gitlab gitlab-rails runner "p = Project.find_by_full_path('root/demo-app'); h = p.hooks.new(url: 'http://192.168.152.131:8080/project/demo-app', push_events: true, enable_ssl_verification: false); puts 'valid=' + h.valid?.to_s; puts h.errors.full_messages.join(' | ')"
  # → Url is blocked: Requests to the local network are not allowed
  ```
- 解决：Admin Area → **Settings → Network → Outbound requests** → 勾选 `Allow requests to the local network from webhooks and integrations` + 白名单填 IP → **Save 立即生效，无需 reconfigure**
- 坑：`gitlab_rails['outbound_local_requests_allowlist']` 在旧版本报 undefined method，UI 勾选框才是正确入口

### 问题 21/22：Jenkins 403（权限 vs CSRF）

- `/project/demo-app`（GitLab 插件端点）→ 403 权限错 → 项目授权矩阵给 Anonymous 勾 **Job → Build**
- `/job/demo-app/build?token=xxx`（远程触发端点）→ 403 crumb 错，绕不开 CSRF
- **结论：GitLab 插件端点 + 匿名 Build 权限是正路，不要走远程触发 token 那条路**

### 问题 23：registry 假活（connection refused 复现）

- 现象：宿主机 curl refused；`gitlab-ctl status` 显示 registry run；容器内 `netstat -tln` 无 5000 监听
- 诊断三板斧：curl 测连通 → 进容器看监听 → 看服务日志
- 解决：`gitlab-ctl restart registry` → 监听恢复

### 问题 16：containerd 2.x config_path 大坑

- 现象：hosts.toml 已配置，K8s 拉私有镜像仍报 HTTPS 错误
- 根因：containerd 2.x 默认 `config_path = '/etc/containerd/certs.d:/etc/docker/certs.d'` 是 Docker 风格冒号分隔多目录，**containerd 只接受单目录** → 整个路径无效
- 解决：
  ```bash
  sudo sed -i "s|config_path = '/etc/containerd/certs.d:/etc/docker/certs.d'|config_path = '/etc/containerd/certs.d'|" /etc/containerd/config.toml
  sudo systemctl restart containerd
  ```
- hosts.toml 结构：`/etc/containerd/certs.d/192.168.152.131:5000/hosts.toml` → `server = "http://192.168.152.131:5000"`
- 注意：`crictl pull` 匿名拉私有仓库报 403 是正常现象；Pod 用 imagePullSecrets(regcred) 不受影响

## 关键配置速查

### GitLab webhook
- URL：`http://192.168.152.131:8080/project/demo-app`（GitLab 插件端点，不是 /job/ 路径）
- 触发：仅 Push events；Secret token 留空

### Jenkins 任务
- 触发器：☑️ `Build when a change is pushed to GitLab` + Push Events
- 授权：项目级矩阵，Anonymous → Job → Build

### GitLab gitlab.rb（registry 相关）
```ruby
registry_external_url "http://192.168.152.131:5000"
registry_nginx["enable"] = false
external_url "http://192.168.152.131:8000"
nginx["listen_port"] = 80
```
> reconfigure 后必须：`sed -i 's|addr: 127.0.0.1:5000|addr: 0.0.0.0:5000|' /var/opt/gitlab/registry/config.yml && gitlab-ctl restart registry`

### containerd
- config.toml：`config_path = '/etc/containerd/certs.d'`（单目录）
- hosts.toml：`server = "http://192.168.152.131:5000"`

### Docker daemon
```json
{"registry-mirrors": ["https://docker.m.daocloud.io"], "insecure-registries": ["192.168.152.131:5000"]}
```

### 镜像路径规则
`192.168.152.131:5000/<namespace>/<project>/<image>:<tag>`

## 经验总结（10 条方法论）

1. "服务进程活着 ≠ 服务正常监听" —— 先看监听地址（ss/netstat），再看进程，最后看日志
2. 端口映射 ≠ 端口可用 —— docker-proxy 监听 ≠ 容器内服务监听，要从容器内验证
3. UI 报错可能误导 —— 用命令行/runner 拿真实报错最准
4. reconfigure 会覆盖手工修改 —— 生成式配置文件的手工改动要固化成脚本
5. 国内环境镜像源策略 —— DaoCloud 镜像（`*.m.daocloud.io`）、jsdelivr CDN、清华/阿里源
6. 二选一时选"官方集成" —— Harbor 受阻时，GitLab 内置 Registry 是零成本替代
7. containerd 2.x 与 Docker 配置差异大 —— config_path 单目录，不能套 Docker 思维
8. CSRF/SSRF 是新版系统最常见的"隐形墙" —— 先了解安全模型再排障
9. 诊断三板斧 —— 先 curl 验证连通 → 进容器看监听 → 看服务日志
10. bash 引号/历史扩展坑 —— rails runner 内嵌 Ruby 避免 `!`、用单引号包裹
