# WAM integration context

Read the repository's `AGENTS.md` first; this file only adds context for the
DreamZero and Cosmos Policy integration on `feat/add-wam-module`.
Then read `PROCESS.md` for the project-level implementation state. Read only the
linked stage and flow documents needed for the current task.

The repository's existing rules remain authoritative. Follow its current code
style, naming, module ownership, lazy optional imports, RPC/runtime patterns,
documentation conventions, and test placement. Read `CONTRIBUTING.md` and the
applicable `.agents/skills/` instructions before changing code. Keep English
and Chinese user documentation aligned, use offline CPU fakes for unit tests,
and do not weaken existing contracts or tests to accommodate WAM.

## Goal and architecture

- WAM backends are model components, not robot packages. Shared contracts and
  clients live in `rpent/robots/components/action_model_*.py`; native-runtime
  bridges live in `scripts/wam/` and run in each upstream model's own
  environment.
- LIBERO owns the execution adapter in `robots/libero/`: `robot_spec.py`
  attaches a configured bridge, `tools.py` builds the normalized observation
  and validates actions, and `toolkit.py` exposes `wam_act` only when configured.
- The executable path is Cosmos Policy Predict2 2B LIBERO with the exact
  `libero_7d` 7-D OSC schema. DreamZero-DROID exposes an 8-D joint-position
  contract and must not execute in LIBERO without a separately validated
  checkpoint or embodiment adapter.
- Keep WAM optional: without both `--wam-backend` and `--wam-endpoint`, existing
  runtime components, prompts, and tools must remain unchanged. Do not install
  either model's CUDA dependencies into RPent's main environment.

## Where to start

1. Read `../2026-09-16-wam-integration-design.md` when working in the current
   harness workspace. It is intentionally outside the RPent repository.
2. Read `rpent/robots/components/action_model_protocol.py` and the two backend
   clients before changing the wire contract.
3. Read `robots/libero/robot_spec.py`, `tools.py`, and `toolkit.py` before
   changing runtime or tool exposure.
4. Inspect `scripts/wam/` in the corresponding official model environment
   before changing upstream-specific preprocessing.
5. Start validation with the action-model component tests and
   `tests/unit_tests/robots/libero/test_libero_wam*.py`. GPU/model E2E evidence
   is separate from offline runtime-contract tests.

## Working agreement

- Preserve capability, embodiment, action-schema, shape, and finite-value
  checks before environment execution.
- Do not silently truncate, pad, or reinterpret DreamZero-DROID actions as
  LIBERO actions.
- Keep future images out of Agent tool-result text.
- Format and lint with the repository's configured Ruff/pre-commit setup. Run
  focused tests while developing and the relevant full repository checks before
  handoff. Report missing GPU/checkpoint validation separately from test failures.
- Use Conventional Commits and preserve unrelated worktree changes. Do not add
  checkpoints, generated rollouts, logs, credentials, or upstream CUDA
  dependencies to the RPent repository.
- After each coherent round, run focused tests plus the relevant repository
  checks, update the relevant `.process/` flow plus `PROCESS.md`, commit the changes, and push the current
  branch to `origin`.
