# 3.3.7

## Improvements

- Improve Content Cloud integration.

## Chore

- Upgrade of Elasticsearch to 9.x
- Internal updates for node.js and dependencies

# 3.3.6

## Chore

Dependency updates.

# 3.3.5

## Improvements

- Improve Content Cloud integration.
- Allow skipping the root translation in some cases.

## Bug fixes

- Misleading property path from nested validation errors.

# 3.3.4

## Features

- Update Content Cloud integration.
- Allow skipping syndication of the root translation for cross-market translation sharing.

## Fix

- Misleading property path in nested validation errors.
- Couldn't remove allowedLanguages filter from Flows.

# 3.3.3

## Features

- Update Content Cloud integration.

# 3.3.2

## Features

- Update Content Cloud integration.

## Bug fixes

- Allow higher check-in margins to avoid incorrect monitoring failure.
- Expect uuid to be empty for newly registered sites, allowing for immediate config updates.

## Chore
- Log site config updates in more detail.

# 3.3.1

*internal release*

# 3.3.0

## Features

- Added webhooks / notifications.

## Improvements

- Improved logging to log all critical CRUD operations.
- Allow requesting site statuses in multiple requests to improve performance and avoid timeouts.
- Delete authentication data immediately when a site is deactivated.

## Chore

- Update node.js version.
- Remove npm from Docker build.

# 3.2.4

## Bug fixes
- Update failure for one site lead to a failed cron job.
- Increase timeout to wait for log messages to be processed for cron jobs.
- Translations weren't deleted at target sites if only the translation deletion was pushed.
- Ignore additional properties when pushing content to target sites so it passes validation.
- Skip validation when cloning existing translations as the entity type definition may have changed in the meantime.

# 3.2.3
*Internal release to update Content Cloud integration.*

# 3.2.2

*Internal release to update Content Cloud integration.*

# 3.2.1

## Features
- Site: store available languages and default language per site.
- Previews: use "processed" text from Drupal when available.

## Chore
- Update Content Cloud integration.
- Update domain from cms-content-sync.io to content-sync.io.

# 3.2.0

## Features
- Authentication: use feature flag to determine session lifespan.
- Authentication: allow session lifespans of at least 5m, set by console command.
- Syndication / Entity Status: show up to 10 items in the syndication history for the entity status tab.
- Syndication / Pull: validate that the expected entity type version can be used for the provided entity.
- Syndication / Pull: save update + ignore count per operation if provided.
- Syndication / Flow: allow optional language filter.
- Site / Registration: immediately request feature flags after registration.

## Bug fixes
- Syndication: recreate items after deletion even when they have the same version id.

## Chore
- Update all upstream services to their latest stable version.
- Update Node.js to v22 and update all dependencies to their latest stable version.
- Cron: make it easier to recover from failed runs by providing go-back-hours option.
- Avoid unsolicited and unnecessary warnings from NestJS.

# 3.1.6
- fix: startup issues for cron jobs without dummy data.

# 3.1.5
- fix: avoid error when using cross-syncing for individual translations.
- fix: syndication was set as successful too early for non-root translations.
- chore: upgrading Node.js to the newest LTS version v20.
- chore: upgrading dependencies to new major versions.

# 3.1.4
- chore: internal release for content cloud.

# 3.1.3
- feat: enable new feature flags for improved functionality and performance by default for: prefer-2xx-status-code, request-per-translation, skip-unchanged-translations, skip-unchanged-entities.
- fix: add workaround for corrupted entity data.

# 3.1.2
- chore: only set high priority for pushing operations, not pulling operations.
- chore: update Sentry library and integration; use Sentry cron monitoring when available.
- chore: improve pull dashboard performance.
- chore: allow database updates to take longer than 60 seconds.

# 3.1.1
- feat: allow deleting individual translations of content.
- feat: allow requesting entity types with properties for a new, interactive content diff view.
- fix: error when providing different translations of the same entity from multiple sources.
- chore: database update to improve querying for content sources.

# 3.1.0
- feat: allow providing an update priority. Prioritized updates are processed earlier and retried more often and in quicker succession.
- feat: clear the entity cache with a follow-up request for highly critical updates.
- feat: mark entities as incomplete until all translations are available.
- feat: make translation handling more resilient to avoid deleting translations in edge cases.
- feat: make requests to the backend more resilient by auto-retrying them.
- fix: more reliably cancel follow-up tasks.
- fix: use better message id format for indexing.
- fix: expect the wrong dead letter queue name to be used in an older release.

# 3.0.19
- feat: improve performance of pull dashboard requests.
- fix: add feature flag to mark "update tracing" as an available feature.
- fix: expect errors for entity type versions in Flow configs.
- fix: update list-task status reliably for mappings.
- chore: improve performance of syndication list interface.

