# WAM handoff — 2026-09-21

## Read first

1. `AGENTS.md` — repository rules.
2. `WAM_AGENT.md` — WAM architecture and compatibility constraints.
3. `PROCESS.md` — project-level status and next task.
4. `.process/03-native-bridges/PROCESS.md` — native bridge index.
5. `.process/03-native-bridges/cosmos-policy.md` — current Cosmos operation,
   evidence, and blocker.
6. `.process/04-release-validation/validation.md` — validation commands and
   evidence.

## Repository state

- Repository: `/home/gao/worldmodel/harnessvla/rpent`
- Branch: `feat/add-wam-module`
- Latest commit: `847a749` — merged upstream `main` at `eb269c8`.
- The latest merge has not yet been pushed to `origin/feat/add-wam-module`.
- Upstream's `task_card` implementation is now `flash`; preserve that rename.
- Offline verification after owned lifecycle wiring:
  `625 passed, 3 skipped` after the latest upstream merge.
- `pre-commit run --all-files`: passed.

## Existing local assets (do not commit)

- Pi0.5 checkpoint:
  `/home/gao/worldmodel/harnessvla/rpent/checkpoints/RLinf-Pi05-LIBERO-130-fullshot-SFT`
- SAM3 checkpoint:
  `/home/gao/worldmodel/harnessvla/rpent/checkpoints/sam3/sam3.pt`
- Cosmos checkpoint directory:
  `/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy/`
- Cosmos weight file:
  `/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy/Cosmos-Policy-LIBERO-Predict2-2B.pt`
- Cosmos statistics and embeddings are in the same directory.
- Keep all checkpoints, caches, logs, and generated rollouts outside Git.

## Cosmos environment

- Official checkout:
  `/home/gao/worldmodel/harnessvla/cosmos-policy`
- Checkout commit: `18a2acc`.
- Intended environment: `/home/gao/worldmodel/harnessvla/cosmos-policy/.venv`.
- Dependency sync is complete in the independent `.venv`; outside the sandbox,
  `.venv/bin/python` reports `torch 2.7.0+cu128`, CUDA `12.8`, and an RTX 3060
  with `torch.cuda.is_available() == True`.
- The gated base model and tokenizer are cached outside Git under
  `/home/gao/worldmodel/harnessvla/checkpoints/huggingface-http/`.
- The bridge now loads the local LIBERO policy checkpoint with
  `COSMOS_INTERNAL=1`, the external `HF_HUB_CACHE`, and the CUDA wheel path.
- A real `action_model.capabilities` call succeeded with `cosmos_policy`,
  `libero_7d`, and action dimension 7. A real cached-LIBERO-instruction
  `action_model.predict` returned finite `[16, 7]` actions, future-observation
  data, and a scalar value.
- A bounded PRO-task `wam_act` reached the bridge but executed no action because
  its instruction was absent from the official precomputed T5 cache. RPent now
  uses exact environment task language and rejects cache misses instead of
  loading T5-11B online. Validate execution on a cached standard task.
- Task 1 is now implemented: `--wam-endpoint` remains external/borrowed, while
  `--wam-checkpoint` starts Cosmos inside its isolated `.venv` through a
  `ProcessDaemon`, validates health and `libero_7d` capabilities, and joins
  Dashboard cleanup. The venv shim path is preserved so its numpy/CUDA packages
  are used correctly.
- Owned native evidence is recorded at
  `logs/20260923-16:59:52_dashboard_session/`: standard `libero_10` task 5
  executed one cached Cosmos `wam_act` chunk (`16` actions), returned finite
  actions/value, created `action_wam_act.mp4/05.mp4`, and left `done=false`.
  The owned process was stopped with the test Dashboard session; benchmark task
  success is intentionally separate from this bounded action evidence.
- Current operational gap: the complete WAM change is still uncommitted and
  unpushed; final diff review, commit, push, and upstream PR remain.
- Host GPU: NVIDIA GeForce RTX 3060, 12 GiB VRAM.

Resume with the official dependency command from the Cosmos checkout:

```bash
cd /home/gao/worldmodel/harnessvla/cosmos-policy
/home/gao/.local/bin/uv sync --extra cu128 --group libero --python 3.10
.venv/bin/python -c 'import torch, cosmos_policy; print(torch.__version__, torch.cuda.is_available())'
```

For external bridge mode, start the bridge manually with RPent on `PYTHONPATH`
and the downloaded local checkpoint:

```bash
PYTHONPATH=/home/gao/worldmodel/harnessvla/rpent \
  .venv/bin/python \
  /home/gao/worldmodel/harnessvla/rpent/scripts/wam/cosmos_policy_rpc_bridge.py \
  --checkpoint /home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy \
  --host 127.0.0.1 --port 8120
```

The bridge must first answer `action_model.capabilities`. Then use a separate
RPent process with local Pi0.5/SAM3 paths and:

```bash
--wam-backend cosmos --wam-endpoint http://127.0.0.1:8120
```

For RPent-owned mode, use `--wam-backend cosmos --wam-checkpoint
/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy`. RPent launches
`/home/gao/worldmodel/harnessvla/cosmos-policy/.venv/bin/python`, waits for
health/capabilities, and stops the bridge during Dashboard cleanup. Preserve
the venv shim path so numpy and CUDA packages load from the Cosmos environment.

For an Astra-style planner run, explicitly use `--model gpt-6-astra
--reasoning-effort low`; the previous Dashboard was launched without those
flags and used the local Codex default instead. Do not claim native success
until capabilities, one prediction, bounded `wam_act`, artifacts, and cleanup
are all observed.

## Remaining work order

1. Keep the downloaded base assets and local policy checkpoint outside Git.
2. Run final release checks and reconcile exact owned-process commands/results
   in `.process/03-native-bridges/cosmos-policy.md`
   and `.process/04-release-validation/validation.md`.
3. Run focused tests, full unit tests, pre-commit, update `PROCESS.md`, commit,
   and push.

## Important boundaries

- Cosmos is a LIBERO-compatible 7D WAM backend; DreamZero-DROID remains an 8D
  DROID-only backend and must be rejected by LIBERO.
- Do not silently pad, truncate, or reinterpret DreamZero actions.
- Do not add checkpoints or generated output to the repository.
- Do not merge additional upstream changes without rerunning the full offline
  suite and reviewing interactions with WAM/Flash runtime wiring.
