# Project Driven Learning — 项目驱动学习法（Agent Skill）

> **学任何新知识，都通过"做一个真实项目"来完成——并把项目变成能写进简历、扛得住面试官追问的经历。**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![平台: WorkBuddy / Claude Code / Cursor / 任意 AI](https://img.shields.io/badge/platform-WorkBuddy%20%7C%20Claude%20Code%20%7C%20Any%20AI-blue)

一个符合 [Agent Skills 标准](https://agentskills.io) 的 AI 技能，把"我想学 XX"变成一个完整的学习闭环：

```
学习 → 做真实项目 → 记录真实排障 → 验证理解 → 产出能写进简历的项目
```

学完一次，三样同时到手：**知识（已验证）**、**真实项目（带生产视角）**、**能扛住面试官追问的简历经历**。

## 为什么有这个 skill

纯学习类 skill 教了知识但学完没产出；纯项目教程让你跑通 demo 但没深度。这个 skill 补上中间的缺口：

- **排障即学习**——真实踩过的坑（以及怎么修的）既是学得最深的地方，也是最好的面试故事
- **面试含金量导向设计**——每个项目都按生产就绪（可观测/弹性/安全/性能/高可用/容灾）、量化指标、深水区来设计
- **案例库自增长**——每完成一个项目就归档为新案例，skill 越用越强

## 安装

| 工具 | 安装方式 |
|---|---|
| **Claude Code** | `git clone https://github.com/BellWinter/project-driven-learning.git ~/.claude/skills/project-driven-learning` |
| **WorkBuddy** | `git clone https://github.com/BellWinter/project-driven-learning.git ~/.workbuddy/skills/project-driven-learning` |
| **Cursor / Windsurf** | 把 `SKILL.md` 内容粘贴到 `AGENTS.md` 或 `.cursor/rules/` |
| **任意 AI（通用）** | 把 `SKILL.md` 全文作为系统提示词粘贴 |

装完重启工具，让 AI 扫描到新技能。

> **关于标准**：本仓库遵循 [Agent Skills 规范](https://agentskills.io)——一个文件夹 + `SKILL.md`（YAML frontmatter + 指令）。任何支持技能/规则文件的 AI 工具都能用。

## 使用方法

直接对你的 AI 说：

```
"我想学 Prometheus，帮我做个能写进简历的练手项目"
"Teach me React by building a project I can put on my resume"
```

AI 会加载本 skill，带你走 8 阶段闭环：

| 阶段 | 内容 | 说明 |
|---|---|---|
| 0 | 需求澄清 | ≤3 个问题：目标、水平、时间预算 |
| 1 | **项目设计（含金量导向）** | 功能范围 + ≥2 项生产就绪 + 量化指标 + 深水区；用含金量清单把关（<6 分重设计） |
| 2 | 环境准备 | 镜像源、版本可验证 |
| 3 | 分阶段实施 | 每阶段有完成标志；关键操作你亲手做 |
| 4 | **排障记录（强制）** | 每个问题记录：现象 → 根因 → 方案 → 教训 |
| 5 | 复盘沉淀 | 提炼 5-10 条方法论 + 知识地图 |
| 6 | **学习验证** | 检索练习（mini-test）+ 费曼讲解——跑通 ≠ 学会 |
| 7 | 简历/面试输出 | 简历成品（STAR + 量化）+ 面试模拟；短板循环回项目补强 |

## 仓库结构

```
project-driven-learning/
├── SKILL.md                    # 技能本体（frontmatter + 指令）
├── README.md / README.zh-CN.md # 说明文档（EN / 中文）
├── CHANGELOG.md                # 版本历史
├── LICENSE                     # MIT
├── CONTRIBUTING.md             # 贡献指南
├── references/
│   ├── learning-science.md            # 学习科学依据：检索练习、费曼、间隔复习
│   ├── cicd-project-case.md           # 案例①：GitLab+Jenkins+K8s CI/CD（24 条真实排障）
│   └── python-data-analysis-case.md   # 案例②：Python 数据分析（非运维方向示范）
└── assets/                     # 7 个领域无关模板
    ├── project-plan-template.md            # 项目学习档案（一个文件管全程）
    ├── interview-value-checklist.md        # 面试含金量自检（项目设计把关）
    ├── troubleshooting-log-template.md     # 排障记录（核心）
    ├── learning-check-template.md          # 学习验证（mini-test + 费曼）
    ├── resume-final-template.md            # 简历成品（STAR + 量化）
    ├── interview-prep-template.md          # 面试模拟
    └── learning-path-template.md           # 多项目学习路径
```

## 案例库

`references/` 里是两个真实完成的项目，示范方法论怎么跑：

- **CI/CD 平台**（GitLab + Jenkins + 私有镜像仓库 + Kubernetes）——24 条真实排障记录（现象/根因/方案）
- **Python 电商数据分析**——非运维方向的例子，证明方法不挑领域

每完成一个项目，按同样格式归档进来——案例库随你的学习一起增长。

## 贡献

欢迎贡献：完善模板、补充跨平台说明、或把你的完成项目作为案例提交（格式见 `assets/troubleshooting-log-template.md`）。详见 [CONTRIBUTING.md](CONTRIBUTING.md)。

## License

[MIT](LICENSE)

## 免责声明

本 skill 提供方法与自检框架。项目的真实含金量取决于你的实际投入——踩了多少坑、复盘有多认真。任何框架都无法替代真实的努力。