# 3.0.18
- feat: save Drupal's requirements per site.
- feat: expose a site's rest urls to the frontend to identify capabilities.
- feat: fail faster during config exports to not block CI jobs.
- feat: allow embedded entities and references to provide a view URL.
- feat: allow tracing updates and future updates.
- feat: provide better feedback for failed updates when e.g. the validation failed.
- feat: handle wrong view URL from crop entities gracefully.
- feat: propagate update failures to dependent updates.
- feat: allow more efficient JSON bodies for forced 2xx responses.
- feat: provide information for per-language requests of updates.
- fix: expect files to have been deleted before.
- fix: version hash for entities was sometimes updated when data didn't change.
- fix: deletion of embedded entities wasn't propagated sometimes.

# 3.0.17
- fix: avoid having the root language twice for an update, leading to double updates and deleted translations.
- fix: expect null values for reference fields.

# 3.0.16
- fix: didn't load the referenced entity correctly during some updates.

# 3.0.15
- feat: add "self" interface for sites for quick access to core details.
- feat: allow providing descriptions for types and properties.
- feat: add "published" property to all content.
- fix: mapping didn't work for sites that didn't have the module installed before.
- fix: files were re-used between sites when they should be independent.
- chore: improve syndication list performance.

# 3.0.14
- feat: allow running updates in a specific sequence.
- fix: expect empty reference values at content.

# 3.0.13
- feat: allow sites to provide their config asynchronously. About 5x faster than before.

# 3.0.12
*internal release*

# 3.0.11
*internal release*

# 3.0.10
- fix: auth cache wasn't cleared when re-registering a site.
- fix: re-run/retry of aborted/failed updates sometimes didn't work.
- chore: remove redundant logging when aborting updates.

# 3.0.9
*internal release*

# 3.0.8
- feat: send file details along with entity update requests to improve performance.

# 3.0.7
- feat: allow multiple DNS check endpoints to improve robustness.
- feat: keep full syndication history for 3 months by default.
- chore: add more logging for when syndications are aborted.

# 3.0.6
- feat: allow sites to only respond with 2xx status codes even on failures to bypass CDN rules.
- fix: pagination for migrations was bugged.

# 3.0.5
- feat: improve sentry context data.
- feat: don't send large "embed"/"properties" properties to the frontend at the pull dashboard to improve erformance.
- fix: improve version id that's generated for entities.

# 3.0.4
- feat: add performance profiling for messages.
- chore: improve auto-generated previews.

# 3.0.3
- feat: add auto-generated preview for all entities.
- feat: profile all database and elasticsearch transactions.
- feat: save migration type per syndication to improve performance.
- fix: avoid error when JWT can't be parsed.
- fix: avoid hitting rss limit too soon in the health checks.
- chore: log site request timeouts correctly.

# 3.0.2
- feat: add new database indices for syndication entities.

# 3.0.1
fix: the skip-unchanged flag was sometimes set incorrectly.

# 3.0.0

**THIS UPDATE REQUIRES YOU TO RUN `sync-core install update` AND UPDATE UPSTREAM SERVICES TO NEW MAJOR VERSIONS**

- feat: allow connecting local sites that poll for requests instead.
- feat: automatically deactivate local sites after 30 days of inactivity instead of 3.
- feat: report last activity of sites to the backend.
- feat: use mongodb v6.0 and elasticsearch v8.8.
- feat: allow running requests manually through the CLI for debugging.
- feat: add new properties status and appType to projects.
- feat: allow searching for content by their UUID and Unique ID in the pull dashboard.
- feat: check queue size before adding new messages to avoid overload from too many large, simultaneous requests (requires management URL to be provided).
- feat: allow providing auth details immediately during site registration.
- feat: allow only updating changed translations when using request-per-translation flag.
- feat: allow skipping unchanged, embedded entities during updates to favor performance over reliability.
- feat: capture more update stats like request times and full duration.
- feat: provide new boolean flags for updates to improve performance.
- fix: ensure auth cache is cleared when sites are re-registered.
- fix: guess file mimetype based on physical file name, not human-entered name.
- fix: avoid immediate retries after aborts breaking updates.
- fix: make entity hashes more reliable to use for versioning.
- fix: updates sometimes didn't show correctly in the entity status view.
- fix: cancel dependent updates when the root update is aborted or fails.
- fix: re-running an update sometimes did nothing.
- fix: log timeouts for update operations correctly.
- chore: order update statuses in migrations for UI.
- chore: always log site deactivations.
- chore: use better database index for message removal in broker.
- chore: add new indices for update dashboard.
- chore: save site secret only at once place.
- chore: allow providing UUID for console feature commands.

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
