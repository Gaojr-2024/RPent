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

## Pending evidence

- Real capabilities call and prediction.
- Bounded LIBERO execution and state artifact inspection.
