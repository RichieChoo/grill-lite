# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> 只问真正阻塞执行的问题，一次问完，然后开工。

Grill Lite 是一个面向 Codex 和 Claude Code 的手动触发 skill。它保留需求澄清的价值，但限制访谈轮次，并在需求足够明确后自动选择最轻量的后续路径。

## 创作初衷

AI 编程协作需要澄清，但不需要把每个可逆细节都变成一次人工审批。Grill Lite 不是拒绝提问，而是减少低价值打断。

- 先读代码和文档，能查到的事实不问用户。
- 只询问会实质改变结果的决策。
- 一次批量展示问题，并给出推荐默认值。
- 对未回答的可逆细节自行做合理假设。
- 保留 TDD、系统化调试、代码审查和完成前验证等工程纪律。

## 与 `grill-me` 的区别

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) 会逐个问题遍历决策树的全部分支，其上游项目也明确表示[不设置问题数量上限](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md)。这种深度适合穷尽式探索，但日常改动可能在低影响决策上消耗过多轮次。

Grill Lite 保留访谈的价值，同时限制问题数量、批量展示决策，并提供推荐默认值。

## 与 Superpowers 的区别

Superpowers 的 [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) 是所有创作工作的强制入口。它逐个提问、分段确认设计、编写并复核 Spec，然后经过详细计划才进入实现。

Grill Lite 提供更轻的入口：在当前任务中替代 `brainstorming` 和 `writing-plans`，同时保留 Superpowers 的 TDD、系统化调试、审查和完成前验证纪律。

## 工作方式

1. 检查仓库、配置和已有约定。
2. 一次展示最多 3 个阻塞决策，每个决策都提供推荐选项。
3. 默认不再追问。只有安全、数据损失、公共契约、不可逆迁移或需求冲突可以追加一轮，最多 2 个问题。
4. 不增加单独的“是否确认理解一致”审批环节。
5. 自动进入最轻量的后续路径。

| 路径 | 适用场景 |
| --- | --- |
| 直接执行 | 默认选择；工作可在当前会话完成 |
| `prototype` | UI、交互或状态模型需要通过可运行产物验证 |
| `to-spec` | 需求已明确，但需要形成持久 Spec |
| `to-tickets` | 工作跨多个独立交付切片或多个会话 |

`prototype`、`to-spec` 和 `to-tickets` 是可选 skill。没有安装时，Grill Lite 会直接遵循相同意图，不会因为缺少依赖而停止。

## 安装

### Codex

使用 [skills.sh](https://skills.sh/) 安装：

```bash
npx skills@latest add RichieChoo/grill-lite
```

也可以手动安装：

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.codex/skills/grill-lite
```

通过 `$grill-lite` 调用：

```text
$grill-lite 为现有项目增加团队邀请功能
```

Codex 使用 [`agents/openai.yaml`](agents/openai.yaml) 中的 `allow_implicit_invocation: false`，因此不会自动加载该 skill。

### Claude Code

Claude Code 使用一个很小的适配入口，因为 `disable-model-invocation` 是 Claude 专用 frontmatter 字段，Codex 的严格 Agent Skills 校验器不接受它。

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

Grill Lite 只替换 Superpowers 的前置需求访谈和详细计划环节，不替换执行质量控制。安装 Superpowers 后，它会按需复用：

- `test-driven-development`
- `systematic-debugging`
- `requesting-code-review`
- `receiving-code-review`
- `verification-before-completion`

Claude Code 插件可能把这些 skill 暴露为 `superpowers:<skill-name>`；Grill Lite 会按能力匹配，不依赖固定命名空间。

在没有可调用的 Superpowers skill 时，Grill Lite 会直接应用相同纪律，不会仅仅为了要求用户手动运行 `/debug`、`/code-review` 或 `/verify` 而停止。

## 快速示例

Grill Lite 只会批量展示真正阻塞的决策：

```markdown
## Decision Brief

Known or assumed:
- 继续使用当前认证和邮件发送基础设施。

Decisions needed:
1. 邀请链接何时失效？
   Recommended: A，兼顾安全和常见协作周期。
   Options: A) 7 天 B) 24 小时 C) 永不过期

Reply with the option letters, corrections, or "use recommendations".
```

回复 `use recommendations` 后，它会直接进入原型、Spec、拆票或实现，不再增加常规确认环节。

## 兼容性

| 平台 | 手动调用 | 禁止自动调用 |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | 带有 `disable-model-invocation: true` 的 Claude 适配入口 |
| 其他 Agent Skills 工具 | 取决于宿主 | 取决于宿主是否支持对应元数据 |

## License

[MIT](LICENSE)
