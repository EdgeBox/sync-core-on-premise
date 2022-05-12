# 2.3.0
- **feat: allow re-assigning Pools and optionally deleting content that no longer matches any configured Pools.**
- fix: pull dashboard filter: entity type filter was sometimes ignored.
- fix: pull dashboard config: only show relevant entity types to filter by.

# 2.2.1
- **fix: avoid pull-migrations sometimes pulling outdated entity revisions.**
- fix: improve mongodb performance of pull-migrations for huge data sets (50k+ entities).

# 2.2.0
- **feat: allow dynamically assigning, un-assigning and re-assigning pools to content.**
  - *this will delete content if a pulling site no longer matches any of the pools of a content item after an update.*
- feat: `sync-core-previews` is now available as a slightly faster replacement for `yarn console-previews previews`.
- fix: content sometimes didn't show up in the pull dashboard, especially when pushing to multiple pools.
  - *if you are missing content in the pull dashboard before 2.2.0 or if you see duplicate content in the pull dashboard after upgrading to 2.2.0, please run `sync-core-previews reindex all`* 

# 2.1.3
- **fix: "pull all" sometimes pulled an older revision instead of the newest.**
- **fix: "pull all" sometimes didn't respect taxonomy term filters.**
- fix: when a pull starts, all running pull operations of the same entity to the same site are cancelled now.
- fix: console command output now has the new log format as well.
- fix: the error handler of console commands threw a follow-up error, hiding the original error.

# 2.1.2
- fix: subscriptions sometimes weren't refreshed automatically after renewal

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
