# WAM and Pi0.5 LIBERO pilot

Updated: 2026-09-27

This pilot compares the Cosmos Policy WAM path with the Pi0.5 baseline on 15
fixed task and seed pairs. Each episode uses the same frozen read-only memory,
the environment's exact task language, and the task JSON `terminated` field as
the success criterion.

## Results

| Suite and task | WAM | Pi0.5 |
| --- | ---: | ---: |
| `libero_spatial/task0` | 5/5 | 1/5 |
| `libero_10/task5` | 5/5 | 3/5 |
| `libero_object/task0` | 4/5 | 4/5 |
| **Overall** | **14/15 (93.33%)** | **8/15 (53.33%)** |

Across paired outcomes, WAM wins 7 pairs, Pi0.5 wins 1 pair, and 7 pairs are
tied. The two-sided exact McNemar test over the eight discordant pairs is
`p=0.0703`. Wilson 95% intervals are `[0.702, 0.988]` for WAM and
`[0.301, 0.752]` for Pi0.5. These are descriptive pilot results rather than a
benchmark claim.

## Evaluation contract

- 3 suites/tasks × 5 seeds × 2 methods = 30 episodes.
- WAM episodes use only `wam_act`.
- Pi0.5 episodes use only `pi0_pick` and `pi0_doubled`.
- Only the current session's exact JSON with a top-level boolean `terminated`
  is included.
- Duplicate records, mixed-method sessions, metadata files, and sessions
  without a task result are excluded.

The complete local audit and statistical report are kept outside Git at:

- `/tmp/paired_pilot_audit_20260927.csv`
- `/tmp/paired_pilot_audit_20260927.md`
- `/home/gao/worldmodel/harnessvla/2026-09-27-wam-pilot-report.md`

Per-task Wilson intervals and paired exact McNemar results are included in the
report. Native DreamZero validation remains separate from this pilot.
