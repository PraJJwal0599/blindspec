# Blindspec

**An independent tester for AI coding agents.**

Blindspec writes tests for your feature *before* your AI coding agent writes the code, locks those tests away where the agent cannot see them, and then grades the agent's work with them.
Think of it as a proctored exam for AI-written code.

> Status: early design stage.
> There is no working code yet.
> This README describes what Blindspec will do.

## Why Blindspec exists

Most developers now let an AI agent write their code, and the same agent also writes the tests.
That is like a student writing their own exam: the tests leak the answers.

In practice, coding agents often:

- hardcode expected outputs so a test passes,
- weaken or remove assertions,
- skip or delete failing tests,
- and still report "all tests pass".

Code then gets shipped on the agent's word.
This is the same problem as train/test leakage in machine learning: if the model sees the test data, the score means nothing.

## What Blindspec does

Blindspec runs a **separate tester**, a fresh AI session that only ever sees the requirements, never the implementation.

| | Your coding agent | Blindspec tester |
|---|---|---|
| Sees the requirements | Yes | Yes |
| Sees the implementation | Yes | No, only names, signatures and docstrings |
| Sees the hidden tests | **Never** | Writes them |
| Runs the hidden tests | **Never** | Blindspec runs them |

## How it works

1. **Read requirements only.**
   The tester reads the task and the project docs (README, design docs), never the code itself.
2. **Write the exam first.**
   It writes tests for what the feature *should* do, including edge cases like empty inputs, invalid credentials and boundary values.
3. **Lock the tests away.**
   The tests are stored outside the project, and the coding agent is blocked from reading or editing them.
4. **Let the coder work.**
   Your coding agent builds the feature as normal, and may write its own tests too.
5. **Run the hidden exam.**
   Blindspec runs the locked tests and reports failures as plain-language behaviour, for example:
   *"Logging in with an empty password should be rejected, but it was accepted."*
   It never reveals test code or expected values.
6. **Check the coder's own tests.**
   Blindspec deliberately plants small bugs in the code (mutation testing).
   If the coder's tests still pass, they are weak or fake, and Blindspec flags them.

## How Blindspec stops answers leaking

- **Only Blindspec runs the hidden tests.** The coding agent never executes or reads them.
- **Behaviour-level feedback only.** No test code, no exact expected values.
- **Two test sets.** A *practice set* whose failures are reported back so the coder can fix bugs, and a *held-out set* that is never reported on and decides the final pass/fail.
  This stops the coder slowly overfitting to the feedback.
- **Retry limits.** A cap on how many fix attempts are allowed against the practice set.

## Where the tester gets its knowledge

Projects range from well-documented to bare, so the tester combines:

1. **Project docs**, when they exist.
2. **A code outline only:** function names, signatures, parameters, return types, docstrings and API routes, but never function bodies.
   Names carry a lot of intent, so the tester knows *what* exists without seeing *how* it works.
3. **A quick interview:** when docs are thin, Blindspec asks you two or three short questions, such as *"What should happen if the user is not logged in?"*, and builds a mini spec from your answers.

## Kinds of tests

- **Spec-based unit tests**, written from requirements before implementation.
- **Property-based tests** that check rules which must always hold, across many generated inputs (using [Hypothesis](https://hypothesis.readthedocs.io/)).
- **Mutation testing** to measure whether tests actually catch bugs (using [mutmut](https://github.com/boxed/mutmut)).
- **Integration tests** that combine two or three features end to end.
- **Cheat checks** that detect deleted or skipped tests, removed assertions and hardcoded outputs in the coder's changes.

## Version 1 plan

- Python projects only, using pytest.
- A command-line tool plus a Claude Code integration via hooks.
- The tester runs on the AI you already use, in its own separate session.
- A plain-language report at the end of each task.

Blindspec is designed to be agent-agnostic.
Support for other languages and other coding agents (Cursor, Codex, custom setups) is planned after v1.

## License

[MIT](LICENSE)
