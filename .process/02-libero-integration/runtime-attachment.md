# Runtime attachment flow

## Operation

1. Parse `--wam-backend {cosmos,dreamzero}` and `--wam-endpoint` as a required
   pair in `robots/libero/robot_spec.py`.
2. Connect to the already-running bridge through RPent HTTP or socket RPC.
3. Construct the selected client under the independent `wam_model` key.
4. Require `libero_7d`, action dimension 7, and the exact LIBERO action schema
   before returning runtime kwargs.
5. Reject DreamZero-DROID during initialization because its native contract is
   not compatible.

## Evidence

`tests/unit_tests/robots/libero/test_libero_wam_runtime.py` covers Cosmos
attachment and DreamZero incompatibility.
