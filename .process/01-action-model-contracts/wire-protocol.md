# Wire protocol flow

## Operation

1. Accept a unified request with RGB camera roles, one-dimensional finite
   proprioception, instruction, embodiment, and metadata.
2. Normalize transport values in
   `rpent/robots/components/action_model_protocol.py`.
3. Query and cache `action_model.capabilities` before prediction.
4. Reject unsupported embodiments and mismatched action dimensions/schemas.
5. Decode `action_model.predict` into `ActionModelPrediction`; reject empty,
   non-2-D, NaN, or infinite actions and invalid values.
6. Keep future observations optional and out of Agent-facing tool text.

## Evidence

`tests/unit_tests/rpent/robots/components/test_action_model_protocol.py` passes.
