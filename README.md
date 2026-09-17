<div align="center">

# agent-skills

**一个技能一个目录，各自是独立的东西** · 与 harness、厂商解耦，任何支持 `SKILL.md` 约定的 agent 都能用

[![skills](https://img.shields.io/badge/skills-1-blue)](#技能)
[![license](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![validate](https://github.com/plyflai/agent-skills/actions/workflows/validate.yml/badge.svg)](https://github.com/plyflai/agent-skills/actions/workflows/validate.yml)

</div>

---

## deeptalk

> **把零散想法问透。** 丢给它一段 bullet points、草稿或半句话，它先给一轮即时补全，再把真实取舍摆到你面前——不替你拍板。

[**→ 完整说明（安装 / 协议 / 边界）**](skills/deeptalk/)

这些说法会命中它：

- 「我有个想法，帮我理一理」
- 丢一段 bullet points 或草稿，说「这样够了吗 / 还缺什么」
- 「帮我把这个需求问透」

它和普通「帮我写需求文档」的区别在**证据标注**：每个新增点都带标签（`补全` / `拆分` / `盲区候选` / `待决定` / `假设`），所以你能分清哪些是你真说过的、哪些是它替你假设的。`盲区候选` 和 `补全` 都只是候选，不等于你已批准。

```bash
git clone https://github.com/plyflai/agent-skills && cd agent-skills
./scripts/install.sh deeptalk --target <你的技能目录>
```

## 技能

每个技能自成一个目录、一个入口 `SKILL.md`，**互不隶属**。目录名 = `SKILL.md` 里的 `name`，装到本地也是这个目录名，没有中间层。

| 技能 | 一句话 | 依赖 |
| --- | --- | --- |
| [`deeptalk`](skills/deeptalk/) | 把零散想法、bullet points、草稿即时深化为结构化需求，补出高信号的盲区候选、拆分项与待决定事项 | 无 |
| [`_template`](skills/_template/) | 新增技能的骨架，复制改名即用（以下划线开头，不参与安装） | 无 |

DSH 专属技能不在这里，见 [plyflai/dsh-skills](https://github.com/plyflai/dsh-skills)。

## 安装

不管哪个 harness，本质都是**把技能目录放到它读技能的地方**。

### 脚本装（任何 harness）

```bash
./scripts/install.sh deeptalk --target <你的技能目录>          # 复制
./scripts/install.sh deeptalk --target <你的技能目录> --link   # 软链接，git pull 后立即生效
./scripts/install.sh --list                                    # 看有哪些技能
./scripts/install.sh --all --target <你的技能目录>             # 全部装
```

`--target` 记得写对——不同 harness 读技能的位置不一样，见下。

### 手动拷

把 `skills/<技能名>/` 整个目录拷进你的技能目录即可，无需其他改动。

### 只用提示词

把技能的 `SKILL.md` 贴进系统提示词也能用，只是 `references/` 不会被自动加载，会丢掉一部分细节。

## 各 harness 装在哪

各 harness 的技能目录：

| Harness | 用户级 | 项目级 | 调用 |
| --- | --- | --- | --- |
| **跨工具通用** | `~/.agents/skills/` | `.agents/skills/` | 取决于各 harness |
| **Codex CLI** | `~/.agents/skills/` | `.agents/skills/` | `/skills` 或 `$<技能名>` |
| **Gemini CLI** | `~/.agents/skills/`<br>`~/.gemini/skills/` | `.agents/skills/`<br>`.gemini/skills/` | 说完需求后模型激活，需你确认 |
| **Cursor** | `~/.agents/skills/`<br>`~/.cursor/skills/` | `.agents/skills/`<br>`.cursor/skills/` | 说完需求后模型自行调用 |
| **Roo Code** | `~/.agents/skills/`<br>`~/.roo/skills/` | `.agents/skills/`<br>`.roo/skills/` | 说完需求后模型自行调用 |
| **Qwen Code** | `~/.qwen/skills/` | `.qwen/skills/` | `/<技能名>` 或 `/skills` 面板 |
| **Claude Code** | `~/.claude/skills/` | `.claude/skills/` | 说完需求后模型自行调用，或 `/<技能名>` |
| **DSH** | `~/.agents/skills/`<br>`~/.dsh/skills/` | `.agents/skills/`<br>`.dsh/skills/` | 说完需求后模型自行加载 |
| **Cline** | `~/.cline/skills/` | `.cline/skills/` | 需先在 Settings → Features 打开 Skills |
| **GitHub Copilot** | — | `.github/skills/` | 仓库/组织级，随 Copilot coding agent |
| **WorkBuddy** | `~/.workbuddy/skills/` | `.codebuddy/skills/` | 说完需求后模型自行调用（腾讯云 CodeBuddy 家族） |

### 装一次，多个 harness 共用

`~/.agents/skills/` 是事实上的**跨工具共用路径**：Codex、Gemini CLI、Cursor、Roo Code、DSH 都读它，Gemini CLI 官方文档原话是「`.agents/skills/` 别名提供了一条可互操作的路径，跨不同 AI 工具保持兼容」。所以最省事的装法是一次装到那儿：

```bash
git clone https://github.com/plyflai/agent-skills && cd agent-skills
./scripts/install.sh deeptalk --target ~/.agents/skills
```

剩下三家要各装一份（`--link` 用软链接，免得复制多份）：

```bash
./scripts/install.sh deeptalk --target ~/.claude/skills --link   # Claude Code
./scripts/install.sh deeptalk --target ~/.qwen/skills   --link   # Qwen Code
./scripts/install.sh deeptalk --target ~/.cline/skills  --link   # Cline
```

### 用各 harness 自带的安装器

```bash
# Codex
codex plugin marketplace add plyflai/agent-skills
codex plugin add deeptalk@plyflai-skills

# Gemini CLI
gemini skills install https://github.com/plyflai/agent-skills.git --path skills/deeptalk

# Claude Code
/plugin marketplace add plyflai/agent-skills
/plugin install deeptalk@plyflai-skills
```

> `.claude-plugin/marketplace.json` 不只 Claude Code 读——**Codex 也读**（实测 `codex plugin marketplace add` 能解析出 `deeptalk@plyflai-skills` 并安装成功）。所以这一个文件同时服务两个 harness。

### 目录名与 `agents/` 子目录

- 技能目录名要和 `SKILL.md` 里的 `name` 一致——多数 harness 直接按目录名注册。
- `agents/openai.yaml` 只有 Codex 用（界面元数据），**其他 harness 会忽略整个 `agents/`**，不影响加载。
- `~/.agents/skills/` 与 `~/.codex/skills/` 都能被 Codex 发现：前者是官方文档列出的发现路径，后者是 `$skill-installer` 的默认安装位置。

### 调用方式

Agent Skills 是**按需加载**的：harness 启动时只注入每个技能的 `name` 和 `description`，命中触发条件时才读整个 `SKILL.md`。所以 `description` 里写的是「什么时候用我」，不是「我有什么功能」。


技能目录本身与 harness 无关（都是 `SKILL.md` + 可选 `references/` `scripts/`），差的只是放到哪。

## 新增一个技能

```bash
cp -R skills/_template skills/my-new-skill
# 改 skills/my-new-skill/SKILL.md 的 name（必须与目录名一致）与 description
./scripts/validate.sh
```

新增后把它作为**独立条目**加进 `.claude-plugin/marketplace.json`——一个条目一个技能，条目名等于技能目录名。`./scripts/validate-marketplace.sh` 会检查这两点，合并成伞形条目会被直接报错。

## 仓库结构

```text
agent-skills/
├── skills/
│   └── deeptalk/              # 一个技能一个目录，目录名 = SKILL.md 里的 name
│       ├── SKILL.md           # 入口，必须
│       ├── README.md          # 这个技能自己的说明页
│       ├── references/        # 细节文档，按需读取
│       └── agents/            # 可选，Codex 侧的界面元数据
├── scripts/
│   ├── install.sh
│   ├── validate.sh
│   └── validate-marketplace.sh
├── .claude-plugin/
│   └── marketplace.json       # 每个技能一个独立条目
├── .github/workflows/validate.yml
├── LICENSE
└── README.md
```

## 校验

```bash
./scripts/validate.sh              # 结构与 frontmatter（CI 跑同一个）
./scripts/validate-marketplace.sh  # 清单与 skills/ 是否一致（CI 跑同一个）
```

`validate.sh` 检查：`SKILL.md` 存在且 frontmatter 合法、`name` 与目录名一致且符合 `^[a-z0-9]+(-[a-z0-9]+)*$`、`description` 非空且不超 1024 字符、`SKILL.md` 里引用的相对路径文件真实存在、无 `.DS_Store` 与嵌套 git 仓库。

`validate-marketplace.sh` 检查：清单 JSON 合法、marketplace 名不是 Claude Code 保留名、**一个条目只声明一个技能**、条目名等于技能目录名、`skills/` 下的真技能没有漏声明。

## 贡献

新增技能照 `skills/deeptalk/` 的结构来：一个目录、一个入口 `SKILL.md`、细节放 `references/`、给它自己的 `README.md`。提交前跑两个校验脚本。

## License

[MIT](LICENSE) © 2026 plyflai
