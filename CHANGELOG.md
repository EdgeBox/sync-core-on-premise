# 2.5.4
- feat: allow throttling backend requests.
- fix: make pool assignments to usage entities more reliable.
- fix: ensure sites can be re-activated after being marked as inactive automatically.

# 2.5.3
- feat: improved deletion speed.
- feat: allow sites to provide a "status" REST interface to asynchronously query for availability and enabled features.
- feat: allow more detailed entity type configurations to prepare for the Drupal module v3.
- feat: send the hourly usage stats through the queue to throttle requests to the licensing backend.
- feat: allow viewing serialized entities in a beautified format for debugging.
- feat: improve performance for saving Flows.
- fix: sometimes deleted syndications from the source site could lead to an error when viewing the synchronization dashboard.

# 2.5.2
- fix: many simultaneous, failing updates for the same entity could lead to a queue overload.

# 2.5.1
- feat: allow names for entities that are longer than 200 characters.
- feat: delete old syndication entities.
- feat: delete old migration entities.
- feat: delete unused files.
- fix: the number of entities displayed incorrectly at the backend.

# 2.5.0
- feat: add cache to improve performance of entity queries. after the update, please run once: sync-core features enable entity:latest
- feat: 404 responses now result in the status Aborted rather than Failed (so a soft / expected failure).

# 2.4.11
- fix: allow content names / titles of up to 1000 characters.

# 2.4.10
- feat: provide a CLI command to print site auth for debugging.
- fix: log request details when login request for cookie auth fails.

# 2.4.9
- feat: allow customizing the RAM limit for the health check using `HEALTH_MEMORY_LIMIT`.
- feat: send messages as *persistent* to RabbitMQ.
- fix: slow database query could lead to queue connection loss.

# 2.4.8
- fix: improve queue resilience for .ack'd messages and unresponsive RabbitMQ connections.
- fix: sometimes deletion requests were sent to sites that didn't have the entity.
- fix: re-authentication for cookie login sometimes failed when also using basic auth.

# 2.4.7
- feat: abort updates that are stuck, i.e. running for more than 48 hours.
- fix: abort whole chain of updates if the root update fails; only aborted the first in line until now.

# 2.4.6
- feat: allow basic auth protection for staging environments on top of regular authentication.
- feat: improve performance monitoring through Sentry.

# 2.4.5
- feat: allow pushing entities asynchronously to drastically improve the editor experience.
- feat: allow making one request per translation for sites using both many translations and many nested entities.
- feat: speed up dependent requests by not having to wait for the message cron to run.
- feat: allow updating the base URL of sites more easily.
- fix: for "pull changed" mass updates, respect the exact entity revision to save unnecessary updates.
- fix: when changing the default throttling for all sites, apply the new limit immediately.

# 2.4.4
- fix: translations sometimes weren't correctly synchronized with "Pull All".

# 2.4.3
- feat: allow syndicated deletions of embedded entities (usually files, media etc.).
- feat: allow sites to customize request timeouts from 5s to 120s.
- feat: allow throttling the number of messages processed per cron run by the broker using `MESSAGE_LIMIT_PER_CRON_RUN`.
- fix: avoid error for messages about failed syndications that were retried in the meantime.
- fix: reset authentication cache (sessions) when re-registering a site for cookie auth users.
- chore: improve performance of configuration exports from sites.
- chore: slightly decrease the number of requested entities per page from 25 to 20.

# 2.4.2
- feat: individually encrypt authentication secrets for connected sites.
  - you may want to provide a custom `AUTHENTICATION_ENCRYPTION_SECRET` environment variable using e.g. `openssl rand -hex 32`.
  - you may want to re-save all existing entries using `sync-core site-authentication resave`.
- fix: the Sync Core version in the log messages was always "dev" instead of the semver version (from v2.4.1).
- fix: many previous messages weren't showing in the logs (from v2.4.1).
- fix: improve config validation for the Sync Core UUID and secret.

# 2.4.1
- feat: always log REST request failures, including the response status code.
- feat: logs are contextually rich now, i.e. log additional related meta-data like the client's site ID or a syndication ID where available.
- feat: logs are non-blocking now, i.e. don't wait for log messages to be processed to increase performance of updates.

# 2.4.0
- **feat: allow aborting updates using the UI.**
- **feat: allow restarting updates using the UI.**
- feat: store entity view URL per translation to make use as canonical URL for SEO more reliable.
- feat: allow querying for all updates, including those from migrations to make the content dashboard UI view more usable.
- feat: save entity and pools reference after asynchronously pushing entity from a site, allowing to see the update status in the UI.
- fix: deactivate and re-activate sites automatically during the license check if their status changed in the backend.
- fix: don't count aborted updates as regular usage.
- stability: query for update status could return status for wrong entity if it shared the same UUID but had a different entity type.
- stability: never overwrite "aborted" status of updates in case of race conditions.

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
