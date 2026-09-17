# deeptalk

**把零散想法问透的技能。** 丢给它一段 bullet points、草稿或半句话，它先给一轮即时补全，再把真实取舍摆到你面前——不替你拍板。

这是 [plyflai/agent-skills](https://github.com/plyflai/agent-skills) 里的第一个技能。单个技能一个目录，`deeptalk` 就是它自己的名字，不是某个大类的下属。

---

## 它解决什么

大多数"帮我把需求写清楚"的工具会直接产出一份看起来很完整的文档。问题是那份文档里混着三样东西：你确实说过你要的、它替你假设的、以及它觉得你大概也想要的。**你没法区分，所以也没法验收。**

deeptalk 的做法是反过来：每个新增的点都带一个轻标签，说明它是哪一类。**关键边界**——`盲区候选` 和 `补全` 都只是候选项，不等于你已经批准了。它不会静默扩大范围，也不会把你的建议当成结论。

| 标签 | 含义 |
| --- | --- |
| `补全` | 当前结果直接需要，且已通过复杂度准入检查 |
| `拆分` | 你已有的点可以拆成更可验收的子项 |
| `盲区候选` | 你还没提到、相关性待证明，但值得先记下来 |
| `待决定` | 存在真实取舍，必须由你来选 |
| `假设` | 为了继续讨论暂时采用，不是事实 |

## 什么时候会命中它

技能是**按需加载**的：agent 只在你的请求命中 `SKILL.md` frontmatter 里的 `description` 时才读它。所以描述写的是「什么时候用」，不是「它是什么」。这些说法会命中：

- 「我有个想法，帮我理一理」
- 丢一段 bullet points 或草稿，说「这样够了吗 / 还缺什么」
- 「帮我把这个需求问透」

命中后它会先给一轮**即时补全**（不等完整访谈），再按依赖顺序问关键取舍——每轮最多 3 个真实取舍，你答完它重新计算，而不是提前问下游问题。

## 安装

不管哪个 harness，本质都是**把 `skills/deeptalk/` 这个目录放到它读技能的地方**。三种方式：

### 方式一：脚本装（任何 harness）

```bash
git clone https://github.com/plyflai/agent-skills
cd agent-skills
./scripts/install.sh deeptalk --target <你的技能目录>
```

`--link` 改成软链接安装（`git pull` 后立即生效），`--force` 覆盖已存在的同名技能：

```bash
./scripts/install.sh deeptalk --target <你的技能目录> --link
```

### 方式二：手动拷

把 `skills/deeptalk/` 整个目录拷进你的技能目录，保持内部结构：

```text
<你的技能目录>/deeptalk/
├── SKILL.md                    # 入口，必须
├── references/mentor-protocol.md
└── agents/openai.yaml          # 可选，Codex 侧的界面元数据
```

### 方式三：只用提示词

不装技能也行，把 `skills/deeptalk/SKILL.md` 的内容直接贴进系统提示词。只是 `references/` 不会被自动加载，会丢掉相当一部分细节。

## 装到哪

技能是按目录认的，惯例放这两处：

| | 目录 |
| --- | --- |
| 用户级 | `~/.agents/skills/deeptalk/`（你的机器，所有项目都能用） |
| 项目级 | `<仓库>/.agents/skills/deeptalk/`（跟着仓库走，写进 git 团队共用） |

`~/.agents/skills/` 是约定俗成的跨工具共用路径。也有 harness 只认自己的目录，那就再放一份：

| 举例 | 它的目录 |
| --- | --- |
| Codex | 读 `~/.agents/skills/` |
| Claude Code | `~/.claude/skills/`（项目内 `.claude/skills/`） |
| DSH | 读 `~/.agents/skills/`，也认 `~/.dsh/skills/` |

deeptalk **不依赖任何 harness 特有功能**，上面每一个都能跑。最省事的装法是共用路径：

```bash
./scripts/install.sh deeptalk --target ~/.agents/skills
```

各家自带的安装器也行：

```bash
# Codex
codex plugin marketplace add plyflai/agent-skills && codex plugin add deeptalk@plyflai-skills
# Claude Code
/plugin marketplace add plyflai/agent-skills && /plugin install deeptalk@plyflai-skills
```

装完不用重启，多数 harness 下次对话就会加载。

## 它依赖什么

**不依赖任何 harness 特有功能**，纯提示词协议。

唯一一处按环境分化：在 **DeepSeek Harness** 上它会优先调用原生 `ask_user_question` 工具来收敛关键取舍（固定选项、推荐项置顶），这是它最顺的形态；其他 harness 下自动回落为编号菜单文本模板，**功能不缺失**。

## 完整协议

[`references/mentor-protocol.md`](references/mentor-protocol.md)（216 行）包含：

- 表达形态镜像（容器 · 粒度 · 语气 · 语言四个维度）
- 场景识别与补全算法（6 类扫视 + 8 个领域视角 + 复杂度的「先发现，后准入」5 项检查）
- 标签与证据规则
- 依赖式问题轮次（为什么每轮最多 3 个）
- 停止条件
- 跨领域示例：结构化任务视图、课程设计、社区服务流程、研究问题

## 边界

- **它不是需求管理系统。** 产出是结构化的讨论结果，不是工单。想接 Jira/Linear 需要你自己接。
- **它不会替你做决定。** `待决定` 项永远留给你。
- **它不猜你的领域。** 8 个领域视角只是扫视角度，相关性需要你确认。

## License

[MIT](https://github.com/plyflai/agent-skills/blob/main/LICENSE) © 2026 plyflai
