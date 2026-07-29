# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> 把有用的问题一次问完，然后开始干活。

Grill Lite 是一个需要手动调用的 Codex 和 Claude Code skill。它会先看仓库，再把重要问题一次问完，并根据任务风险决定流程做到什么程度。原型、规格、任务单、TDD、调试、审查和验证都已经内置。

## 创作初衷

我做 Grill Lite，是因为见过编程助手在两个方向上走得太远。有时它会在大仓库里搜很久，选一条看起来合理的实现路径，做到一半才暴露假设，最后还得由我反复纠正。有时一个很明确的小改动，也会先经历访谈、设计文档、计划和多轮确认，迟迟不开始改代码。

我想要的方式更简单：

- 先看最可能相关的代码和文档。
- 如果我能直接提供路径或测试数据，就尽早问。
- 重要决策放在一条消息里，并给出推荐答案。
- 动手前说清楚会改什么、不会动什么。
- 高风险任务认真测试和审查，小改动不要套完整流程。
- 整个任务只用一种语言沟通。

## 与 `grill-me` 的区别

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) 适合把需求彻底问清楚：一次问一个问题，而且项目明确表示[不限制问题数量](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md)。

这种方式适合探索所有分支，放到日常开发里就容易累。比如“按照现有模式增加一个可选表单字段”，可能被拆成五轮：放在哪里？空值显示什么？失焦还是提交时校验？测试叫什么？是否复用现有组件？

这些答案大多可以从仓库里找到，或者以后很容易调整。Grill Lite 会先自己查，再把剩下的决策一次问完，并附上推荐答案。

## 与 Superpowers 的区别

Superpowers 的 [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) 在实现前提供一套完整设计流程：逐个提问、确认设计、编写规格，再产出详细计划。

我自己的使用经历里，一个小改动走完这些阶段花了约 30 分钟；一个大需求连续跑了约 12 小时。这只是个人记录，不是基准测试，但它让我很清楚地看到了流程成本。

TDD、系统化调试、代码审查和完成前验证仍然值得保留。Grill Lite 内置了这些做法的精简版本，并在当前任务里替代 Superpowers 的 `brainstorming` 和 `writing-plans`。安装时不需要 Superpowers。

## 工作方式

1. 用用户的语言回复。
2. 先看最可能相关的代码、文档和测试。
3. 如果用户给一个路径或测试数据能省下很久的搜索，就直接问。
4. 明确、低风险的改动直接做；更大或更危险的任务先集中问一次，并确认双方理解一致。
5. 只选择当前任务需要的能力：直接执行、原型、规格、任务单、TDD、调试、审查或验证。
6. 展示一份执行简报，然后开工，不再追加计划审批。

## 交付强度

| 强度 | 什么时候用 | 怎么做 |
| --- | --- | --- |
| 轻量 | 改动明确、局部、可逆、低风险，并能在一个会话完成 | 直接修改，运行聚焦测试并验证结果 |
| 结构化 | 跨层修改、需要原型或交接、要分多次完成，或者没有现成模式 | 只补真正有用的原型、规格或任务单 |
| 严谨 | 涉及安全、隐私、支付、破坏性数据改动、迁移、公共契约或高风险回滚 | 加强测试、审查、进度说明和验证 |

风险高于规模：一行鉴权改动也可能需要严谨交付，而大范围机械样式调整可能只需轻量或结构化交付。

## 检索与澄清

第一次澄清最多放三个产品或技术决策。询问代码路径、参考文档或测试 ID 不占这个额度，但前提是这条信息确实能节省时间，或者避免一次危险猜测。

普通的仓库检索仍由 Grill Lite 自己完成。它会先搜最明显的符号和文件名。大仓库里如果搜索卡住，它会说明已经查过什么，再问一个最有用的线索。用户不知道也没关系，它会继续查，不重复追问。

外部 API、导入、迁移、初始化和同步流程需要更直接的证据。Grill Lite 会先要一个安全的测试输入和一条权威参考流程，再判断字段映射或生命周期行为。如果两轮聚焦检索或大约两分钟都没有进展，它会展示一份简短的“发现检查点”。

结构化和严谨任务会在编辑前展示一次“澄清简报”，即使当时没有完全卡住。用户可以在一条回复里回答问题、补充信息、纠正范围。确认发生在这里。后面的“执行简报”只说明准备怎么做，不再要求审批。

## 内置能力

