# Tickets

Break settled work into independently deliverable vertical slices. Do not reopen product decisions or ask the user to approve the breakdown.

## Slicing Rules

- Each ticket delivers a narrow but complete observable behavior across whatever layers it needs.
- Each ticket is independently demoable or verifiable and fits one fresh working session.
- Slice by delivered behavior, not database, backend, frontend, and tests as separate horizontal tickets.
- State blocking relationships. A ticket with no blockers can start immediately.
- Put enabling refactors first only when they are genuinely required.

For a mechanical change with a wide blast radius that cannot land as a green vertical slice, use expand-contract:

1. Expand by adding the new form beside the old.
2. Migrate callers in independently verifiable batches.
3. Contract by removing the old form after every migration completes.

Use this ticket shape:

- Title
- Delivered behavior
- Acceptance criteria
- Blocked by

Draft locally by default. Do not publish to a tracker, create branches, or commit unless explicitly requested and authorized.
