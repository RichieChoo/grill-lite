# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> Ask only what blocks the work. One batch by default. Then move.

Grill Lite is a manually invoked skill for Codex and Claude Code. It keeps requirements clarification bounded, then continues through direct execution or a lightweight prototype, spec, or ticket set.

## Motivation

AI coding benefits from clarification, but every reversible detail does not need to become a human approval checkpoint. Grill Lite is not against questions; it is against low-value interruptions.

- Inspect code and documentation before asking the user for facts.
- Ask only about decisions that materially change the result.
- Batch blocking decisions and provide recommended defaults.
- Infer reversible implementation details when the user leaves them open.
- Keep engineering discipline such as TDD, systematic debugging, code review, and verification.

## Compared with `grill-me`

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) deliberately explores every branch of a decision tree one question at a time. Its upstream project explicitly [declines to impose a question limit](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md). That depth is useful for exhaustive exploration, but routine changes can spend too many turns on low-impact decisions.

Grill Lite keeps the useful interview behavior while imposing a small question budget, batching decisions, and supplying recommended defaults.

## Compared with Superpowers

Superpowers' [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) uses a multi-stage design workflow before implementation, including sequential questions, design review, specification, and planning.

Grill Lite provides a lighter entry point: it replaces `brainstorming` and `writing-plans` for the current request while retaining Superpowers' TDD, systematic debugging, review, and verification disciplines.

## How it works

1. Inspect the repository, configuration, and existing conventions.
2. Count unresolved material decisions. If there are at most three, present them in one batch with recommendations.
3. If there are more than three, narrow the work to the smallest coherent slice and explicitly defer the rest. If no safe slice exists, show the three highest-impact decisions and stop after that decision pass.
4. Do not ask another routine round. One follow-up batch of at most two questions is allowed only when the user's answer creates a new blocker involving security, data loss, a public contract, an irreversible migration, or conflicting requirements.
5. Skip a separate understanding or plan approval gate and continue through the lightest useful route.

| Route | Use it when | Lightweight contract |
| --- | --- | --- |
| Direct execution | The selected scope fits the current session | Implement immediately |
| Lightweight prototype | One UI, interaction, logic, or state question is cheaper to test than discuss | Build the smallest throwaway artifact; no persistence, polish, tests, branches, commits, or tracker work by default |
| Lightweight spec | Settled requirements need a durable handoff | Write only the problem, outcome, decisions, acceptance criteria, testing, deferred scope, and open risks |
| Lightweight tickets | Work spans independent delivery slices or multiple sessions | Draft vertical slices with behavior, acceptance criteria, and blockers; no ticket approval interview or tracker publishing by default |

External `prototype`, `to-spec`, and `to-tickets` skills are optional implementations of these routes. They may be used only when they obey Grill Lite's question limits and lightweight output contract. When unavailable, Grill Lite performs the route directly.

## Installation

### Codex

Install with [skills.sh](https://skills.sh/):

```bash
npx skills@latest add RichieChoo/grill-lite
```

Or install manually in the official personal skills directory:

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.agents/skills/grill-lite
```

Invoke it with `$grill-lite`:

```text
$grill-lite Add team invitations to the existing application
```

Codex reads `allow_implicit_invocation: false` from [`agents/openai.yaml`](agents/openai.yaml), so Grill Lite is available only when selected explicitly.

### Claude Code

Claude Code uses a small adapter because `disable-model-invocation` is a Claude-specific frontmatter field and is not accepted by the strict Agent Skills validator.

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

Grill Lite replaces only Superpowers' up-front interview and detailed planning. It continues to reuse these execution-quality skills when available:

- `test-driven-development`
- `systematic-debugging`
- `requesting-code-review`
- `receiving-code-review`
- `verification-before-completion`

Claude Code plugins may expose these as `superpowers:<skill-name>`. Grill Lite resolves them by capability rather than requiring one namespace. If callable Superpowers skills are unavailable, it applies the same discipline inline instead of stopping to request a manual skill invocation.

## Example

Grill Lite offers only genuine choices. A decision may have two or three options, or ask for a short open answer:

```markdown
## Decision Brief

Known or assumed:
- Continue using the existing authentication and email infrastructure.

Decisions needed:
1. When should an invitation expire?
   Recommended: A, because seven days balances security and a normal collaboration window.
   Options: A) 7 days B) 24 hours, or reply with another duration.
   Consequence if deferred: invitation sending will not be implemented.

Reply with the option letter, a correction, a short answer, or "use recommendations".
```

After `use recommendations`, it applies defaults and continues without another routine approval step. The narrowly defined high-risk follow-up exception still applies.

## Evaluation

[`evals/evals.json`](evals/evals.json) contains structured behavioral cases for direct execution, batched decisions, repository-first discovery, scope overflow, the high-risk follow-up exception, and each lightweight route.

To assess effect, run every case in a fresh session with the same host and model, first without Grill Lite as a baseline and then with Grill Lite explicitly invoked. Record:

- question count and human turns before action;
- time to first implementation or artifact;
- factual questions that repository inspection could have answered;
- unsafe assumptions and omitted requirements;
- selected route, prohibited side effects, and acceptance-criteria coverage.

The eval file defines expected behavior and assertions; it is not a claim of benchmark results. Publish the model, host, version, sample count, and raw transcripts with any reported comparison.

## Compatibility

| Host | Manual invocation | Automatic invocation control |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | Claude adapter with `disable-model-invocation: true` |
| Other Agent Skills hosts | Host-specific | Depends on host metadata support |

## License

[MIT](LICENSE)
