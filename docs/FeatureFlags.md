---
title: Feature Flags
has_children: false
nav_order: 5
---

# Feature Flags

## List of Feature Flags

### Search
- `previews` *static*: Whether search / previews are available. Set to `1` if an `ELASTICSEARCH_URL` is provided and `0` if not.

### Logging
- `log:*` default `0`: Log **everything** (see below). Use with caution as this can create massive amounts of logs.
- `log:Backend` default `0`: Enable advanced logging for the communication with the licensing backend.
- `log:SiteRequest` default `0`: Enable advanced logging for all requests to sites.
- `log:Files` default `0`: Enable advanced logging for files.
- `log:Previews` default `0`: Enable advanced logging for preview indexing and searching.
- `log:Syndication` default `0`: Enable advanced logging for the whole update / syndication process. Try to use this flag narrowly, e.g. only per site.

### Security
- `authentication:session-lifespan` default `86400` (1 day): How long a session lasts (cookie TTL) in seconds before the Sync Core logs in again to the site. Can be customized per project and site as well.

### Private (and local) environments
- `site:request-polling:request-lifetime` default `86400` (1 day): How long the Sync Core waits for a request to be polled by the site before marking it failed in seconds.

### Backend (licensing) request throttling
- `throttling:backend-request:per-minute` default `5`: How many requests to send to the Content Sync backend per cron run.
- `throttling:backend-request:in-parallel` default `5`: How many requests to send to the Content Sync backend in parallel.
- `throttling:internal:per-minute` default `10`: How many internal messages to process in parallel e.g. to delete files.

### Upgrades
- `entity:latest` default `1`: Cache the latest entity revision separately to speed up syndications. Enabled by default in newer installations.

## Manage Feature Flags

You can enable Feature Flags using the environment variable `FEATURE_FLAGS` and providing a comma-separated list or by using the console command `sync-core features <enable|disable> <name> [type] [id]`.

## Dynamic usage (CLI)

To list available flags, and their current values in your installed Sync Core, use `sync-core features list`. To get the settings for an individual flag, use `sync-core features get <name> [type] [id]`.

To revert to the defaults, use `sync-core features unset <name> [type] [id]`.

## Narrow usage

Narrow down the usage of certain features to avoid an unnecessary performance impact. The `type` argument can be one of the following (defaults to `sync-core`):

- `sync-core`: The whole Sync Core, so global (default).
- `contract`: Enable/Disable the feature for the given contract.
- `project`: Enable/Disable the feature for the given project.
- `site`: Enable/Disable the feature for the given site.

All types except for the sync-core require an ID to be provided. You can get the ID from the MongoDB collections manually if needed.
