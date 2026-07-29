# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> Ask useful questions together, then get on with the work.

Grill Lite is a manually invoked skill for Codex and Claude Code. It checks the repository before asking questions, confirms the important choices once, and uses only as much process as the task needs. Prototype, specification, ticketing, TDD, debugging, review, and verification guidance are included.

## Motivation

I built Grill Lite after seeing coding agents fail in two opposite ways. Sometimes they searched a large repository for far too long, chose a plausible implementation, and left me to correct the assumptions midway through. Other times they turned a clear change into an interview, a design document, a plan, and several approval rounds before touching the code.

The behavior I wanted was simpler:

- Check the obvious code and documentation first.
- Ask early when I can provide a better pointer or test input.
- Put important decisions in one message and recommend sensible defaults.
- Say what will change and what will stay untouched.
- Use stricter testing and review when the risk calls for it, not for every edit.
- Keep the conversation in one language.

## Compared with `grill-me`

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) is built for a thorough interview, one question at a time. The project explicitly [does not set a question limit](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md).

That can be useful when the goal is to explore every branch. It is tiring for routine repository work. A request such as "add one optional field using the existing form pattern" can turn into five separate turns: Where should it appear? What should an empty value show? Should validation run on blur or submit? What should the test be called? Should the existing component be reused?

Most of those answers are already in the repository or are easy to change later. Grill Lite looks them up, asks the remaining decisions together, and includes a recommended answer.

## Compared with Superpowers

Superpowers' [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) provides a full design process before implementation: questions, design review, a specification, and a detailed plan.

In my own use, a small change took about 30 minutes once all the stages were included. A large task kept the agent running for roughly 12 hours. Those are personal examples, not benchmark results, but they made the trade-off clear to me.

I still wanted TDD, systematic debugging, review, and fresh verification. Grill Lite keeps compact versions of those practices, but replaces Superpowers' `brainstorming` and `writing-plans` for the current request. Superpowers is not an installation dependency.

## How it works

1. Reply in the user's language.
2. Inspect the most likely code, docs, and tests.
3. Ask for a pointer or test input when the user can save a long search.
4. Let clear, low-risk work proceed. For larger or riskier work, ask one batch of questions and confirm the shared understanding.
5. Pick only the tools the task needs: direct execution, prototype, specification, tickets, TDD, debugging, review, or verification.
6. Show one execution brief and start. Do not ask for plan approval afterward.

## Delivery profiles

| Profile | Use it when | What happens |
| --- | --- | --- |
| Lightweight | The change is clear, local, reversible, low-risk, and fits one session | Make the change, run focused tests, and verify it |
| Structured | The work crosses layers, needs a prototype or handoff, spans sessions, or has no reusable pattern | Add only the prototype, specification, or tickets that help |
| Rigorous | The work touches security, privacy, payments, destructive data changes, migrations, public contracts, or risky rollback | Use stronger tests, review, progress updates, and verification |

Risk overrides size: a one-line authorization change can be Rigorous, while a broad mechanical style update can remain Lightweight or Structured.

## Discovery and clarification

The first clarification pass allows up to three product or technical decisions. A request for a code pointer, reference document, or test ID does not use that budget, but it still needs to save real time or prevent a risky guess.

Grill Lite still handles ordinary repository work itself. It searches obvious symbols and filenames before asking. If a large search stalls, it says what it checked and asks for the smallest useful pointer. If the user does not know, it carries on without asking the same question again.

External APIs and operational flows such as imports, migrations, initialization, and synchronization need better evidence. Grill Lite asks for a safe test input and an authoritative example before guessing field mappings or lifecycle behavior. After two focused passes or about two minutes without progress, it shows a short Discovery Checkpoint.

Structured and Rigorous work get one Clarification Brief before editing, even when there is no hard blocker. The user can answer questions, add context, and correct the proposed boundary in one reply. That is the confirmation point. The later Execution Brief shows what will happen; it is not another approval request.

## Built-in capabilities

