# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> 只问真正阻塞执行的问题。默认一次批量问完，然后开工。

Grill Lite 是一个面向 Codex 和 Claude Code 的手动触发 skill。它限制需求澄清的范围和轮次，然后直接执行，或生成轻量原型、Spec、Tickets。

## 创作初衷

AI 编程协作需要澄清，但不需要把每个可逆细节都变成一次人工审批。Grill Lite 不是拒绝提问，而是减少低价值打断。

- 先读代码和文档，能查到的事实不问用户。
- 只询问会实质改变结果的决策。
- 批量展示阻塞决策，并提供推荐默认值。
- 对未回答的可逆细节自行做合理假设。
- 保留 TDD、系统化调试、代码审查和完成前验证等工程纪律。

## 与 `grill-me` 的区别

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) 会逐个问题遍历决策树的全部分支，其上游项目也明确表示[不设置问题数量上限](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md)。这种深度适合穷尽式探索，但日常改动可能在低影响决策上消耗过多轮次。

Grill Lite 保留访谈的价值，同时限制问题数量、批量展示决策，并提供推荐默认值。

## 与 Superpowers 的区别

Superpowers 的 [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) 在实现前使用多阶段设计流程，包括逐个提问、设计确认、编写 Spec 和详细计划。

Grill Lite 提供更轻的入口：在当前任务中替代 `brainstorming` 和 `writing-plans`，同时保留 Superpowers 的 TDD、系统化调试、审查和完成前验证纪律。

## 工作方式

1. 检查仓库、配置和已有约定。
2. 统计尚未解决的实质决策。不超过 3 个时，一次批量展示并给出推荐。
3. 超过 3 个时，把工作缩小为最多需要 3 个决策的最小完整切片，并明确推迟其余范围。如果无法得到安全、完整的切片，只展示影响最大的 3 个决策，并在本轮决策后停止。
4. 默认不再追问。只有用户回答产生了涉及安全、数据损失、公共契约、不可逆迁移或需求冲突的新阻塞项，才允许追加一轮，且最多 2 个问题。
5. 不增加单独的理解确认或计划审批环节，自动进入最轻量的后续路径。

| 路径 | 适用场景 | 轻量契约 |
| --- | --- | --- |
| 直接执行 | 当前选定范围可在本会话完成 | 立即实现 |
| 轻量原型 | 一个 UI、交互、逻辑或状态问题用运行结果验证更省成本 | 只构建回答问题所需的最小一次性产物；默认不做持久化、打磨、测试、分支、提交或 Tracker 操作 |
| 轻量 Spec | 已明确的需求需要持久交接 | 只包含问题、目标、决策、验收标准、测试、推迟范围和开放风险 |
| 轻量 Tickets | 工作跨多个独立交付切片或多个会话 | 按垂直切片记录行为、验收标准和阻塞项；默认不追加审批访谈，也不发布到 Tracker |

外部的 `prototype`、`to-spec` 和 `to-tickets` skill 只是这些路径的可选实现。只有在它们遵守 Grill Lite 的问题上限和轻量输出契约时才会使用；未安装时，Grill Lite 会直接执行相同路径。

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

通过 `$grill-lite` 调用：

```text
$grill-lite 为现有项目增加团队邀请功能
```

Codex 使用 [`agents/openai.yaml`](agents/openai.yaml) 中的 `allow_implicit_invocation: false`，因此只有显式选择时才会使用 Grill Lite。

### Claude Code

Claude Code 使用一个很小的适配入口，因为 `disable-model-invocation` 是 Claude 专用 frontmatter 字段，严格的 Agent Skills 校验器不接受它。

```bash
mkdir -p ~/.local/share ~/.claude/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.local/share/grill-lite
ln -s ~/.local/share/grill-lite/.claude/skills/grill-lite ~/.claude/skills/grill-lite
```

通过 `/grill-lite` 调用：

```text
/grill-lite 为现有项目增加团队邀请功能
```

适配入口设置了 `disable-model-invocation: true`，遵循 [Claude Code Skills 官方文档](https://code.claude.com/docs/en/skills)，因此 Claude 无法自动调用它。

## 与 Superpowers 配合

Grill Lite 只替换 Superpowers 的前置需求访谈和详细计划。它会在可用时继续复用以下执行质量 skill：

- `test-driven-development`
- `systematic-debugging`
- `requesting-code-review`
- `receiving-code-review`
- `verification-before-completion`

Claude Code 插件可能把这些 skill 暴露为 `superpowers:<skill-name>`。Grill Lite 会按能力匹配，不依赖固定命名空间。如果没有可调用的 Superpowers skill，它会直接应用相同纪律，而不会停止并要求用户手动调用替代 skill。

## 快速示例

Grill Lite 只提供真实存在的选择。一个决策可以有 2 至 3 个选项，也可以要求简短的开放回答：

```markdown
## Decision Brief

Known or assumed:
- 继续使用当前认证和邮件发送基础设施。

Decisions needed:
1. 邀请链接何时失效？
   Recommended: A，7 天兼顾安全和常见协作周期。
   Options: A) 7 天 B) 24 小时，或直接回复其他时长。
   Consequence if deferred: 暂不实现邀请发送。

Reply with the option letter, a correction, a short answer, or "use recommendations".
```

回复 `use recommendations` 后，它会应用默认值并继续，不增加常规审批环节。前述范围严格的高风险追问例外仍然适用。

## 效果评测

[`evals/evals.json`](evals/evals.json) 提供结构化行为用例，覆盖直接执行、批量决策、先查仓库、范围溢出、高风险追问例外，以及三种轻量路径。

评测时，对每个用例使用相同宿主和模型开启全新会话：先不使用 Grill Lite 取得基线，再显式调用 Grill Lite。记录：

- 执行前的问题数量和人工交互轮数；
- 开始实现或生成产物所需时间；
- 本可通过检查仓库回答、却错误询问用户的事实问题；
- 不安全的假设和遗漏的需求；
- 选择的路径、被禁止的副作用和验收标准覆盖率。

评测文件定义的是预期行为和断言，不代表已经取得基准测试结果。发布对比结果时，应同时给出模型、宿主、版本、样本数和原始对话记录。

## 兼容性

| 平台 | 手动调用 | 禁止自动调用 |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | 带有 `disable-model-invocation: true` 的 Claude 适配入口 |
| 其他 Agent Skills 工具 | 取决于宿主 | 取决于宿主是否支持对应元数据 |

## License

[MIT](LICENSE)
