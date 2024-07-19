---
title: Install Scripts
parent: Setup
has_children: false
nav_order: 4
---

# Install Scripts

## Setup database

Run in a fresh or existing sync-core container:
```bash
# This will setup the database with all required indices.
sync-core install new
```

## Setup search index

Run in a fresh or existing sync-core container:
```bash
# This will create the index in Elasticsearch.
sync-core-previews create-index
```
