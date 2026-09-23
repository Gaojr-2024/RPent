# Cosmos Policy bridge flow

## Operation

1. Run `scripts/wam/cosmos_policy_rpc_bridge.py` inside the official
   `cosmos-policy` LIBERO environment with RPent on `PYTHONPATH`.
2. Load `PolicyEvalConfig` for
   `cosmos_predict2_2b_480p_libero__inference_only` and the checkpoint
   `nvidia/Cosmos-Policy-LIBERO-Predict2-2B`.
3. Accept raw LIBERO primary/wrist images plus the official 9-D
   gripper/EEF-position/quaternion proprio order, then apply the upstream
   vertical image flip.
4. Let upstream code own dataset statistics, T5 embeddings, remaining image
   transforms, normalization, denoising, and action unnormalization.
5. Map the validated fields into upstream `get_action` and return its actions,
   future images, and value.
6. Verify with fake-native tests before running the real checkpoint.

## Completed evidence

`test_cosmos_bridge_uses_official_preprocessing_and_preserves_aux_outputs`
verifies official loader/config calls, observation mapping, actions, future
images, value, raw-image orientation, and the 9-D proprio contract without
importing Cosmos Policy.

The RPent branch now includes upstream `main` through merge commit `847a749`
(upstream `eb269c8`). The official Cosmos Policy checkout is at
`/home/gao/worldmodel/harnessvla/cosmos-policy`, commit `18a2acc`, and the
public LIBERO checkpoint is downloaded outside RPent at
`/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy/`. Files currently
present include `Cosmos-Policy-LIBERO-Predict2-2B.pt` (about 3.91 GiB),
`libero_dataset_statistics.json`, and `libero_t5_embeddings.pkl`.

The independent environment is now synchronized with the official dependency
set (`uv resolved 352 packages`) in
`/home/gao/worldmodel/harnessvla/cosmos-policy/.venv`. Its health check passes
with `torch 2.7.0+cu128`, compiled CUDA `12.8`, and (outside the sandbox)
`torch.cuda.is_available() == True` on an NVIDIA GeForce RTX 3060 with 12 GiB
VRAM. The gated base model file and tokenizer are cached outside Git under
`/home/gao/worldmodel/harnessvla/checkpoints/huggingface-http/`. The bridge
loads the official Cosmos stack when `CUDA_HOME` points to the installed
`nvidia/cuda_nvrtc` wheel directory.

With `COSMOS_INTERNAL=1` and the external `HF_HUB_CACHE`, the bridge loads the
local LIBERO policy checkpoint and serves RPC. Capabilities returned
`cosmos_policy`, `libero_7d`, action dimension 7, and the exact LIBERO schemas.
A real cached-LIBERO-instruction prediction returned finite `[16, 7]` actions,
future-observation data, and a scalar value.

A bounded Dashboard `wam_act` attempt on a PRO task reached the bridge but
executed no action. The Agent supplied a grasp-only paraphrase rather than the
environment task language; that exact string was absent from the official
precomputed T5 cache. Upstream attempted to load T5-11B online under
`HF_HUB_OFFLINE=1` and surfaced the misleading SentencePiece error
`not a string`. The PRO task's original instruction was also absent from the
official cache, so using it verbatim would not make that task supported.

RPent now follows the official cache-only deployment model. `wam_act` takes the
exact `task_descriptions` value from the current LIBERO observation and no
longer accepts Agent-authored instruction text. The bridge rejects cache misses
with an explicit unsupported-instruction error instead of loading T5-11B.
Focused WAM/upstream integration tests pass (`79 passed`); the complete offline
suite passes (`622 passed, 3 skipped`) after the latest upstream merge.

## Bounded owned-process evidence

- The owned Dashboard run at `logs/20260923-16:59:52_dashboard_session/`
  attached the bridge to standard `libero_10` task 5, executed one cached
  `wam_act` chunk with 16 finite actions, wrote
  `tasks/0001_libero_10_t5_s0/action_wam_act.mp4/05.mp4`, and returned
  `executed_steps=16`, `backend=cosmos_policy`, `done=false`. Dashboard
  cleanup stopped the owned process. This is bounded action-chain evidence,
  not benchmark task success.

The standard LIBERO runtime prerequisite is now verified: official simulation
assets are installed outside Git, and `libero_10` task 5 completes `env.reset`
under its isolated `LIBERO_CONFIG_PATH`.

The first cached standard-task call completed native inference in about 9.46
seconds (12.75 seconds end to end) and returned finite `[16, 7]` actions. Eight
gripper entries slightly undershot the legal lower bound (`-1.0002` to
`-1.0050`); all six motion dimensions remained in range. RPent now clips only
the binary gripper dimension to `[-1, 1]` while continuing to reject any
out-of-range translation or rotation before environment execution.

## Current operational gap

RPent now supports both an external `--wam-endpoint` and an owned Cosmos bridge
selected by `--wam-checkpoint`. The owned path launches the independent Cosmos
Python environment, waits for health and exact LIBERO capabilities, and joins
normal Dashboard cleanup. Release checks and the upstream handoff remain.
