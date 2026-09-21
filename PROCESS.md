# WAM integration process

This is the level-1 project index. Read it first, then open only the stage that
contains the current task. Do not preload every document under `.process/`.

Branch: `feat/add-wam-module`

Latest local commit: `30093a3` (merged `upstream/main` at `886b3b2`).

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
- Focused Dashboard/WAM/config tests: `74 passed`.
- Complete offline unit suite after upstream merge in `.venv`: `610 passed, 3 skipped`.
- Focused Ruff lint, Ruff formatting, and `git diff --check`: passed.
- Full pre-commit: passed.
- Chinese Dashboard served successfully with local Pi0.5 and SAM3 checkpoints;
  the session and both shared components reached `ready`.
- Cosmos Policy checkpoint downloaded outside the repository at
  `/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy/`; native
  environment setup remains incomplete because its `.venv` currently lacks
  `torch`.
- Native DreamZero/Cosmos GPU inference: not run.

## Next task

Finish the independent Cosmos environment, then validate bridge capabilities,
one prediction, and a bounded LIBERO WAM action; DreamZero remains a separate
native DROID-only validation.
