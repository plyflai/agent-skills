---
name: deeptalk
description: 即时把零散想法、bullet points、草稿或问题深化为结构化需求，并从当前领域、参与者、流程和约束中补出高信号的 unknown unknowns、拆分项和待决定事项。Use this skill when the user wants a mentor-like second perspective for a product, service, policy, workflow, research question, education plan, creative project, personal decision, or software idea; it is domain-agnostic and can hand results to any downstream method.
---

# deeptalk

> 先把用户想解决的问题变清楚，再用上下文相关的视角补足它；建议保持可见，决定留给用户。

## 激活标识

进入 skill 时输出：`🧠 deeptalk 已激活`

## 默认目标

- 对用户刚说出的点立即给出一轮高信号补全，不等完整访谈。
- 把用户目标、当前方案、Agent 推断、候选盲区和待拍板事项分开。
- 保留用户原编号，并在有依据时展开为 `2.1 / 2.2` 等子项。
- 优先镜像用户的表达形式、语言、粒度和语气；只在可读性或下游契约需要时做最小结构化。
- 每轮只补最可能改变结果、范围、规则或验收的点；不制造百科式清单。
- 当关键取舍彼此依赖时，按依赖顺序逐轮提出当前可回答的 1-3 个问题；不提前询问需要猜测前提的下游问题。

## 读取顺序

1. 读取 [mentor-protocol.md](references/mentor-protocol.md)。
2. 先识别当前场景的对象、参与者、目标、流程、环境和约束，再选择相关的领域视角；不要预设用户一定在做软件或产品。
3. 若存在下游工作流，按 reference 末尾的适配规则交接；下游系统的门禁、格式和批准状态仍由下游自己负责。

## 全局边界

- 建议是候选项，不是用户已批准的需求；不得静默扩大范围或替用户拍板。
- unknown unknowns 只能作为有理由的候选盲区呈现，不能声称穷尽未知。
- 只缺一个只有用户或相关责任人才能确认的关键事实时直接问；可从现有材料或工具查到的事实先自行调查。存在多个真实取舍时，优先用 DSH 原生 `ask_user_question`（固定选项）；该工具不可用时回落到编号菜单。
- 当新增点开始重复、只剩低影响细节，或用户已能进入下游确认门时停止补全。
- 依赖式问题轮次只为关闭会影响当前结果的关键取舍服务；不把它变成“所有潜在分支都问完”的完整访谈。

等待用户选择时，优先调用 DSH 原生 `ask_user_question`：每题一个稳定 `id` 且只问一个决定，选项固定，推荐项放首位并在标签后加 `(Recommended)`；用户显式指定其他形式时以用户为准。该工具不可用（非 DSH 运行时、无应答者或调用失败）时回落到编号菜单：

```text
📍 当前: ...
📌 请选择:
[1] ...
[2] ...
[0] ...
```

完整模板见 reference。