| 能力 | 用在 | 做法 |
| --- | --- | --- |
| 直接执行 | 不需要以下任何产物 | 立即实现选定范围 |
| 原型 | 一个界面、交互、逻辑或状态问题需要运行证据 | 构建一次性产物、记录结论，并避免混入生产代码 |
| 规格 | 已明确需求需要持久交接 | 记录问题、目标、决策、验收标准、测试、边界和风险 |
| 任务单 | 工作包含独立交付切片或跨多个会话 | 按可验证的垂直切片拆分，并标明阻塞关系 |
| TDD | 修改可测试行为 | 确认失败、最小实现通过，再保持通过状态重构 |
| 系统化调试 | 出现失败或异常 | 复现、收集证据、验证单一根因假设并增加回归检查 |
| 代码审查 | 改动规模较大或风险较高 | 按严重程度检查需求、正确性、兼容性、风险和测试缺口 |
| 完成前验证 | 每次准备声明完成 | 运行最新证据、检查结果并报告残余风险 |

具体规则放在 [`references/`](references/) 中。Grill Lite 不会调用外部的 `prototype`、`to-spec`、`to-tickets` 或 Superpowers skill。

所有能力共用一份“执行简报”。只有新证据改变了执行路线时才会出现“能力更新”，例如测试意外失败并触发系统化调试。

## 安装

### Codex

使用 [skills.sh](https://skills.sh/) 安装：

```bash
npx skills@latest add RichieChoo/grill-lite
```

也可以安装到官方个人 skills 目录：

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.agents/skills/grill-lite
```

显式调用：

```text
$grill-lite 为现有项目增加团队邀请功能
```

[`agents/openai.yaml`](agents/openai.yaml) 默认关闭自动调用。在 Codex 中启用 skill 只代表它可用。如果希望 fork 后的版本自动匹配，把 `allow_implicit_invocation` 改成 `true`。

### Claude Code

Claude Code 需要一个很小的适配入口，因为 `disable-model-invocation` 是 Claude 专用元数据，Agent Skills 校验器不接受它。

```bash
mkdir -p ~/.local/share ~/.claude/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.local/share/grill-lite
mkdir -p ~/.claude/skills/grill-lite
ln -s ~/.local/share/grill-lite/adapters/claude-code.md ~/.claude/skills/grill-lite/SKILL.md
ln -s ~/.local/share/grill-lite/SKILL.md ~/.claude/skills/grill-lite/grill-lite.shared.md
```

显式调用：

```text
/grill-lite 为现有项目增加团队邀请功能
```

适配入口按照 [Claude Code Skills 官方文档](https://code.claude.com/docs/en/skills) 设置了 `disable-model-invocation: true`。它在源码中使用另一个文件名，避免 Codex 发现第二个 skill；安装命令再把共享流程链接到它旁边。

## 快速示例

下面是一项明确、低风险任务的执行简报：

```markdown
## 执行简报

交付强度：轻量
选择原因：字段、位置、空值行为和参考实现都已明确。
已理解：使用现有需求详情字段增加指定卡片。
所用假设：用户笔误指的是已有的下游依赖字段。
不会修改：不修改分析接口，也不从文档正文补推数据。
方案依据：现有字段类型、DetailCard 实现和聚焦渲染测试已经明确行为与测试入口。
能力安排：
- 直接执行：只修改目标卡片的渲染；空值和有值状态符合需求后退出。
- TDD：增加最小渲染测试并确认预期失败；新测试与聚焦测试集通过后退出。
- 完成前验证：运行聚焦测试和构建，并在浏览器检查空值和有值状态。
下一步：实现选定范围。
```

这份简报只用于说明情况。没有真正的决策或授权问题时，工作会马上继续。

## 语言一致性

Grill Lite 会使用用户请求中的语言。粘贴的代码、网址、路径和仓库内容不会影响判断；技术名称和已有界面文案保持原样。只有用户切换语言时，它才会跟着切换。

## 效果评测

[`evals/evals.json`](evals/evals.json) 覆盖语言选择、交付强度、批量提问、仓库检索、风险处理和每项内置能力。

它是一组测试，不是基准结果。需要对比时，在相同宿主和模型下开启新会话，每个用例分别运行一次原始版本和 Grill Lite 版本。发布数字时，同时附上模型、宿主、版本、样本数和原始记录。

## 兼容性

| 平台 | 手动调用 | 自动调用控制 |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | 带有 `disable-model-invocation: true` 的 Claude 适配入口 |
| 其他 Agent Skills 工具 | 取决于宿主 | 取决于宿主是否支持对应元数据 |

## License

[MIT](LICENSE)
