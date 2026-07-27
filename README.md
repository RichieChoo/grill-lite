# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> Ask only what blocks the work. Ask it once. Then move.

Grill Lite is a manually invoked skill for Codex and Claude Code. It keeps the useful part of requirements clarification, limits the interview, and routes settled work to the lightest useful next step.

## Motivation

AI coding benefits from clarification, but every reversible detail does not need to become a human approval checkpoint. Grill Lite is not against questions; it is against low-value interruptions.

- Inspect code and documentation before asking the user for facts.
- Ask only about decisions that materially change the result.
- Present all blocking decisions in one batch with recommended defaults.
- Infer reversible implementation details when the user leaves them open.
- Keep engineering discipline such as TDD, systematic debugging, code review, and verification.

## Compared with `grill-me`

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) deliberately explores every branch of a decision tree one question at a time. Its upstream project explicitly [declines to impose a question limit](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md). That depth is useful for exhaustive exploration, but routine changes can spend far too many turns on low-impact decisions.

Grill Lite keeps the useful interview behavior while imposing a small question budget, batching decisions, and supplying recommended defaults.

## Compared with Superpowers

Superpowers' [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) is a mandatory gate before creative work. It asks one question per turn, requests approval for design sections, writes and reviews a spec, then transitions through detailed planning before implementation.

Grill Lite provides a lighter entry point: it replaces `brainstorming` and `writing-plans` for the current request while retaining Superpowers' TDD, systematic debugging, review, and verification disciplines.

## How it works

1. Inspect the repository, configuration, and existing conventions.
2. Present at most three blocking decisions in one batch, each with a recommended option.
3. Do not ask another routine round. A single follow-up batch of at most two questions is allowed only for security, data loss, public contracts, irreversible migrations, or conflicting requirements.
4. Skip the separate "do we share an understanding?" approval gate.
5. Continue through the lightest useful route.

| Route | Use it when |
| --- | --- |
| Direct execution | The default; the work fits in the current session |
| `prototype` | UI, interaction, or state behavior is cheaper to validate with a runnable artifact |
| `to-spec` | Requirements are settled and deserve a durable specification |
| `to-tickets` | Work spans independent delivery slices or multiple sessions |

`prototype`, `to-spec`, and `to-tickets` are optional skills. When unavailable, Grill Lite follows the same intent directly instead of stopping on a missing dependency.

## Installation

### Codex

Install with [skills.sh](https://skills.sh/):

```bash
npx skills@latest add RichieChoo/grill-lite
```

Or install manually:

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.codex/skills/grill-lite
```

Invoke it with `$grill-lite`:

```text
$grill-lite Add team invitations to the existing application
```

Codex reads `allow_implicit_invocation: false` from [`agents/openai.yaml`](agents/openai.yaml), so the skill is not loaded automatically.

### Claude Code

Claude Code uses a small adapter because `disable-model-invocation` is a Claude-specific frontmatter field and is not accepted by Codex's strict Agent Skills validator.

```bash
mkdir -p ~/.local/share ~/.claude/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.local/share/grill-lite
ln -s ~/.local/share/grill-lite/.claude/skills/grill-lite ~/.claude/skills/grill-lite
```

Invoke it with `/grill-lite`:

```text
/grill-lite Add team invitations to the existing application
```

The adapter sets `disable-model-invocation: true`, following the [Claude Code Skills documentation](https://code.claude.com/docs/en/skills), so Claude cannot invoke it automatically.

## Superpowers integration

Grill Lite replaces only Superpowers' up-front interview and detailed planning gates. It continues to reuse execution-quality skills when they are available:

- `test-driven-development`
- `systematic-debugging`
- `requesting-code-review`
- `receiving-code-review`
- `verification-before-completion`

Claude Code plugins may expose these as `superpowers:<skill-name>`. Grill Lite resolves them by capability rather than requiring one fixed namespace.

If callable Superpowers skills are unavailable, Grill Lite applies the same discipline inline. It does not stop solely to ask the user to invoke `/debug`, `/code-review`, or `/verify`.

## Example

Grill Lite batches only decisions that genuinely block the work:

```markdown
## Decision Brief

Known or assumed:
- Continue using the existing authentication and email infrastructure.

Decisions needed:
1. When should an invitation expire?
   Recommended: A, balancing security with a normal collaboration window.
   Options: A) 7 days B) 24 hours C) Never

Reply with the option letters, corrections, or "use recommendations".
```

After `use recommendations`, it proceeds to a prototype, spec, tickets, or implementation without another routine approval step.

## Compatibility

| Host | Manual invocation | Automatic invocation control |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | Claude adapter with `disable-model-invocation: true` |
| Other Agent Skills hosts | Host-specific | Depends on host metadata support |

## License

[MIT](LICENSE)
