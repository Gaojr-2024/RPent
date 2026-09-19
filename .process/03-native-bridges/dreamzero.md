# DreamZero bridge flow

## Operation

1. Start the official DreamZero WebSocket server for DreamZero-DROID.
2. Run `scripts/wam/dreamzero_rpc_bridge.py` inside that environment with RPent
   on `PYTHONPATH`.
3. Validate native server metadata: two external cameras, one wrist camera,
   session ID, and joint-position actions.
4. Require the explicit 14-field DROID proprio schema and a non-empty episode
   ID, map the three cameras, and call `WebsocketClientPolicy.infer`.
5. Advertise the returned 8-D joint-position-plus-gripper schema and only the
   DROID embodiment.
6. Confirm LIBERO runtime rejects it before environment execution.

## Completed evidence

Fake WebSocket tests verify native metadata/session requirements, required
episode IDs, session switching, and exact camera, 14-field proprio, prompt,
and 8-D action mapping.

## Pending evidence

- Real capabilities call and native DROID prediction.
- Explicit LIBERO incompatibility E2E check.
