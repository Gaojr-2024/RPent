# WAM integration process

This is the level-1 project index. Read it first, then open only the stage that
contains the current task. Do not preload every document under `.process/`.

Branch: `feat/add-wam-module`

Latest local commit: `847a749` (merged `upstream/main` at `eb269c8`).

Design: `../2026-09-16-wam-integration-design.md` in the current harness
workspace.

## Project stages

1. [Action-model contracts](.process/01-action-model-contracts/PROCESS.md) —
   implemented and covered by offline tests.
2. [LIBERO runtime and tool](.process/02-libero-integration/PROCESS.md) —
   implemented and covered by offline tests.
3. [Native backend bridges](.process/03-native-bridges/PROCESS.md) — bridge code,
   lifecycle tests, owned startup, and one bounded native action run complete;
   longer benchmark execution remains separate.
4. [Documentation and end-to-end validation](.process/04-release-validation/PROCESS.md)
   — paired usage documentation, standard assets, offline validation, and one
   owned native bounded run complete; benchmark success remains separate.
5. [Mainline integration plan](.process/plan/PROCESS.md) — Cosmos validation
   handoff, release checks, commit, push, and upstream PR; DreamZero remains in
   its current state. Status: planned.

## Latest evidence

- Focused WAM/config/tool/runtime tests: `34 passed` for lifecycle/config/bridge
  coverage in the current handoff round.
- Focused Dashboard/WAM/config tests: `79 passed` before the owned lifecycle
  round; the final complete suite is green.
- Complete offline unit suite after cache-only task-language wiring in `.venv`:
  `625 passed, 3 skipped` after merging current upstream `main` and adding the
  owned Cosmos lifecycle.
- Focused Ruff lint, Ruff formatting, and `git diff --check`: passed.
- Full pre-commit: passed.
- Chinese Dashboard served successfully with local Pi0.5 and SAM3 checkpoints;
  the session and both shared components reached `ready`.
- Cosmos Policy checkpoint downloaded outside the repository at
  `/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy/`; its independent
  environment now has `torch 2.7.0+cu128` with CUDA available outside the
  sandbox. Base model/tokenizer assets are cached outside Git; bridge
  capabilities and one real prediction have passed. A bounded PRO-task request
  exposed an official T5-cache miss before action execution; WAM now uses exact
  environment task language and rejects uncached instructions without loading
  T5-11B. Focused lifecycle regression tests pass (`34 passed`). WAM supports
  both external endpoint and RPent-owned lifecycle modes.
- Official standard LIBERO assets are installed outside Git; `libero_10` task 5
  now passes `healthz` and `env.reset` with its exact cached task language.
- Owned Cosmos native run: RPent started the bridge from
  `/home/gao/worldmodel/harnessvla/cosmos-policy/.venv/bin/python`, loaded the
  local checkpoint, passed capabilities, and executed one `wam_act` chunk with
  16 actions. Evidence: `logs/20260923-16:59:52_dashboard_session/`, task
  `0001_libero_10_t5_s0`, artifact `action_wam_act.mp4/05.mp4`, result
  `executed_steps=16`, `backend=cosmos_policy`, `done=false`. The owned process
  was stopped with the Dashboard test session after the run.
- Native DreamZero/Cosmos GPU inference: not run.

## Next task

Execute [.process/plan/upstream-handoff.md](.process/plan/upstream-handoff.md):
reconcile the owned-process evidence, run release checks, and submit
`feat/add-wam-module` to `RLinf/RPent:main`.
