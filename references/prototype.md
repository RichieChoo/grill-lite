# Prototype

Build a throwaway artifact that answers one explicit question. Do not use a prototype merely because the task is large.

## Common Rules

1. State the question before writing code.
2. Follow the repository's runtime, routing, component, and task-runner conventions.
3. Keep state in memory unless persistence is the question being tested.
4. Skip production polish, broad error handling, abstractions, and automated tests.
5. Make relevant state and variant changes directly observable.
6. Provide one command or URL for running it.
7. Record the answer learned before continuing.
8. Keep prototype code out of production. Do not create branches, commits, tracker items, or external artifacts unless the user explicitly requests them.

## Logic or State

- Put the uncertain behavior behind a small pure reducer, state machine, data transformation, or similarly narrow interface.
- Build the smallest interactive harness that can exercise the difficult transitions and show the full relevant state after each action.
- Keep I/O in the harness so validated logic can be rewritten or promoted independently.

## UI or Interaction

- Prefer embedding the comparison in the existing page so real layout, data density, navigation, and permissions remain visible.
- Create two or three structurally different variants only when comparison is the question. Do not manufacture a third option.
- Make variants switchable without losing context, using the project's existing routing or state conventions.
- Keep mutations stubbed or read-only unless mutation behavior is itself the question.

After a decision, implement the selected behavior under normal production quality rules and remove or clearly isolate the throwaway artifact.