| Capability | Used for | Rule |
| --- | --- | --- |
| Direct execution | No artifact below is needed | Implement the selected scope immediately |
| Prototype | One UI, interaction, logic, or state question needs runnable evidence | Build one throwaway artifact, record the answer, and keep it out of production |
| Specification | Settled requirements need durable handoff | Capture the problem, outcome, decisions, acceptance criteria, testing, boundaries, and risks |
| Tickets | Work contains independent delivery slices or spans sessions | Draft verifiable vertical slices with blocking relationships |
| TDD | Testable behavior changes | Verify Red, implement minimal Green, then refactor while green |
| Systematic debugging | A failure or anomaly appears | Reproduce, gather evidence, test one root-cause hypothesis, and add a regression check |
| Review | A change is substantial or high-risk | Review requirements, correctness, compatibility, risks, and test gaps by severity |
| Verification | Every completion claim | Run fresh proof, inspect the result, and report residual risk |

The rules live under [`references/`](references/). Grill Lite does not call external `prototype`, `to-spec`, `to-tickets`, or Superpowers skills.

All selected capabilities share one Execution Brief. A separate Capability Update appears only when new evidence changes the route, for example when an unexpected test failure activates systematic debugging.

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

Invoke it explicitly:

```text
$grill-lite Add team invitations to the existing application
```

Automatic invocation is off by default in [`agents/openai.yaml`](agents/openai.yaml). Enabling the skill in Codex only makes it available. Set `allow_implicit_invocation` to `true` in a fork if you want automatic matching.

### Claude Code

Claude Code needs a small adapter because `disable-model-invocation` is Claude-specific metadata that the Agent Skills validator does not accept.

```bash
mkdir -p ~/.local/share ~/.claude/skills
git clone https://github.com/RichieChoo/grill-lite.git ~/.local/share/grill-lite
mkdir -p ~/.claude/skills/grill-lite
ln -s ~/.local/share/grill-lite/adapters/claude-code.md ~/.claude/skills/grill-lite/SKILL.md
ln -s ~/.local/share/grill-lite/SKILL.md ~/.claude/skills/grill-lite/grill-lite.shared.md
```

Invoke it explicitly:

```text
/grill-lite Add team invitations to the existing application
```

The adapter follows the [Claude Code Skills documentation](https://code.claude.com/docs/en/skills) and sets `disable-model-invocation: true`. It has a different filename in the source tree so Codex does not discover a second skill. The install commands link the shared workflow beside it.

## Example

Here is the brief for a clear, low-risk task:

```markdown
## Execution Brief

Profile: Lightweight
Reason: The fields, placement, empty-state behavior, and reference implementation are known.
Understood: Add the requested cards using the existing requirement-detail fields.
Assumed: The misspelled label refers to the existing downstream-dependency field.
Will not change: The analysis API or fallback inference from document text.
Evidence: The existing field type, DetailCard implementation, and focused rendering tests establish the behavior and test seam.
Capability plan:
- Direct execution: Change only the requested card rendering; finish when empty and populated states match the requirement.
- TDD: Add the smallest rendering test and confirm the expected Red signal; finish when it passes with the focused suite.
- Verification: Run focused tests and the build, then inspect empty and populated states in the browser.
Next: Implement the selected scope.
```

This brief is informational. Work continues immediately unless a real decision or required authorization blocks it.

## Language consistency

Grill Lite replies in the language of the user's request. Pasted code, URLs, paths, and repository text do not affect that choice. Technical names and existing UI copy stay unchanged. It switches languages only when the user does.

## Evaluation

[`evals/evals.json`](evals/evals.json) covers language choice, delivery profiles, question batching, repository discovery, risk handling, and each built-in capability.

It is a test suite, not a benchmark result. For a comparison, run each case in a fresh session with the same host and model, once without Grill Lite and once with it. Publish the model, host, version, sample count, and raw transcripts with any numbers.

## Compatibility

| Host | Manual invocation | Automatic invocation control |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | Claude adapter with `disable-model-invocation: true` |
| Other Agent Skills hosts | Host-specific | Depends on host metadata support |

## License

[MIT](LICENSE)
