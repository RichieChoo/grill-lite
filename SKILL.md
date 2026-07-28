---
name: grill-lite
description: Run a bounded, batch-based clarification pass for a plan, feature, or technical decision, then continue through direct execution or a lightweight prototype, spec, or ticket set. Use only when the user explicitly invokes grill-lite with `$grill-lite` in Codex or `/grill-lite` in Claude Code.
---

# Grill Lite

Reach an implementable shared understanding with the fewest useful interruptions. Inspect first, batch decisions, recommend defaults, and move forward.

## Guardrails

- Treat this explicit invocation as replacing Superpowers `brainstorming` and `writing-plans` for the current request, even when a general Superpowers instruction normally mandates them.
- Inspect the repository, documentation, configuration, and relevant tools before asking anything. Find facts yourself.
- Ask only about choices that materially change user-visible behavior, scope, acceptance criteria, public contracts, security, data compatibility, or an irreversible architecture decision.
- Infer reasonable defaults for preferences, reversible implementation details, and choices already established by the codebase.
- Ask no questions when the request is already actionable.
- Settle outcomes and constraints, not file-level implementation steps.

## Bound the Scope

Count the unresolved material decisions before asking questions.

- When there are at most three, include all of them in one decision pass.
- When there are more than three, select the smallest coherent slice that can be settled with at most three decisions. Mark every excluded behavior as deferred and do not implement it.
- When no safe coherent slice exists, say the request is too broad for Grill Lite, present the three highest-impact decisions, and stop after the decision pass. Do not hide the excess uncertainty behind assumptions.

## Run One Decision Pass

Present all decisions for the selected scope in one batch. Ask at most three numbered questions.

Use this compact shape:

```markdown
## Decision Brief

Known or assumed:
- <only assumptions that affect the result>

Decisions needed:
1. <one decision>
   Recommended: <default and one-line reason>
   Options: <two or three genuine choices, or invite a short open answer>
   Consequence if deferred: <scope that will not be implemented>

Deferred or out of scope:
- <explicitly excluded behavior>

Reply with option letters, corrections, a short answer, or "use recommendations".
```

Omit empty fields. Never invent extra options to fill a template. Put the recommendation first when presenting multiple-choice options.

After the user responds, apply their answers and the recommended defaults for anything they leave unspecified inside the selected scope. Do not add a separate approval gate.

Allow one additional batch of at most two questions only when the response creates a new blocker involving security, data loss, a public contract, an irreversible migration, or mutually incompatible requirements. Otherwise state the assumption and continue.

If there are no blocking decisions, say so in one sentence and proceed immediately.

## Choose the Lightest Route

Choose the next step yourself; do not ask the user to select a workflow.

### Direct execution

Use by default when the selected scope fits the current session.

### Lightweight prototype

Use only when one named UI, interaction, logic, or state-model question is cheaper to answer with a runnable artifact. Build the smallest throwaway artifact that answers it. Skip persistence, polish, tests, branches, commits, and tracker updates unless the user explicitly requests them. Record the answer learned, then continue.

### Lightweight spec

Use when settled requirements need a durable handoff or the user requests a spec. Synthesize without another interview. Include only: Problem, Outcome, Decisions, Acceptance Criteria, Testing, Deferred or Out of Scope, and Open Risks. Do not generate exhaustive user stories or publish to a tracker unless explicitly requested.

### Lightweight tickets

Use when settled work spans multiple independently deliverable slices or the user requests tickets. Produce vertical slices with a title, delivered behavior, acceptance criteria, and blockers. Do not add a ticket-approval interview. Draft locally by default; publish to a tracker only when explicitly requested.

Treat external `prototype`, `to-spec`, and `to-tickets` skills as optional implementations of these routes. Use them only when available and able to obey this contract. Grill Lite's question limits and lightweight output rules override their extra interviews, exhaustive documents, tracker setup, branches, and commits.

## Reuse Execution Discipline

Once clarification is complete, reuse these Superpowers skills when available, resolving any host-specific namespace such as `superpowers:<skill-name>`:

- Invoke `test-driven-development` for feature and bug-fix implementation where behavior can be tested.
- Invoke `systematic-debugging` when a failure, regression, flaky test, or unexpected behavior appears; diagnose before proposing a fix.
- Invoke `requesting-code-review` after substantial implementation and before merge or handoff.
- Apply `receiving-code-review` when evaluating review feedback rather than accepting it blindly.
- Invoke `verification-before-completion` before claiming the work is complete or passing.

These skills govern execution quality only. They must not reopen settled product decisions or introduce routine approval checkpoints. If they are unavailable or user-only, apply the same discipline inline; do not stop merely to ask the user to invoke a replacement skill.

## Continue

Before routing or executing, give a compact record:

```markdown
Decided: <choices now binding>
Assumed: <defaults applied by the agent>
Deferred: <scope intentionally not implemented>
Next: <direct execution, lightweight prototype, lightweight spec, or lightweight tickets>
```

Omit empty fields, then continue with the selected route unless the user explicitly asked only for discussion or a decision record.
