# Quality Discipline

Apply only the relevant sections. Verification applies to every delivery profile.

## Test-Driven Development

Use for testable feature, bug-fix, refactoring, and behavior changes. Exempt throwaway prototypes, generated output, pure configuration, or work with no practical automated seam; record the resulting test gap.

1. Write one minimal behavior-focused test.
2. Run it and confirm it fails for the expected missing behavior, not a setup error.
3. Write the smallest production change that passes it.
4. Run the focused test and relevant surrounding suite.
5. Refactor only while tests remain green.

Prefer real behavior over mock assertions. Do not alter a valid failing test merely to accommodate the implementation.

## Systematic Debugging

Enter this loop only after a bug, failure, regression, flake, performance problem, or unexpected behavior appears.

1. Read the full error and reproduce the problem.
2. Inspect recent changes and gather evidence at relevant component boundaries.
3. Trace the bad state backward to its source and compare with a working pattern.
4. State one falsifiable root-cause hypothesis.
5. Test it with the smallest experiment and one changed variable.
6. Add the smallest reproducer or regression test, then fix the root cause rather than the symptom.
7. If three fix attempts fail, stop and reassess the architecture with the user before attempting another fix.

## Review

Review substantial changes and every Rigorous delivery before handoff. Use an independent reviewer when the host permits one; otherwise review the diff directly.

- Compare the diff with the request, decisions, protected boundaries, and acceptance criteria.
- Prioritize correctness bugs, security, data loss, regressions, compatibility, missing behavior, and test gaps.
- Report findings by severity with precise file and line references.
- Verify review feedback against the actual codebase before implementing it; push back with evidence when it is incorrect.
- Resolve critical and important findings before completion or report them as blockers.

## Verification Before Completion

Do not claim completion from confidence, a diff, or a previous run.

1. Map each completion claim and acceptance criterion to observable evidence.
2. Run the full relevant command or inspect the real resulting behavior freshly.
3. Read the complete output, exit status, failure count, and warnings.
4. Check the final diff and confirm protected and deferred scope stayed untouched.
5. State what passed, what was not run, and any residual risk.

Evidence comes before every success claim.
