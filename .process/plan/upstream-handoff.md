# Cosmos Policy upstream handoff plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans or superpowers:subagent-driven-development to execute this plan task-by-task. Keep each checkbox and evidence link current.

**Goal:** Prepare the validated Cosmos Policy WAM integration for review and
submission to `RLinf/RPent:main` while preserving the existing DreamZero bridge
and its explicit DROID-only rejection in LIBERO.

**Architecture:** Keep the generic action-model protocol, LIBERO `wam_act`
runtime, Cosmos bridge, and DreamZero bridge as separate optional components.
Cosmos remains the native LIBERO backend (`libero_7d`, 7D OSC); DreamZero
remains the native DROID backend (`droid_joint_position_8d`, 8D joint-position)
and is not removed or adapted in this handoff.

**Tech Stack:** Python, NumPy, RPent RPC, Cosmos Policy Predict2, LIBERO,
pytest, Ruff, pre-commit, GitHub pull request workflow.

**Spec:** `/home/gao/worldmodel/harnessvla/2026-09-16-wam-integration-design.md`,
plus `WAM_AGENT.md` and the stage records under `.process/`.

## Global Constraints

- Keep WAM optional: without both `--wam-backend` and `--wam-endpoint`, the existing runtime remains unchanged.
- Keep Cosmos dependencies in `/home/gao/worldmodel/harnessvla/cosmos-policy/.venv`; do not install CUDA dependencies into RPent's main environment.
- Do not commit checkpoints, Hugging Face tokens, caches, logs, rollouts, or generated artifacts.
- Preserve the exact LIBERO 7D OSC schema and DreamZero 8D DROID schema; do not pad, truncate, or reinterpret actions.
- Keep English and Chinese user documentation paired for any changed public behavior.
- Record native inference separately from benchmark task success.

## Review Focus

- Native Cosmos startup must use the isolated environment and local checkpoint paths, with the auxiliary Hugging Face asset authenticated and available.
- Capabilities must advertise `libero_7d`, action dimension 7, and the exact `LIBERO_ACTION_SCHEMA` before a runtime connects.
- A prediction must return finite `[T, 7]` actions and preserve future observation/value fields without changing the protocol.
- A bounded `wam_act` run must show action execution, artifacts, and owned-process cleanup; a clean process exit alone is insufficient.
- DreamZero must remain discoverable as a bridge but must continue to fail LIBERO compatibility checks before environment execution.

### Task 1: Add optional RPent-owned Cosmos bridge lifecycle

**Files:**
- Modify: `robots/libero/robot_spec.py`
- Modify: `robots/libero/robot_spec.py` CLI arguments and runtime wiring
- Test: `tests/unit_tests/robots/libero/test_libero_wam_runtime.py`
- Test: `tests/unit_tests/rpent/robots/components/test_wam_bridges.py`

**Interfaces:**
- Consumes: the existing Cosmos bridge command, local checkpoint/cache paths,
  and the current `--wam-endpoint` connection mode.
- Produces: an optional `--wam-checkpoint`/environment-driven launcher that
  starts Cosmos in its isolated environment, waits for `healthz` and
  capabilities, and stops the owned process on Dashboard cleanup.

- [x] **Step 1: Preserve and test external endpoint mode**

  Keep `--wam-endpoint` behavior unchanged and add a regression test proving
  that an externally supplied endpoint does not create an owned daemon.

- [x] **Step 2: Add the isolated bridge starter**

  Build the bridge command with the official Cosmos Python executable, local
  checkpoint directory, `HF_HOME`/`HF_HUB_CACHE`, `CUDA_HOME`,
  `COSMOS_INTERNAL=1`, and RPent `PYTHONPATH`. Allocate a free localhost port,
  create a `ProcessDaemon`, and return its RPC client through the same
  `try_spawn_server`/`try_wait_server` lifecycle used by VLA and SAM3.

- [x] **Step 3: Validate capabilities before exposing `wam_act`**

  Connect the client, require the exact `libero_7d` 7D schema, and fail the
  component with a useful Dashboard error if the bridge cannot start or
  advertises a different embodiment.

- [x] **Step 4: Run focused lifecycle tests**

  Run:

  ```bash
  pytest tests/unit_tests/robots/libero/test_libero_wam_runtime.py \
    tests/unit_tests/rpent/robots/components/test_wam_bridges.py -v
  ```

  Expected: external endpoint and owned-process paths both pass, including
  cleanup and DreamZero incompatibility checks.

