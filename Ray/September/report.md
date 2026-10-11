# Ray Monthly Contribution Report — September 2026

**Contributor:** [@GNITOAHC](https://github.com/GNITOAHC) · **Repository:** [ray-project/ray](https://github.com/ray-project/ray) · **Area:** Ray Serve

## Summary

Two bug fixes merged in Ray Serve, both fixing failures where a request or the controller loop stalls with no error shown to the user.

| PR | Title | Status | Diff |
|----|-------|--------|------|
| [#66358](https://github.com/ray-project/ray/pull/66358) | [serve] Drop dead replicas from the multiplexed model ID mapping | Merged 2026-09-22 | +50 / -0 |
| [#66438](https://github.com/ray-project/ray/pull/66438) | [serve] Fix KeyError that stalls the application control loop when a deployment is removed | Merged 2026-09-24 | +118 / -0 |

## Details

### #66358 — Dead replicas in the multiplexed model mapping
- **Problem:** `on_replica_actor_died` did not clear `MultiplexMixin._multiplexed_model_id_to_replica_ids`. Until the controller sent a new replica set, the router could still pick a dead replica. That caused a `KeyError` in `pow_2_router.py`, which ended the routing task without notice and left requests unfulfilled.
- **Fix:** Also remove the dead replica from the multiplexed mapping, the same way locality sets are already handled. Unit tests added.

### #66438 — Controller stall after a deployment is removed
- **Problem:** `ApplicationAutoscalingState.get_decision_num_replicas` looked up every registered deployment in the caller's target map without checking that it was there. Removing a deployment from a running app raised a `KeyError`, which stalled the application control loop permanently.
- **Fix:** Skip deployments that are missing from the caller's map, matching the membership checks used elsewhere in the class. Regression tests added.

Note: #66433 was an earlier copy of #66438 and was closed in favor of it.
