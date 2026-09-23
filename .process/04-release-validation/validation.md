# Validation flow

## Operation

1. Run focused Ruff and WAM tests after each implementation change.
2. Run `pre-commit run --all-files` and `pytest tests/unit_tests -v` before
   handoff; inspect hook edits and rerun affected checks.
3. In the Cosmos environment, test capabilities, one prediction, and a bounded
   LIBERO policy chain with artifacts and cleanup evidence.
4. In the DreamZero environment, test capabilities and one DROID prediction,
   then verify LIBERO rejects the backend without executing actions.
5. Record exact commands and outcomes in the relevant level-3 backend document
   and summarize only the newest evidence in root `PROCESS.md`.

## Current evidence

- Complete offline unit suite after cache-only task-language wiring:
  `622 passed, 3 skipped` in `.venv` after merging upstream `main` at
  `eb269c8` (localhost RPC tests required running outside the socket-restricted
  sandbox).
- Focused WAM/config/tool/runtime suite: `56 passed`.
- Focused Dashboard/WAM/config suite: `74 passed`.
- `pre-commit run --all-files`: passed.
- Chinese Dashboard manual startup returned HTTP 200. The first run exposed an
  unset `SAM3_CHECKPOINT_PATH`; restarting with the downloaded local SAM3 and
  Pi0.5 checkpoints reached session `ready` with both shared components
  `ready`. Evidence: `logs/20260919-17:04:03_dashboard_session/`.
- Native/GPU tests: not run.
- Cosmos checkpoint and dependency setup are complete. Outside the sandbox,
  `torch 2.7.0+cu128` reports CUDA available on the RTX 3060. The bridge has
  served capabilities and one real cached-LIBERO-instruction prediction
  (`[16, 7]`, finite actions, future-observation data, scalar value). A bounded
  PRO-task `wam_act` reached prediction but executed no action because its
  instruction was absent from the official precomputed T5 cache. RPent now uses
  exact environment task language and rejects cache misses without loading
  T5-11B; focused WAM/upstream integration tests pass (`79 passed`). A bounded
  cached standard-task execution remains unrecorded. Dashboard WAM readiness
  currently depends on keeping the externally started bridge alive.
- Sphinx build: not run because `sphinx-build` is unavailable locally.
- Standard LIBERO initially failed before reset because the wheel intentionally
  omits its simulation assets. `libero-download-assets` fetched the official
  `RLinf/LIBERO-assets` snapshot into the external Hugging Face cache and linked
  the RPent environment to it. With the isolated standard config at
  `/home/gao/worldmodel/harnessvla/checkpoints/libero-config-standard`, a
  temporary `libero_10` task 5 env server returned `healthz=ok`, completed
  `env.reset`, and reported the cached instruction `pick up the book and place
  it in the back compartment of the caddy`.
- The first cached standard-task `wam_act` produced finite `[16, 7]` actions in
  about 9.46 seconds of native inference. Execution was initially blocked by
  small gripper-only overshoots down to `-1.0050`; the runtime now clips only
  the gripper dimension and retains strict rejection for motion dimensions.
