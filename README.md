# Blindspec

An independent tester for AI coding agents.

## The problem

When an AI agent writes both the code and the tests, the tests leak the answers.
Agents can hardcode expected outputs, weaken assertions, or skip tests, and still report "all tests pass".
It is the same problem as train/test leakage in machine learning: if the model sees the test data, the score means nothing.

## The idea

A separate AI writes tests from the requirements only, never from the implementation.
Those tests are locked away where the coding agent cannot read or edit them.
Blindspec runs them itself after the code is written and reports failures as plain-language behaviour, without revealing test code or expected values.
It also uses mutation testing to check whether the coding agent's own tests actually catch bugs.

## Planned v1

- Python only, pytest-based.
- A CLI tool plus a Claude Code integration via hooks.
- A plain-language report at the end of each task.

## Status

Early design stage.
No code yet.
