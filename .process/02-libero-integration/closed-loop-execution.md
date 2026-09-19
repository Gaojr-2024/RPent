# Closed-loop execution flow

## Operation

1. Read raw primary/wrist images and the 9-D gripper/EEF-position/quaternion
   proprio fields from the latest real LIBERO observation.
2. Add explicit proprio/action schemas and `embodiment="libero_7d"`.
3. Predict one action chunk and validate `[T, 7]`, finite values, per-response
   schema, `[-1, 1]` bounds, and cancellation before any environment call.
4. Execute at most `max_actions_per_chunk`, capture frames/Flywheel transitions,
   and replace the cached observation with the actual final environment state.
5. For `max_chunks > 1`, rebuild the request and re-predict from that new real
   observation.
6. Return only executed count, backend, checkpoint, scalar value, and done state.

## Evidence

`tests/unit_tests/robots/libero/test_libero_wam.py` covers raw observation
mapping, chunk bounds, cancellation, re-prediction, and rejection before
execution.