### Task 2: Reconcile native Cosmos evidence

**Files:**
- Modify: `.process/03-native-bridges/cosmos-policy.md`
- Modify: `.process/04-release-validation/validation.md`
- Modify: `PROCESS.md`
- Modify: `handoff.md`

**Interfaces:**
- Consumes: the completed native validation commands, outputs, artifact paths,
  and cleanup evidence supplied by the operator.
- Produces: synchronized process records that state the exact environment,
  bridge capabilities, prediction, bounded LIBERO run, and any remaining
  limitation.

- [ ] **Step 1: Capture the exact native evidence**

  Record the Cosmos checkout revision, RPent revision, checkpoint directory,
  `CUDA_HOME`/`HF_HOME` values, and the exact commands used for the bridge,
  capabilities request, prediction, and bounded `wam_act` run. Keep tokens and
  generated outputs out of the repository.

- [ ] **Step 2: Update the level-3 records**

  Replace stale “Torch missing” or “403 blocked” statements only when the
  corresponding successful command output and artifact path are available.
  Keep DreamZero’s native-runtime status unchanged and state that its LIBERO
  rejection remains intentional.

- [ ] **Step 3: Propagate only status changes upward**

  Update `.process/04-release-validation/PROCESS.md` and the root
  `PROCESS.md` with concise evidence links and the next handoff task; leave
  detailed commands in the level-3 records.

### Task 3: Run the release checks on the handoff tree

**Files:**
- Test: `tests/unit_tests/`
- Check: all tracked files through pre-commit

**Interfaces:**
- Consumes: the complete WAM tree plus reconciled process records from Task 1.
- Produces: reproducible offline and formatting evidence for the pull request.

- [ ] **Step 1: Run focused WAM checks**

  Run:

  ```bash
  pytest tests/unit_tests/rpent/robots/components/test_action_model_protocol.py \
    tests/unit_tests/rpent/robots/components/test_wam_bridges.py \
    tests/unit_tests/robots/libero/test_libero_wam.py \
    tests/unit_tests/robots/libero/test_libero_wam_runtime.py -v
  ```

  Expected: all focused action-model, bridge, and LIBERO compatibility tests
  pass, including the DreamZero rejection test.

- [ ] **Step 2: Run the complete offline suite**

  Run:

  ```bash
  pytest tests/unit_tests -v
  ```

  Expected: no failures; record the exact pass/skip counts in
  `.process/04-release-validation/validation.md`.

- [ ] **Step 3: Run repository hooks and inspect edits**

  Run:

  ```bash
  pre-commit run --all-files
  git diff --check
  ```

  Expected: hooks pass without introducing unreviewed changes. If a hook edits
  a file, inspect the diff and rerun the affected checks.

### Task 4: Commit and submit the upstream handoff

**Files:**
- Commit: all intended WAM implementation, tests, documentation, and process records
- Exclude: checkpoints, credentials, caches, logs, rollouts, and unrelated changes

**Interfaces:**
- Consumes: a clean, verified branch based on `upstream/main`.
- Produces: a pushed topic branch and a pull request targeting `RLinf/RPent:main`.

- [ ] **Step 1: Review the complete diff and repository status**

  Run:

  ```bash
  git status --short
  git diff --stat upstream/main...HEAD
  git diff --check
  ```

  Confirm that DreamZero files remain present, no generated asset is tracked,
  and the diff is limited to the WAM integration and its records.

- [ ] **Step 2: Commit with a Conventional Commit message**

  Use a focused message such as:

  ```text
  feat(wam): add optional Cosmos and DreamZero action-model bridges
  ```

  Include the native validation evidence in the commit or its process record,
  not in the commit message alone.

- [ ] **Step 3: Push the topic branch**

  Run:

  ```bash
  git push -u origin feat/add-wam-module
  ```

- [ ] **Step 4: Open the pull request**

  Target `RLinf/RPent:main`. The description must include the problem,
  architecture, optional-dependency isolation, focused/full test commands,
  native Cosmos commands and artifacts, the explicit DreamZero/DROID boundary,
  and the fact that native validation is separate from benchmark success.

- [ ] **Step 5: Record the handoff result**

  Update `.process/plan/PROCESS.md` and root `PROCESS.md` with the PR URL or
  review state, then leave the branch available for review feedback.
