# Stage 3: native backend bridges

This level-2 index summarizes backend-specific flows. Read only the backend
being changed.

- [Cosmos Policy bridge](cosmos-policy.md) — call the official Predict2 2B
  LIBERO API and return actions, future state, and value. Status: upstream
  merged and checkpoint downloaded; independent runtime setup blocked on missing
  Torch/CUDA packages, native inference unverified.
- [DreamZero bridge](dreamzero.md) — proxy the official WebSocket client using
  explicit DROID camera/proprio/action schemas. Status: implemented, native
  mapping/session tests complete; native runtime unverified.
