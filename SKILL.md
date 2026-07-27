---
name: grill-lite
description: Run a bounded, batch-based clarification pass for a plan, feature, or technical decision, then route the work to direct execution, a spec, a prototype, or tickets. Use only when the user explicitly invokes grill-lite with `$grill-lite` in Codex or `/grill-lite` in Claude Code.
---

# Grill Lite

Reach an implementable shared understanding with the fewest useful interruptions. Inspect first, batch decisions, recommend defaults, and move forward.

## Guardrails

- Treat invocation of this skill as the user's explicit replacement for Superpowers `brainstorming` for the current request, even if a general Superpowers instruction normally mandates it. Do not invoke `brainstorming` or `writing-plans` as part of this workflow.
- Inspect the repository, documentation, configuration, and relevant tools before asking anything. Find facts yourself.
- Ask only about choices that materially change user-visible behavior, scope, acceptance criteria, public contracts, security, data compatibility, or an irreversible architecture decision.
- Infer reasonable defaults for preferences, reversible implementation details, and choices already established by the codebase.
- Ask no questions when the request is already actionable.
- Do not turn clarification into implementation planning. Settle outcomes and constraints; leave file-level steps to execution.

## Run One Decision Pass

Present all currently blocking decisions in one batch. Ask at most three numbered questions.

Use this compact shape:

```markdown
## Decision Brief

Known or assumed:
- <only assumptions that affect the result>

Decisions needed:
1. <decision>
   Recommended: <default and one-line reason>
   Options: A) ... B) ... C) ...

Deferred or out of scope:
- <only meaningful boundaries>

Reply with the option letters, corrections, or "use recommendations".
```

Omit empty sections. Keep each question to one decision. Make the recommended option the first option.

After the user responds, apply the answers and recommended defaults for anything they leave unspecified. Do not add a separate approval gate.

Allow one additional batch of at most two questions only when the response creates a new blocker involving security, data loss, a public contract, an irreversible migration, or mutually incompatible requirements. Otherwise state the assumption and continue.

If there are no blocking decisions, say so in one sentence and proceed immediately.

## Choose the Lightest Route

Choose the next step yourself; do not ask the user to select a workflow.

- **Direct execution:** Default for small or medium work that fits one session.
- **Prototype:** Invoke `prototype` when a runnable artifact is cheaper or more reliable than further discussion, especially for UI direction, interaction behavior, or a state model. Record the decision learned from the prototype before continuing.
- **Spec:** Invoke `to-spec` when the requirements are settled but the work benefits from a durable specification, or when the user requests one. It must synthesize the current context without starting another interview.
- **Tickets:** Invoke `to-tickets` after a spec or settled plan only when the work spans multiple independently deliverable slices, multiple sessions, or the user requests tickets.

If a named downstream skill is unavailable, follow the same behavior directly instead of stopping just to install it.

Resolve skills by capability, not by an exact namespace. Claude Code plugins may expose names such as `superpowers:test-driven-development`; other hosts may expose only `test-driven-development`.

## Reuse Execution Discipline

Once clarification is complete, reuse these Superpowers skills when available:

- Invoke `test-driven-development` for feature and bug-fix implementation where behavior can be tested.
- Invoke `systematic-debugging` when a failure, regression, flaky test, or unexpected behavior appears; diagnose before proposing a fix.
- Invoke `requesting-code-review` after substantial implementation and before merge or handoff.
- Apply `receiving-code-review` when evaluating review feedback rather than accepting it blindly.
- Invoke `verification-before-completion` before claiming the work is complete or passing.

These skills govern execution quality only. They must not reopen settled product decisions or introduce new routine approval checkpoints.

In Claude Code, prefer the installed Superpowers versions when they are callable. If they are unavailable or user-only, apply the same discipline inline; do not stop merely to ask the user to invoke `/debug`, `/code-review`, or `/verify`.

## Finish the Clarification

Before routing or executing, give a compact record:

```markdown
Decided: <choices now binding>
Assumed: <defaults Codex selected>
Next: <direct execution, prototype, spec, or tickets>
```

Then continue with the selected route unless the user explicitly asked only for discussion or a decision record.
