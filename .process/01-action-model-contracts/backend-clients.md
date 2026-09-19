# Backend client flow

## Operation

1. `BaseActionModelClient` owns RPC timeouts, capability caching, request
   normalization, prediction decoding, and metadata consistency checks.
2. `CosmosPolicyClient` requires backend identity `cosmos_policy`.
3. `DreamZeroClient` requires backend identity `dreamzero` and never fabricates
   LIBERO compatibility.
4. Runtime code performs environment-specific compatibility checks before it
   exposes an executable tool.

## Evidence

Client caching, backend identity, and malformed response behavior are covered by
the action-model protocol test module.
