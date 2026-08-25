# Project Driven Learning — Agent Skill

> **Learn any new knowledge by building a real project — and turn that project into a resume-ready, interview-proof experience.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Platform: WorkBuddy / Claude Code / Cursor / Any AI](https://img.shields.io/badge/platform-WorkBuddy%20%7C%20Claude%20Code%20%7C%20Any%20AI-blue)

An [Agent Skill](https://agentskills.io) that turns "I want to learn X" into a complete learning loop:

```
learn → build a real project → log real problems → verify understanding → produce a resume-worthy project
```

When you finish, you get three things at once: **the knowledge (verified)**, **a real project (with production perspective)**, and **a resume item that survives interviewer questioning**.

## Why this skill

Most learning skills teach you *knowledge* but leave you with nothing to show. Most project tutorials get you a *working demo* but no depth. This skill is the missing link:

- **Troubleshooting is the curriculum** — the real bugs you hit (and how you fixed them) are both the deepest learning and the best interview stories
- **Interview-value driven design** — every project is designed with production readiness (observability, resilience, security, performance, HA, DR), quantifiable results, and a "deep water" area interviewers will want to probe
- **Self-growing case library** — every completed project gets archived as a new case, so the skill gets smarter the more you use it

## Installation

| Tool | Command / Method |
|---|---|
| **Claude Code** | `git clone https://github.com/BellWinter/project-driven-learning.git ~/.claude/skills/project-driven-learning` |
| **WorkBuddy** | `git clone https://github.com/BellWinter/project-driven-learning.git ~/.workbuddy/skills/project-driven-learning` |
| **Cursor / Windsurf** | Copy the content of `SKILL.md` into `AGENTS.md` or `.cursor/rules/` |
| **Any AI (universal)** | Paste the full content of `SKILL.md` as a system prompt |

Restart your tool after installation so the skill gets discovered.

> **Note on the standard:** this repo follows the [Agent Skills specification](https://agentskills.io) — a folder with a `SKILL.md` (YAML frontmatter + instructions). Works in any tool that supports skills or rule files.

## Usage

Just tell your AI, in plain language:

```
"我想学 Prometheus，帮我做个能写进简历的练手项目"      (Chinese)
"Teach me React by building a project I can put on my resume"   (English)
```

The AI will load this skill and walk you through the 8-stage loop:

| # | Stage | What happens |
|---|---|---|
| 0 | Clarify | ≤3 questions: goal, level, time budget |
| 1 | **Design (interview-value)** | functional scope + ≥2 production-ready dimensions + quantifiable metrics + deep-water area; gated by an interview-value checklist (score < 6 → redesign) |
| 2 | Setup | environment, mirrors, versions verified |
| 3 | Implement | stage-by-stage with verifiable milestones; you do the hands-on work |
| 4 | **Troubleshoot (mandatory)** | every problem logged: symptom → root cause → fix → lesson |
| 5 | Retrospect | distill 5-10 reusable lessons + knowledge map |
| 6 | **Verify** | retrieval practice (mini-test) + Feynman explanation — running ≠ learning |
| 7 | Output | final resume entry (STAR + metrics) + mock interview; gaps loop back to the project |

## Repository structure

```
project-driven-learning/
├── SKILL.md                    # The skill itself (frontmatter + instructions)
├── README.md / README.zh-CN.md # This doc (EN / 中文)
├── CHANGELOG.md                # Version history
├── LICENSE                     # MIT
├── CONTRIBUTING.md
├── references/
│   ├── learning-science.md            # Cognitive-science basis: retrieval practice, Feynman, spaced repetition
│   ├── cicd-project-case.md           # Case ①: GitLab+Jenkins+K8s CI/CD (24 real troubleshooting records)
│   └── python-data-analysis-case.md   # Case ②: Python data analysis (non-DevOps example)
└── assets/                     # 7 field-agnostic templates
    ├── project-plan-template.md            # One file tracking the whole journey
    ├── interview-value-checklist.md        # Interviewer-question gate for project design
    ├── troubleshooting-log-template.md     # Core: problem log
    ├── learning-check-template.md          # Verification (mini-test + Feynman)
    ├── resume-final-template.md            # Final resume entry (STAR + metrics)
    ├── interview-prep-template.md          # Mock interview
    └── learning-path-template.md           # Multi-project learning roadmap
```

## Case studies

The `references/` folder contains real, completed projects as runnable examples of the methodology:

- **CI/CD platform** (GitLab + Jenkins + Container Registry + Kubernetes) — 24 real troubleshooting records with root causes and fixes
- **Python e-commerce data analysis** — a non-DevOps example proving the method is field-agnostic

Each time you complete a project, archive it here the same way — the case library grows with you.

## Contributing

Contributions are welcome: improve the templates, add cross-platform instructions, or contribute your own completed project as a case study (format: `assets/troubleshooting-log-template.md`). See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE)

## Disclaimer

This skill provides a method and self-check framework. The actual quality of your project depends on your real practice — the depth of the problems you hit, and how seriously you retrospect them. No framework can replace genuine effort.
