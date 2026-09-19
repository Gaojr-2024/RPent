# WAM integration process

This is the level-1 project index. Read it first, then open only the stage that
contains the current task. Do not preload every document under `.process/`.

Branch: `feat/add-wam-module`

Design: `../2026-09-16-wam-integration-design.md` in the current harness
workspace.

## Project stages

1. [Action-model contracts](.process/01-action-model-contracts/PROCESS.md) —
   implemented and covered by offline tests.
2. [LIBERO runtime and tool](.process/02-libero-integration/PROCESS.md) —
   implemented and covered by offline tests.
3. [Native backend bridges](.process/03-native-bridges/PROCESS.md) — bridge code
   and fake-native tests complete; real checkpoints and services remain unverified.
4. [Documentation and end-to-end validation](.process/04-release-validation/PROCESS.md)
   — paired usage documentation and offline validation complete; native E2E
   remains pending.

## Latest evidence

- Focused WAM/config/tool/runtime tests: `56 passed`.
- Complete offline unit suite in `.venv`: `622 passed, 3 skipped`.
- Focused Ruff lint, Ruff formatting, and `git diff --check`: passed.
- Full pre-commit: passed.
- Native DreamZero/Cosmos GPU inference: not run.

## Next task

Validate Cosmos and DreamZero in their native GPU environments when those
environments and checkpoints are available.
