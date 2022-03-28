# 2.1.1
- **fix: fix translations of embedded entities sometimes not being sent.**
- fix: some readiness checks were used as liveness checks.

# 2.1.0
- **feat: allow rest-based health checks on queue services as well to avoid unreliable health check results.**
  - *please update your health checks! for details please consult the setup documentation.*
- feat: use new alpine-based NodeJS base image.
- feat: allow increased RAM and CPU usage before failing health checks.
- feat: add scheduler script to replace Kubernetes cron jobs on slow-provisioning systems like Fargate.
- feat: decrease image size by using multi-layer builds.
- feat: add bash, vim and curl to the image.
- feat: move some internal log messages to `verbose` type.
- feat: remove CLI coloring in logs.
- feat: allow using `json` format for logs.
- feat: add `sync-core` and `sync-core-broker` scripts to the container CLI.
- feat: allow requesting auth headers via CLI for manual request debugging per site.