# Grill Lite

[English](README.md) | [简体中文](README.zh-CN.md)

> Ask when it materially improves speed or accuracy. Batch decisions, then deliver with proportionate rigor.

Grill Lite is a manually invoked, self-contained skill for Codex and Claude Code. It locks onto the user's language, keeps clarification bounded, selects an explainable delivery profile, and completes the work without requiring other skills.

## Motivation

AI coding benefits from clarification, but every reversible detail does not need to become a human approval checkpoint. Grill Lite is not against questions; it is against low-value interruptions, wasteful repository archaeology, and invisible execution boundaries.

- Start with focused code and documentation inspection.
- Ask early when a user pointer is likely faster and more accurate than another broad search pass.
- Reserve decision questions for choices that materially change the result.
- Batch blocking decisions and provide recommended defaults.
- Show what will and will not change before editing.
- Match delivery rigor to risk, uncertainty, and coordination needs.
- Keep one working language throughout the task.

## Compared with `grill-me`

[`grill-me`](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) deliberately explores decision branches one question at a time. Its upstream project explicitly [declines to impose a question limit](https://github.com/mattpocock/skills/blob/main/.out-of-scope/question-limits.md). That depth is useful for exhaustive exploration, but routine changes can spend too many turns on low-impact decisions.

Grill Lite uses a small question budget, batches decisions, supplies recommended defaults, and proceeds when repository inspection resolves the uncertainty.

## Compared with Superpowers

Superpowers' [`brainstorming`](https://github.com/obra/superpowers/tree/main/skills/brainstorming) uses a multi-stage design workflow before implementation, including sequential questions, design review, specification, and planning.

Grill Lite replaces `brainstorming` and `writing-plans` for the current request. It independently implements compact TDD, systematic debugging, code review, and evidence-before-completion rules, so Superpowers is not an installation dependency.

## How it works

1. Lock the user's working language and run one focused discovery pass.
2. Ask for a repository pointer or test input when it is more valuable than another broad search pass.
3. Skip clarification for actionable Lightweight work; require one batched Clarification Brief for Structured and Rigorous work.
4. Let the user answer questions, add context, and confirm or correct the provisional understanding in one reply.
5. Finalize delivery rigor and select only the capabilities whose triggers are present.
6. Show one execution brief, then continue without a plan-approval gate. Announce only later capability changes.

## Delivery profiles

| Profile | Select it when | Default behavior |
| --- | --- | --- |
| Lightweight | Scope is clear, local, reversible, low-risk, and fits one session | Direct execution with focused testing and verification |
| Structured | The work has runnable uncertainty, cross-layer coordination, durable handoff, multiple slices, or no reusable pattern | Add only the needed prototype, specification, or tickets |
| Rigorous | Security, privacy, payment, destructive data work, migrations, public contracts, partial-failure risk, unsafe rollback, or explicit high assurance is involved | Apply stronger testing, review, progress visibility, and verification |

Risk overrides size: a one-line authorization change can be Rigorous, while a broad mechanical style update can remain Lightweight or Structured.

## Discovery and clarification

The three-question budget applies to product and technical decisions, not to concise requests for evidence or navigation. After one focused search pass, Grill Lite may ask where the authoritative module, document, example, owner, or prior change lives when that pointer is likely to avoid a broad search or a risky guess. It states what was already inspected and exactly what would help. If the user does not know, the agent resumes investigation and does not repeat the same question.

This exception is not permission to offload routine repository work. A direct symbol or filename search should still be performed by the agent, and Lightweight tasks remain self-service unless access or a material ambiguity blocks them.

For external APIs and operational initialization, import, migration, or synchronization flows, a representative non-sensitive test identifier or payload and an authoritative reference flow are treated as primary evidence. Grill Lite asks for them before inferring data mappings or lifecycle behavior from indirect code. If two focused passes or about two minutes do not establish that evidence, it shows a Discovery Checkpoint with what was inspected, what is confirmed, the remaining evidence gaps, the smallest useful user input, and its fallback if the user does not know.

For Structured and Rigorous work, Grill Lite does not wait for a hard blocker before speaking with the user. After focused discovery it runs one proactive Clarification Brief, surfacing provisional understanding and assumptions, asking the highest-value questions, inviting constraints, references, test data, concerns, and the user's own questions, and requesting confirmation or correction in the same reply. This prevents a plausible code path from becoming an unreviewed interpretation that the user must correct through repeated interruptions.

Confirmation belongs to clarification, not execution. Once the user confirms or corrects the understanding, Grill Lite proceeds without asking for plan approval. The execution brief exposes evidence, remaining gaps, protected scope, and capability exits. A material boundary change discovered later returns to clarification confirmation for only the changed parts.

## Built-in capabilities

| Capability | Trigger | Contract |
| --- | --- | --- |
| Direct execution | No artifact below is needed | Implement the selected scope immediately |
| Prototype | One UI, interaction, logic, or state question needs runnable evidence | Build one throwaway artifact, record the answer, and keep it out of production |
| Specification | Settled requirements need durable handoff | Capture the problem, outcome, decisions, acceptance criteria, testing, boundaries, and risks |
| Tickets | Work contains independent delivery slices or spans sessions | Draft verifiable vertical slices with blocking relationships |
| TDD | Testable behavior changes | Verify Red, implement minimal Green, then refactor while green |
| Systematic debugging | A failure or anomaly appears | Reproduce, gather evidence, test one root-cause hypothesis, and add a regression check |
| Review | A change is substantial or high-risk | Review requirements, correctness, compatibility, risks, and test gaps by severity |
| Verification | Every completion claim | Run fresh proof, inspect the result, and report residual risk |

These capabilities are bundled under [`references/`](references/). Grill Lite does not search for or invoke external `prototype`, `to-spec`, `to-tickets`, or Superpowers skills. The references are loaded only when their trigger applies.

Grill Lite uses one upfront execution brief. Its capability plan gives every selected capability a concise reason, objective, and exit condition. It does not print another brief when moving from direct execution to TDD, review, or verification. A separate Capability Update appears only when new evidence activates an unplanned capability, such as systematic debugging after an unexpected failure, or materially changes the original plan.

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

Codex reads `allow_implicit_invocation: false` from [`agents/openai.yaml`](agents/openai.yaml), so it does not invoke Grill Lite automatically. Skill enablement in Codex controls whether the skill is available; implicit invocation is a separate authoring policy. Set this field to `true` in a fork only when automatic matching is the intended default.

### Claude Code

Claude Code uses a small adapter because `disable-model-invocation` is Claude-specific metadata and is not accepted by the strict Agent Skills validator.

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

The adapter sets `disable-model-invocation: true`, following the [Claude Code Skills documentation](https://code.claude.com/docs/en/skills). Its source filename is deliberately not `SKILL.md`, preventing Codex from discovering it as a second skill inside the package. The second link exposes the shared workflow beside the installed adapter without creating another discoverable skill.

## Example

A task with no blocking decision still exposes its execution boundary before editing:

```markdown
No blocking decisions found after inspecting the repository and reference page.

## Execution Brief

Profile: Lightweight
Reason: The fields, placement, empty-state behavior, and reference implementation are known.
Understood: Add the requested cards using the existing requirement-detail fields.
Assumed: The misspelled label refers to the existing downstream-dependency field.
Will not change: The analysis API or fallback inference from document text.
Deferred: Prototype, specification, and tickets because no runnable uncertainty or durable handoff exists.
Evidence: The existing field type, DetailCard implementation, and focused rendering tests establish the behavior and test seam.
Capability plan:
- Direct execution: Change only the requested card rendering; finish when empty and populated states match the requirement.
- TDD: Add the smallest rendering test and confirm the expected Red signal; finish when it passes with the focused suite.
- Verification: Run focused tests and the build, then inspect empty and populated states in the browser.
Next: Implement the selected scope.
```

The brief is not an approval prompt. The agent continues in the same turn unless a material decision or required authorization blocks it.

## Language consistency

Grill Lite infers the language from the user's request, ignoring pasted code, URLs, paths, identifiers, and quoted repository content. It localizes workflow headings, profile names, questions, specifications, tickets, updates, and final responses. Exact technical identifiers and project-owned UI copy remain unchanged. It switches only when the user explicitly requests another language or clearly changes the conversation language.

## Evaluation

[`evals/evals.json`](evals/evals.json) contains structured behavioral cases for language consistency, delivery profiles, direct execution, batched decisions, repository-first discovery, scope overflow, high-risk follow-up, and every built-in capability route.

Run each case in a fresh session with the same host and model, first without Grill Lite and then with explicit invocation. Record question count, human turns, time to first action, avoidable factual questions, unsafe assumptions, language violations, profile accuracy, selected capabilities, prohibited side effects, and acceptance-criteria coverage.

The eval file defines expected behavior and assertions; it is not a claim of benchmark results. Publish the model, host, version, sample count, and raw transcripts with any reported comparison.

## Compatibility

| Host | Manual invocation | Automatic invocation control |
| --- | --- | --- |
| Codex | `$grill-lite` | `agents/openai.yaml` |
| Claude Code | `/grill-lite` | Claude adapter with `disable-model-invocation: true` |
| Other Agent Skills hosts | Host-specific | Depends on host metadata support |

## License

[MIT](LICENSE)
