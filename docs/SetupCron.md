---
title: Cron Setup
parent: Setup
has_children: false
nav_order: 3
---

# Setup Cron

## Environment

The cron containers must receive the same environment variables as the service containers.

## Required cron jobs

Create the following two cron jobs:
- Every hour at minute 0 (sync-core): sync-core syndication update-stats
- Every minute (ideally close to second 0) (sync-core-broker): sync-core-broker messages cron

If you are using rest+queue separately, replace `sync-core` with `sync-core-queue` above.

## Notes

**Caution: The message broker cron job is NOT expected to be retried on failure as jobs are not expected to overlap.**

All other cron jobs can be retried on failure.

**Caution: Some cron jobs are expected to run early every minute, so you need a pod scheduler that has new pods up and running within 15s.**
**If you are using a slow scheduler like Fargate please reach out to us for alternative cron job scheduling options.**
