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
