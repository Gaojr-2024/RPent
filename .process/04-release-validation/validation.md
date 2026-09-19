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

- Complete offline unit suite: `622 passed, 3 skipped` in `.venv`.
- Focused WAM/config/tool/runtime suite: `56 passed`.
- Focused Dashboard/WAM/config suite: `74 passed`.
- `pre-commit run --all-files`: passed.
- Chinese Dashboard manual startup returned HTTP 200. The first run exposed an
  unset `SAM3_CHECKPOINT_PATH`; restarting with the downloaded local SAM3 and
  Pi0.5 checkpoints reached session `ready` with both shared components
  `ready`. Evidence: `logs/20260919-17:04:03_dashboard_session/`.
- Native/GPU tests: not run.
- Sphinx build: not run because `sphinx-build` is unavailable locally.
