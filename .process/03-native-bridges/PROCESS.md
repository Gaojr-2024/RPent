# Stage 3: native backend bridges

This level-2 index summarizes backend-specific flows. Read only the backend
being changed.

- [Cosmos Policy bridge](cosmos-policy.md) — call the official Predict2 2B
  LIBERO API and return actions, future state, and value. Status: native
  capabilities and prediction pass; WAM now uses exact environment task
  language, supports external and RPent-owned lifecycle modes, and rejects
  instructions outside the official T5 cache; bounded owned-process execution,
  artifacts, and cleanup pass on the standard cached task.
- [DreamZero bridge](dreamzero.md) — proxy the official WebSocket client using
  explicit DROID camera/proprio/action schemas. Status: implemented, native
  mapping/session tests complete; native runtime unverified.
