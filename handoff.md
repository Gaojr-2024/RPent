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
- Latest commit: `30093a3` — merged upstream `main` at `886b3b2`.
- The merge was pushed to `origin/feat/add-wam-module`.
- Upstream's `task_card` implementation is now `flash`; preserve that rename.
- Offline verification after the merge: `610 passed, 3 skipped`.
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
- Current blocker: `.venv/bin/python -c 'import torch'` fails with
  `ModuleNotFoundError: No module named 'torch'`.
- Host GPU: NVIDIA GeForce RTX 3060, 12 GiB VRAM.

Resume with the official dependency command from the Cosmos checkout:

```bash
cd /home/gao/worldmodel/harnessvla/cosmos-policy
/home/gao/.local/bin/uv sync --extra cu128 --group libero --python 3.10
.venv/bin/python -c 'import torch, cosmos_policy; print(torch.__version__, torch.cuda.is_available())'
```

Then start the bridge with RPent on `PYTHONPATH` and the downloaded local
checkpoint:

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

For an Astra-style planner run, explicitly use `--model gpt-6-astra
--reasoning-effort low`; the previous Dashboard was launched without those
flags and used the local Codex default instead. Do not claim native success
until capabilities, one prediction, bounded `wam_act`, artifacts, and cleanup
are all observed.

## Remaining work order

1. Complete Cosmos `uv sync`; if it fails, record the full error here and do
   not install CUDA dependencies into RPent's `.venv`.
2. Verify Torch/CUDA and official Cosmos imports.
3. Start the bridge and test capabilities.
4. Test one real prediction with a controlled raw LIBERO observation.
5. Start local RPent services with explicit `SAM3_CHECKPOINT_PATH` and
   `PI05_CHECKPOINT_PATH`, attach the Cosmos endpoint, and run one bounded
   `wam_act` action.
6. Record exact commands/results in `.process/03-native-bridges/cosmos-policy.md`
   and `.process/04-release-validation/validation.md`.
7. Run focused tests, full unit tests, pre-commit, update `PROCESS.md`, commit,
   and push.

## Important boundaries

- Cosmos is a LIBERO-compatible 7D WAM backend; DreamZero-DROID remains an 8D
  DROID-only backend and must be rejected by LIBERO.
- Do not silently pad, truncate, or reinterpret DreamZero actions.
- Do not add checkpoints or generated output to the repository.
- Do not merge additional upstream changes without rerunning the full offline
  suite and reviewing interactions with WAM/Flash runtime wiring.
