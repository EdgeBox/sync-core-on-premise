---
title: On-premise Setup
parent: Setup
has_children: false
nav_order: 2
---

# Setup on-premise

## Upstream services

Setup the required services and provide their URLs as environment variables.

You can attach a volume to the sync-core container at `/usr/src/files` or you can use S3 for file storage.
Using S3 has the advantage that the Sync Core can focus more on syndication and requires less resources for file distribution.
The S3 bucket can be private or public.
The Sync Core will sign URLs that connected sites can use to upload and download individual files.

To use S3, set the `LOCAL_FILESYSTEM` environment variable to "no" and provide all four AWS environment variables.
If you are using a local filesystem, you can leave the AWS environment variables empty or not provide them at all.

## Containers

Setup either sync-core + sync-core-broker -OR- sync-core-rest + sync-core-queue + sync-core-broker using the docker-compose file as a blueprint.

Adjust the environment variables to contain the secrets and external service URLs that are required to run.

Include the health checks if you are using Kubernetes or OpenShift (see below for details).

You can enable auto scaling and redundancy for all two/three Sync Core services.

## Configure readiness / liveness checks

You can use curl inside the container to run health, e.g.
- Readiness: `curl --fail localhost:8080/sync-core/healthz/ready`
- Liveness: `curl --fail localhost:8080/sync-core/healthz/live`

### Readiness

Endpoint:
- Port: 8080
- Path: /sync-core/healthz/ready

We test with the following probe config:
```yaml
failureThreshold: 2
initialDelaySeconds: 10
periodSeconds: 10
successThreshold: 2
timeoutSeconds: 5
```

You can adjust the config to match your own best practices.

### Liveness

Endpoint:
- Port: 8080
- Path: /sync-core/healthz/live

We test with the following probe config:
```yaml
failureThreshold: 18
initialDelaySeconds: 30
periodSeconds: 10
successThreshold: 1
timeoutSeconds: 5
```

To avoid dropping requests, we recommend running `sleep 5` as a `preStop` lifecycle hook for the `sync-core` / `sync-core-rest` service.

You can adjust the config to match your own best practices.

## HTTPS encryption

We strongly recommend you setup HTTPS to encrypt the traffic for all production environments.
The most reliable way to accomplish this is to add a load balancer in front of the Sync Core that handles SSL offloading.
The Sync Core accepts proxy requests by default.

## Cron

The cron containers must receive the same environment variables as the service containers.
If you are using rest+queue separately, replace `sync-core` with `sync-core-queue` below.

Create the following two cron jobs:
- Every hour at minute 0 (sync-core): sync-core syndication update-stats
- Every minute (ideally close to second 0) (sync-core-broker): sync-core-broker messages cron

**Caution: The message broker cron job is NOT expected to be retried on failure as jobs are not expected to overlap.**

All other cron jobs can be retried on failure.

**Caution: Some cron jobs are expected to run early every minute, so you need a pod scheduler that has new pods up and running within 15s.**
**If you are using a slow scheduler like Fargate please reach out to us for alternative cron job scheduling options.**

## Logging

If you are using a central log management, you can add an alert for the "Error" keyword.
Please note that syndication errors are expected so they won't be logged by default and are available via the Content Sync UI instead.
So by default only application/service errors will be logged.

## Monitoring

Apart monitoring that the health checks work inside your cluster we also recommend you setup HTTP/HTTPS monitoring to monitor the uptime from outside your cluster.
If your Sync Core is available at `https://sync-core.example.com`, please setup monitoring for `https://sync-core.example.com/sync-core/healthz/ready` that expects a 200 OK response as JSON.

## Setup database and search index

Run in a fresh or existing sync-core container:
```bash
# This will setup the database with all required indices.
sync-core install new

# This will create the index in Elasticsearch.
sync-core-previews create-index
```
