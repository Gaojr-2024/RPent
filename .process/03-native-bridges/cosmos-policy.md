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

The RPent branch now includes upstream `main` through merge commit `30093a3`
(upstream `886b3b2`). The official Cosmos Policy checkout is at
`/home/gao/worldmodel/harnessvla/cosmos-policy`, commit `18a2acc`, and the
public LIBERO checkpoint is downloaded outside RPent at
`/home/gao/worldmodel/harnessvla/checkpoints/cosmos-policy/`. Files currently
present include `Cosmos-Policy-LIBERO-Predict2-2B.pt` (about 3.91 GiB),
`libero_dataset_statistics.json`, and `libero_t5_embeddings.pkl`.

The independent environment was created at
`/home/gao/worldmodel/harnessvla/cosmos-policy/.venv`, but its health check
failed with `ModuleNotFoundError: No module named 'torch'`; rerun the official
dependency sync before starting the bridge. The host GPU check reports an
NVIDIA GeForce RTX 3060 with 12 GiB VRAM.

## Pending evidence

- Complete `uv sync --extra cu128 --group libero --python 3.10` and verify
  `torch`/CUDA imports in the independent environment.
- Start `scripts/wam/cosmos_policy_rpc_bridge.py` with the local checkpoint and
  call `action_model.capabilities` over HTTP.
- Run one real `action_model.predict`, then attach the bridge to LIBERO and run
  one bounded `wam_act` chunk with artifacts and cleanup evidence.
