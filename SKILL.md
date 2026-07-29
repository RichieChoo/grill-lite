---
name: grill-lite
description: Use focused repository discovery, one batched clarification confirmation, and proportionate delivery rigor to complete work with self-contained prototype, specification, ticketing, testing, debugging, review, and verification guidance. Use only when the user explicitly invokes grill-lite with `$grill-lite` in Codex or `/grill-lite` in Claude Code.
---

# Grill Lite

Reach an implementable shared understanding without making the user repeatedly correct the work, then deliver with proportionate rigor and no external skill dependencies.

## 1. Lock the Working Language

Determine the working language before any user-facing response.

- Honor an explicit output-language request first. Otherwise use the dominant natural language of the invoking request, falling back to the established conversation language when the request is genuinely mixed.
- Ignore code blocks, quoted material, URLs, paths, identifiers, API fields, and pasted repository content when detecting the language.
- Use the working language for narration, questions, headings, briefs, specs, tickets, updates, and final responses.
- Preserve exact code identifiers, commands, paths, API names, product names, and project-owned UI copy.
- When a language request applies only to one named artifact, keep surrounding communication in the working language and render only that artifact in the requested language.
- Switch only when the user explicitly requests it or clearly continues the conversation in another language. Never ask when the language can be inferred.

## 2. Follow the Core Contract

- Scope this invocation to the current request and its direct follow-ups, not every later request in the conversation.
- Treat this invocation as replacing Superpowers `brainstorming` and `writing-plans` for the current request.
- Do not look for, install, or invoke external `prototype`, `to-spec`, `to-tickets`, or Superpowers skills. Use this skill's bundled references.
- Inspect before asking, but do not exhaust a large search space when the user can provide a faster, more authoritative pointer.
- Ask about outcomes, constraints, evidence, and decisions, not file-level implementation steps.
- Infer reversible preferences and implementation details established by the repository. Do not infer user intent from the first plausible code path.
- Keep Lightweight work light. Give Structured and Rigorous work one proactive clarification confirmation before execution.
- Use one execution brief. Do not print separate upfront briefs for every selected capability.

## 3. Discover Efficiently

Start with one focused discovery pass:

1. Inspect the most likely files, symbols, documentation, configuration, and existing tests.
2. Identify direct evidence, inferred assumptions, and missing context.
3. Provisionally classify the request as Lightweight, Structured, or Rigorous; finalize the profile after clarification.

For likely Lightweight work, continue self-service discovery unless access is blocked or an ambiguity could materially change the result.

For likely Structured or Rigorous work, ask for discovery help when a user pointer is more valuable than another search pass. Prefer asking before a broad recursive scan, long history archaeology, cross-repository search, or speculative tracing across several plausible ownership boundaries. High-value inputs include an authoritative module or document, prior change, owner, representative non-sensitive test identifier or payload, fixture, and known-good operational flow.

Treat external integrations and initialization, import, migration, or synchronization flows as evidence-sensitive. Ask for representative evidence before deriving mappings, defaults, lifecycle semantics, or failure behavior from indirect code.

Do not search silently for an extended period. If two focused passes or about two minutes elapse without the evidence needed for likely Structured or Rigorous work, show a localized discovery checkpoint:

```markdown
## <localized "Discovery Checkpoint">

<localized "Inspected">: <specific evidence already checked>
<localized "Confirmed">: <facts supported by direct evidence>
<localized "Evidence gaps">: <facts still inferred or ambiguous>
<localized "Useful input">: <smallest pointer, example, test input, or reference that would help>
<localized "Fallback">: <how investigation will continue if the user does not know>
```

Ask for the useful input directly. Let the user say they do not know, then resume without repeating the same request. When enough provisional understanding already exists, fold this checkpoint into the clarification brief below instead of showing two briefs back to back.

Never claim that the implementation boundary is unique while a material source of truth, external payload, or operational flow remains unverified.

## 4. Clarify and Confirm Once

Choose the clarification behavior from the provisional profile:

| Profile | Clarification behavior |
| --- | --- |
| Lightweight | Skip clarification when the request is actionable and no material decision or high-value context question exists. Otherwise use the same clarification brief below. |
| Structured | Always run one proactive clarification confirmation before execution. |
| Rigorous | Always run one proactive clarification confirmation before execution. |

Use one batch. Ask the smallest useful set of high-value questions, usually one to three. Include tightly related questions when omitting them would predictably force the user to send multiple corrections later.

At most three questions may require material decisions. Evidence, navigation, test-data, reference, and other non-decision context questions do not consume that budget, but every question must still earn its interruption. There is no lifetime cap on evidence questions at genuinely new discovery boundaries; batch them and do not repeat a request the user could not answer.

Select only relevant dimensions:

- intended users, callers, workflow, and observable outcome;
- assumptions inferred from code that could surprise the user;
- edge cases, failure behavior, compatibility, security, data handling, and protected scope;
- references, examples, test inputs, fixtures, operational history, and known-good flows;
- validation environment and evidence that would make the result trustworthy.

If more than three material decisions remain, choose the smallest coherent slice that can be settled with three and defer the rest. If no safe coherent slice exists, present the three highest-impact decisions and stop after clarification.

Use this shape:

