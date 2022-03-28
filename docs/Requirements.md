---
title: Requirements
has_children: false
nav_order: 2
---

# Requirements

*Note: The upstream services are included in this repository for a local setup.*

- A Docker v19 environment with at least 1 GB RAM and 1 CPU.
- Upstream services:
  - MongoDB 4.4.
  - RabbitMQ 3.8.
  - elasticsearch: 7.12.
  - S3 bucket -OR- file system access.
  - Any cron / scheduler.
- Network rules must:
  - Allow GET, POST, PUT, DELETE between the Sync Core and the connected sites and vice versa.
  - Allow POST and GET from the Sync Core to api.cms-content-sync.io.
- The Drupal module may not be older than 6 months compared to the Sync Core version.
- The Sync Core must be accessible by all your backend site users that are using Content Sync.
  - The Sync Core can still be private, but users must have direct HTTP/HTTPS access to it.
- We provide you credentials to pull the Sync Core from our private Docker registry. The Sync Core may not be shared
  publicly and only stored at secured registries.

As the Sync Core runs as a horizontally scalable cluster, we don't recommend running multiple Sync Cores simultaneously unless you want to have a separate Sync Core for testing.

## Compatibility

Please note that we're only testing the Sync Core with *real* MongoDB servers and not substitute services like AWS DocumentDB or Azure CosmosDB. CosmosDB has known restrictions on indexes that make it incompatible with Content Sync; you will receive an error like this during start-up:
> MongoError: Unique and compound indexes do not support nested paths.

## Optional
- If you would like to follow your own deployment workflow, you can pull the image and securely store it in your own private registry with your own release tags.
- Store the logs centrally so you can easily access them.
- Secrets are passed as environment variables, so you should preferably use a vault.
- A load balancer if you would like to scale horizontally (strongly recommended).
- Use templates to manage your setup like CFT on AWS or Kubernetes.
