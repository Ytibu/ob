# Codex 配置

> Codex 是 OpenAI 的编码智能体（CLI）。本文记录本机配置要点与常用命令，2026-09-02 整理。

## 安装与验证

```bash
npm install -g @openai/codex
codex --version    # 本机 0.152.0
```

## 配置目录 ~/.codex

| 路径 | 作用 |
| --- | --- |
| AGENTS.md | 全局指令，所有会话生效；AGENTS.override.md 存在时优先 |
| config.toml | 主配置：模型、provider、项目信任、插件启用 |
| skills/ | 个人技能目录，新会话自动发现 |
| plugins/cache/ | 已安装插件缓存 |
| .tmp/plugins/ | 官方插件市场快照（github.com/openai/plugins） |

## 项目指令 AGENTS.md

- Codex 读取仓库根 `AGENTS.md`（支持嵌套目录），**不读 CLAUDE.md**；Claude Code 则相反。
- 本项目 `andrej-karpathy-skills` 仓库根目录已建 `AGENTS.md`，为 CLAUDE.md 四原则的镜像（编码前思考 / 简洁优先 / 精准修改 / 目标驱动）。

## 技能（Skill）

- 技能 = 一个目录 + `SKILL.md`，frontmatter 至少含 `name`（小写连字符）与 `description`。
- 安装：复制目录到 `~/.codex/skills/<技能名>/`，新会话生效。
- 已装：`karpathy-guidelines`（中文版，Karpathy 编码行为准则，来自 andrej-karpathy-skills 仓库）。

## 插件（Plugin）

- 插件清单：`.codex-plugin/plugin.json`（不是 Claude 的 `.claude-plugin/`）。
- 市场：个人 `~/.agents/plugins/marketplace.json`；仓库 `<repo>/.agents/plugins/marketplace.json`；官方市场快照自动同步。
- 已装：`superpowers@openai-api-curated`（官方市场中最接近"行为准则"的方法论插件：规划 / TDD / 调试 / 验证）。

```bash
# 列出插件
codex plugin list
# 从市场安装
codex plugin add <插件名>@<市场名>
# 添加社区市场（例：awesome-codex-plugins）
codex plugin marketplace add https://github.com/hashgraph-online/awesome-codex-plugins.git --ref main --sparse .agents/plugins --sparse plugins
# 卸载
codex plugin remove <插件名>
```

## 备注

- 插件 / 技能 / AGENTS.md 的变更均在新会话生效。
- 系统预装技能位于 `~/.codex/skills/.system/`（openai-docs、plugin-creator、skill-creator、skill-installer 等）。
- 已配置用户环境变量 `CODEX_HOME=C:\Users\dar06\.codex`，避免 CLI 报 "Could not find home directory"。
- 注意：`config.toml` 中 `experimental_bearer_token` 为明文 API Key，请勿外泄或提交到仓库；如需改环境变量引用可另行配置。