```markdown
## <localized "Clarification Brief">

<localized "Inspected">: <focused repository evidence already checked>
<localized "Provisional understanding">: <outcome and likely delivery boundary>
<localized "Assumptions to verify">:
- <only assumptions that affect usefulness, trust, or likely rework>
<localized "Questions">:
1. <one high-value decision or context question>
   <localized "Recommended">: <default and concise reason when this is a decision>
   <localized "Consequence if deferred">: <excluded behavior when material>
<localized "Deferred or out of scope">: <scope intentionally excluded>
<localized "Useful additions">: <invite constraints, references, test data, concerns, and the user's own questions in the same reply>
<localized "Confirmation">: <ask the user to confirm or correct the provisional understanding while answering>
```

Omit empty fields, never manufacture options, and put recommendations first. Pause for the user's response.

Treat a response that confirms or corrects the understanding and resolves material questions as clarification confirmation. `Use recommendations` confirms the provisional understanding and recommended answers unless the user says otherwise. Answer the user's questions and incorporate their additions before execution.

Allow one follow-up batch of at most two material decisions only when the response creates a new blocker involving security, data loss, a public contract, an irreversible migration, or mutually incompatible requirements. If the response materially changes a confirmed boundary, show only the clarification delta and confirm it again. Otherwise state the applied assumption and continue.

Do not add plan approval or execution approval after clarification confirmation.

## 5. Finalize Delivery Rigor

Judge risk, uncertainty, coordination, and reversibility, not lines of code or file count. A user may request a stronger profile but cannot use a weaker profile to bypass a hard safety gate.

### Rigorous

Use when any hard gate applies: authentication, authorization, security, privacy, payment, destructive or irreversible data work, migration, public API or schema compatibility, cross-system partial-failure risk, broad blast radius without reliable rollback, or an explicit high-assurance request.

### Structured

Use when no hard gate applies but at least one coordination gate does: runnable design uncertainty, cross-layer coordination, durable handoff, multiple independent delivery slices, work spanning sessions, substantial acceptance criteria or dependencies, or no reusable implementation pattern.

### Lightweight

Use only when the selected scope is actionable, local, reversible, low blast radius, compatible with existing patterns, verifiable, and expected to fit the current session without durable handoff.

## 6. Select Only Needed Capabilities

- **Direct execution:** default when no artifact below is needed.
- **Prototype:** use when one named UI, interaction, logic, or state question is cheaper to answer with a runnable artifact. Read [references/prototype.md](references/prototype.md).
- **Specification:** use when settled requirements need durable handoff or the user requests one. Read [references/spec.md](references/spec.md).
- **Tickets:** use when work has multiple independently deliverable slices, spans sessions, or the user requests tickets. Read [references/tickets.md](references/tickets.md).
- **Quality discipline:** read the relevant sections of [references/quality.md](references/quality.md). Apply TDD to testable behavior changes, systematic debugging only when a failure or anomaly exists, review to substantial or Rigorous changes, and fresh verification before every completion claim.

Do not automatically stack Prototype, Specification, and Tickets. Select each only when its trigger exists. A failure may activate debugging in any profile without changing the artifact route.

## 7. Show One Execution Brief

Before the first edit, external write, or other material action, show one localized execution brief:

```markdown
## <localized "Execution Brief">

<localized "Profile">: <localized delivery profile>
<localized "Reason">: <observable profile signals>
<localized "Understood">: <confirmed outcome and selected scope>
<localized "Decided">: <binding choices, when any>
<localized "Assumed">: <remaining reversible defaults, when any>
<localized "Will not change">: <protected behavior, data, or systems>
<localized "Deferred">: <scope intentionally excluded>
<localized "Evidence">: <authoritative code, payloads, references, or user examples>
<localized "Evidence gaps">: <remaining unverified assumptions that could affect success>
<localized "Capability plan">:
- <localized capability>: <why it applies>; <bounded objective>; <observable exit condition>
<localized "Next">: <immediate action>
```

Always include Profile, Reason, Understood, Will not change, Evidence, Capability plan, and Next. Include Evidence gaps for Structured or Rigorous work when a success-critical assumption remains unverified. Include optional fields only when useful.

Give every selected capability exactly one concise entry in Capability plan:

- Direct execution: production change boundary.
- Prototype: named uncertainty and disposal boundary.
- Specification: handoff audience and destination.
- Tickets: slicing basis and destination.
- TDD: first behavior and expected Red signal.
- Review: diff scope and priority risks.
- Verification: fresh checks that prove or disprove completion.

The execution brief is a visibility checkpoint, not an approval request. Continue in the same turn after required clarification confirmation. If execution reveals a material boundary change, return to clarification confirmation for the delta instead of asking the user to approve an implementation plan.

## 8. Announce Only Plan Changes

Do not emit separate briefs when moving among capabilities already listed in the execution brief.

If new evidence activates an unplanned capability or materially changes a planned capability, show one localized update before using it:

```markdown
## <localized "Capability Update">

<localized "Activated or changed">: <capability>
<localized "Why now">: <new evidence>
<localized "Next evidence">: <bounded action or check>
<localized "Exit condition">: <observable evidence required before moving on>
```

The common case is Systematic debugging activated by an unexpected failure. Name the failure and the first evidence boundary or falsifiable hypothesis. Do not issue an update per command, test, ticket, review step, or debugging experiment. Continue immediately unless a real decision, authorization, or safety gate blocks progress.
