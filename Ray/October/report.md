# Ray Monthly Contribution Report — October 2026

**Contributor:** [@GNITOAHC](https://github.com/GNITOAHC) · **Repository:** [ray-project/ray](https://github.com/ray-project/ray) · **Area:** Ray Serve
**Period covered:** 2026-10-01 to 2026-10-11 (month in progress)

## Summary

One bug fix merged and one PR under review, both in Ray Serve: one for custom metrics and one for reporting deployment health.

| PR | Title | Status | Diff |
|----|-------|--------|------|
| [#66616](https://github.com/ray-project/ray/pull/66616) | [serve] Fix TypeError when recording a custom metric with a route tag | Merged 2026-10-03 | +97 / -14 |
| [#66910](https://github.com/ray-project/ray/pull/66910) | [serve] Surface terminal deployment actor failures outside UPDATING | Open (in review) | +119 / -78 |

## Details

### #66616 — Custom metrics with a `route` tag
- **Problem:** `ray.serve.metrics.Counter/Gauge/Histogram` raised `TypeError: argument of type 'NoneType' is not iterable` when `"route"` was in `tag_keys` and the metric was recorded without `tags`, because `tags=None` reached a `ROUTE_TAG not in tags` check.
- **Fix:** Handle `tags=None` in `inc()`, `set()` and `observe()`. Tests added. (Opened 2026-09-30.)

### #66910 — Report terminal actor failures (in review)
- **Problem:** When a replica actor failed its health check and every recreation attempt failed, the deployment stayed at `UNHEALTHY` with a "Retrying" message forever, so users had no sign that they needed to redeploy. There were two causes:
  - `DeploymentStatusInfo.handle_transition` handled `DEPLOYMENT_ACTOR_FAILED` only in the `UPDATING` and `DEPLOY_FAILED` states.
  - `DeploymentState.check_curr_status` raised the trigger only when no replica was running at the target version.
- **Fix:** Handle the trigger in every state and check for terminal failure even while some replicas are running. Also removes two unused, broken helpers from `ServeControllerClient`.

Note: #66904 was closed and replaced by #66910 because its branch contained unrelated changes.
