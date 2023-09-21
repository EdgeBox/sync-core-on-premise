---
title: Upgrade
has_children: false
nav_order: 7
---

# Upgrade to v3

## MongoDB

We are now using version 6.0 of MongoDB. You first need to upgrade to 5.0 before upgrading to 6.0. If you are using a self-hosted MongoDB, you also have to adjust the feature compatibility version after each update:
1. Update MongoDB to v5.0, then run: `db.adminCommand( { setFeatureCompatibilityVersion: "5.0" } )`
2. Update MongoDB to v6.0, then run: `db.adminCommand( { setFeatureCompatibilityVersion: "6.0" } )`
 
Please also note that the `mongo` CLI command is now `mongosh`, in case you are using it for health checks.

After a successful update, you can remove the following indices manually that won't be used moving forward:
- remote_entity_revision_entity
  - entityrev_custproj_type_uuid
  - entityrev_custproj_type_uniqid
- latest_remote_entity_revision_entity
  - lentityrev_custproj_type_uuid
  - lentityrev_custproj_type_uniqid
- syndication_entity
  - syndication_custproj_site_errors

## Elasticsearch

We are now using version 8.8 of Elasticsearch. You first need to upgrade to 7.17 before upgrading to 8.8.

## RabbitMQ

We are now using version 3.12 of RabbitMQ. Please check the RabbitMQ documentation for required version steps as that also depends on your installed Erlang version.

## Migrate data

If you haven't done so when updating to v2.5, please also enable this feature that is now required:
```
sync-core features enable entity:latest
```

Some of the new v3 features require additional, calculated data. After updating the cluster to v3, please run in a Queue container:
```
sync-core install update
```

Before running this, some of the UI elements may not display properly for users.

## Large clusters only

If you run a larger cluster with 100+ sites, please enable throttling for backend requests:
```
sync-core features set throttling:backend-request:in-parallel 5
sync-core features set throttling:backend-request:per-minute 5
sync-core features set throttling:internal:in-parallel 10
sync-core features set throttling:internal:per-minute 15
```
