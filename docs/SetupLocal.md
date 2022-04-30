---
title: Local Setup
parent: Setup
has_children: false
nav_order: 1
---

# Local Setup

If you want to use the Sync Core locally for development, the Sync Core and the sites must be in the same Docker network.
You have two options to achieve this:
- You can copy the services and volumes from our `docker-compose.yml` into your own `docker-compose.yml` file that also includes your websites.
  - If you are exposing port 8080 for your own sites, you can remove the port exposure from the Sync Core or choose a different port to expose the Sync Core at.
  - The disadvantage is that if anything changes in this stack, you must copy the changes to your own stack.
- You can connect the networks of this stack to your own website Docker stack.
  - The easiest way to accomplish this is to reference your existing network in this stack. Please checkout the `docker-compose.yml`'s `networks` section for details.
  - The disadvantage is that if you are using identical services you may get conflicts with host names.

## Environment variables

Copy the `.env.template` file to `.env`. Go through the `.env` file and add all required values.

**IMPORTANT: Don't use the same Sync Core UUID and secret for two different setups! This can lead to
all kinds of hard-to-debug issues. Using the same Sync Core UUID and secret for your production environment
and any other environment can lead to the production environment shutting down completely due to how our licensing and
abuse detection works.**



## Start the services locally

Run:
```bash
docker-compose up -d
```

## Setup database and search index

Run in a fresh or existing sync-core container:
```bash
# This will setup the database with all required indices.
sync-core install new

# This will create the index in Elasticsearch.
sync-core-previews create-index
```

If you want to test this locally, just prepend `docker-compose exec sync-core ` to the commands.

## Verify the setup

View the logs of the `sync-core` container and the `sync-core-broker` container:
- verify there are no errors shown
- verify that you see a "Successfully verified Sync Core token and secret for \[...]\" message
- verify that you see a "\[...\] successfully started" message

Run the health checks manually for all containers (see below).

## Debug startup issues

**Empty logs**

Sometimes Docker can shutdown your container before the log out put has been fully processed.
So if your container restarts but you don't see any error message, try starting the service manually to get all log output:
```bash
docker-compose run --rm sync-core
```

**RabbitMQ not ready**

Sometimes the RabbitMQ container doesn't accept connections yet even if the health checks succeeds.
Just restart the sync-core* containers if this happens.

# Health checks (readiness + liveness)

For Kubernetes and OpenShift we provide health checks for all containers.
We recommend you test that all health checks return "ok" locally either way.

## Verify health checks locally

All readiness and liveness checks are expected to return `{"status": "ok", ...}`. HTTP checks
will return status code 200.

```bash
# Verify REST is ready.
curl -f localhost:8080/sync-core/healthz/ready
curl -f localhost:8080/sync-core/healthz/live

# Verify the broker is operational.
docker-compose exec sync-core-broker curl -f localhost:8080/sync-core/healthz/ready
docker-compose exec sync-core-broker curl -f localhost:8080/sync-core/healthz/live

# If you have divided the Sync Core into "rest" and "queue", you can test the queue like:
#docker-compose exec sync-core-queue curl -f localhost:8080/sync-core/healthz/ready
#docker-compose exec sync-core-queue curl -f localhost:8080/sync-core/healthz/live
```
