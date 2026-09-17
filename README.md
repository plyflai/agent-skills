<div align="center">

# agent-skills

**通用型 Agent Skills 集合** · 与具体 harness、厂商解耦，可被任何支持 `SKILL.md` 约定的 agent 使用

[![skills](https://img.shields.io/badge/skills-1-blue)](#技能清单)
[![license](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![validate](https://github.com/plyflai/agent-skills/actions/workflows/validate.yml/badge.svg)](https://github.com/plyflai/agent-skills/actions/workflows/validate.yml)

</div>

---

## 这是什么

一组**领域无关**的 agent skills。每个技能是一个自包含目录，只有一个入口 `SKILL.md` 加它自己的 `references/`、`scripts/`，不依赖任何特定运行时。

这些技能的共同取向：**不替用户拍板**。它们负责把问题问清楚、把候选盲区摆上桌、把决定权交还给人，而不是直接产出一份看起来完整、实际没人确认过的东西。

DeepSeek Harness 专属的技能不在这里，见 [plyflai/dsh-skills](https://github.com/plyflai/dsh-skills)。

## 技能清单

| 技能 | 一句话 | 依赖 |
| --- | --- | --- |
| [`deeptalk`](skills/deeptalk/) | 把零散想法、bullet points、草稿即时深化为结构化需求，补出高信号的 unknown unknowns、拆分项与待决定事项 | 无（可选增强见下） |
| [`_template`](skills/_template/) | 新增技能的骨架，复制改名即用（以下划线开头，不参与安装） | 无 |

## 快速开始

### 方式一：Claude Code 插件市场

仓库根带 `.claude-plugin/marketplace.json`，可直接作为插件市场添加：

```bash
/plugin marketplace add plyflai/agent-skills
/plugin install agent-skills@plyflai-agent-skills
```

清单里的 marketplace 名是 `plyflai-agent-skills` —— `agent-skills` 本身在 Claude Code 的保留名列表里，第三方用了会导致整个 marketplace 拒绝加载，所以这里换了名。本仓库是 skills-only，条目用 `"source": "./"` + `"strict": false` 直接指向 `skills/`，不需要每个技能各自的 `plugin.json`。

### 方式二：一行装好

```bash
git clone https://github.com/plyflai/agent-skills
cd agent-skills
./scripts/install.sh deeptalk --target ~/.claude/skills   # 复制安装
./scripts/install.sh --list                               # 看有哪些技能
```

`--target` 换成你自己的技能目录即可，例如 `~/.dsh/skills`、`~/.codex/skills`。
加 `--link` 改成软链接安装，`git pull` 后立即生效。

### 方式三：手动放

把 `skills/deeptalk/` 整个目录拷进你的技能目录，保持内部结构不变：

```text
<你的技能目录>/deeptalk/
├── SKILL.md                    # 入口，必须
├── references/mentor-protocol.md
└── agents/openai.yaml          # 可选，给 Codex/OpenAI 侧用的界面元数据
```

### 方式四：只用提示词

不想装技能，把 `skills/deeptalk/SKILL.md` 的内容直接贴进系统提示词里也能用，只是 `references/` 不会被自动加载 —— 那样会丢掉相当一部分细节。

## 新增一个技能

```bash
cp -R skills/_template skills/my-new-skill
# 改 skills/my-new-skill/SKILL.md 的 name（必须与目录名一致）与 description
./scripts/validate.sh
```

`_template` 目录不会被当成真技能安装（名字以下划线开头），但会被校验，可以当作格式参考。

新增后记得把它加进 `.claude-plugin/marketplace.json` 的 `skills` 数组 —— `./scripts/validate-marketplace.sh` 会检查两边是否一致，漏了会报错。

## 技能怎么用

技能是**按需加载**的：agent 只在你的请求命中 `SKILL.md` frontmatter 里的 `description` 时才读它。所以描述写的是「什么时候用」，不是「它是什么」。

以 `deeptalk` 为例，这些说法会命中它：

- 「我有个想法，帮我理一理」
- 丢一段 bullet points 或草稿，说「这样够了吗 / 还缺什么」
- 「帮我把这个需求问透」

命中后它会先给一轮**即时补全**（不等完整访谈），每个新增点带一个轻标签：

| 标签 | 含义 |
| --- | --- |
| `补全` | 当前结果直接需要，且已通过复杂度准入检查 |
| `拆分` | 你已有的点可以拆成更可验收的子项 |
| `盲区候选` | 你还没提到、相关性待证明，但值得先记下来 |
| `待决定` | 存在真实取舍，必须由你来选 |
| `假设` | 为了继续讨论暂时采用，不是事实 |

**关键边界**：`盲区候选` 和 `补全` 都只是候选项，不等于你已批准的需求。技能不会静默扩大范围，也不会把你的建议当成结论。

完整协议见 [`skills/deeptalk/references/mentor-protocol.md`](skills/deeptalk/references/mentor-protocol.md)（216 行，含场景识别算法、依赖式问题轮次、跨领域示例）。

## 可选增强

`deeptalk` 在 **DeepSeek Harness** 上会优先调用原生的 `ask_user_question` 工具来收敛关键取舍（固定选项、推荐项置顶），这是它最顺的形态。其他运行时下它会自动回落为编号菜单文本模板，功能不缺失。

## 仓库结构

```text
agent-skills/
├── .claude-plugin/
│   └── marketplace.json   # Claude Code 插件市场清单（skills-only，指向 skills/）
├── skills/
│   └── deeptalk/          # 每个技能一个目录，目录名 = SKILL.md 里的 name
│       ├── SKILL.md
│       ├── references/
│       └── agents/
├── scripts/
│   ├── install.sh             # 安装技能到任意技能目录
│   ├── validate.sh            # 技能结构校验（CI 也在跑）
│   └── validate-marketplace.sh # 插件清单校验（CI 也在跑）
├── .github/workflows/validate.yml
├── LICENSE
└── README.md
```

## 校验

改完技能跑一下，CI 跑的是同一个脚本：

```bash
./scripts/validate.sh
./scripts/validate-marketplace.sh
```

`validate.sh` 检查：`SKILL.md` 存在且 frontmatter 合法、`name` 与目录名一致且符合命名规范、`description` 非空且不超 1024 字符、`SKILL.md` 里引用的相对路径文件真实存在、技能目录内没有 `.DS_Store` 或本地 git 嵌套。

`validate-marketplace.sh` 检查：清单 JSON 合法、marketplace 名不是 Claude Code 保留名、每个技能条目都真实存在且含 `SKILL.md`、`skills/` 下的真技能没有漏声明。

## 贡献

新增技能请照 `skills/deeptalk/` 的结构来：一个目录、一个入口 `SKILL.md`、细节放 `references/`。提交前跑 `./scripts/validate.sh`。

写技能本身的方法论不在本仓库范围内。

## License

[MIT](LICENSE) © 2026 plyflai
