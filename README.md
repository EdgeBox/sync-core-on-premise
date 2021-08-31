# Introduction

## Support

If you need any help with this, please contact us at support \[at\] cms-content-sync.io.

## Content Sync

[Content Sync](https://cms-content-sync.io/) allows you to share content and media between multiple Drupal websites. You can use it to prepare your content on a private staging instance and then push it to your production environment by the click of a button. Or you can use it to connect any number of Drupal sites and share content between them.
Distribute your content automatically to all connected sites or provide your editors a simple interface for importing content and media by the click of a button- it’s all in your hands.

## Sync Core

The Sync Core handles the distribution of the content. All sites must connect to the same Sync Core to exchange content.
All our packages include our SaaS Sync Core- so if cloud hosting is an option for you, we recommend you use that.
If you require on-premise hosting, you can use this repository as a starting point for setting up your own Sync Core.
Please note that an Enterprise license is required to get access to the Sync Core Docker image.

To get the required UUID and secret for a new Sync Core, please register your Sync Core [here](https://app.cms-content-sync.io/sync-cores).
Don't re-use the same UUID and secret on multiple environments. Please checkout our documentation for more information.

The Sync Core consists of two different containers that share the same image:
- sync-core: The main application that exposes REST interfaces and performs remote updates.
- sync-core-broker: A message broker that provides some more advanced features compared to plain RabbitMQ.

If you expect high loads (usually only after 5+ sites are connected), you can divide the Sync Core further by having
one service that handles REST requests and one service that handles messages. In this case, copy the
sync-core service definition to `sync-core-rest` and `sync-core-queue`. Then simply add this environment
variable to the `sync-core-rest` service:
- SERVICE_TYPES=rest

and add this environment variable to the `sync-core-queue` service:
- SERVICE_TYPES=queue

then remove the port exposure for the sync-core-queue service as that is not required anymore.
Please note that the sync-core-queue service now must use CLI health checks (see below).

You can do this separation later, too, as the sync-core service can simply run in parallel to the sync-core-queue and sync-core-rest services.

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
- If you would like to follow your own deployment workflow, you can pull the image and store it in your own     private registry with your own release tags.
- Store the logs centrally so you can easily access them.
- Secrets are passed as environment variables, so you should preferably use a vault.
- A load balancer if you would like to scale horizontally (strongly recommended).
- Use templates to manage your setup like CFT on AWS.

# Setup

We recommend you start with a local setup to test that everything is working as expected and to get familiar with the setup.
Starting with a simpler, local setup also allows you to circle back to it and see how it works locally if you run into any issues setting up your cluster.

If you want to use the Sync Core locally for development, the Sync Core and the sites must be in the same Docker network.
You have two options to achieve this:
- You can copy the services and volumes from our `docker-compose.yml` into your own `docker-compose.yml` file that also includes your websites.
  - If you are exposing port 8080 for your own sites, you can remove the port exposure from the Sync Core or choose a different port to expose the Sync Core at.
  - The distadvantage is that if anything changes in this stack, you must copy the changes to your own stack.
- You can connect the networks of this stack to your own website Docker stack.
  - The easiest way to accomplish this is to reference your existing network in this stack. Please checkout the `docker-compose.yml`'s `networks` section for details.
  - The disadvantage is that if you are using identical services you may get conflicts with host names.

# Prepare

## Registry access

We're using ECR from AWS to host the Sync Core images. Please install the AWS CLI and create a profile at ~/.aws/credentials with the following content:

```
[edge-box]
aws_access_key_id=XXX
aws_secret_access_key=YYY
```

XXX must be replaced with the Key ID we send you, YYY with the Key we send you.

If you want to provide the Sync Core to your developers for a local setup, please let us know, so we can provide you with
a separate access key. Otherwise, if we need to revoke a key, your production environment may be afflicted.

If you are using your own registry, just add this as secrets to your build script instead of storing the secrets locally.

## Environment variables

Copy the `.env.template` file to `.env`. Go through the `.env` file and add all required values.

**IMPORTANT: Don't use the same Sync Core UUID and secret for two different setups! This can lead to
all kinds of hard-to-debug issues. Using the same Sync Core UUID and secret for your production environment
and any other environment can lead to the production environment shutting down completely due to how our licensing and
abuse detection works.**

# Start locally

## Log in to the Docker registry

Login to our Docker registry:

```bash
aws ecr get-login-password --region eu-central-1 --profile edge-box | docker login --username AWS --password-stdin 949515072404.dkr.ecr.eu-central-1.amazonaws.com/cms-content-sync/sync-core
```

...or if you are using the CLI v1:
```bash
$(aws ecr get-login --no-include-email --region eu-central-1 --profile edge-box)
```

## Start the services locally

Run:
```bash
docker-compose up -d
```

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
docker-compose run --rm sync-core yarn start
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
curl localhost:8080/sync-core/healthz/ready
curl localhost:8080/sync-core/healthz/live

# Verify the broker is operational.
docker-compose exec sync-core-broker yarn run console health ready
docker-compose exec sync-core-broker yarn run console health live

# If you have divided the Sync Core into "rest" and "queue", you can test the queue like:
#docker-compose exec sync-core-queue yarn run console health ready
#docker-compose exec sync-core-queue yarn run console health live
```

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

### Readiness

Endpoint (if REST):
- Port: 8080
- Path: /sync-core/healthz/ready

CLI (if broker or queue):
- `yarn run console health ready`

We test with the following probe config:
```yaml
failureThreshold: 2
initialDelaySeconds: 10
periodSeconds: 30
successThreshold: 2
timeoutSeconds: 5
```

You can adjust the config to match your own best practices.

### Liveness

Endpoint (if REST):
- Port: 8080
- Path: /sync-core/healthz/live

CLI (if broker or queue):
- `yarn run console health live`

We test with the following probe config:
```yaml
failureThreshold: 2
initialDelaySeconds: 30
periodSeconds: 30
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
- Every hour at minute 0 (sync-core): yarn run console syndication update-stats
- Every minute (ideally close to second 0) (sync-core-broker): yarn run console-broker messages cron

**Caution: The message broker cron job is NOT expected to be retried on failure as jobs are not expected to overlap.**

All other cron jobs can be retried on failure.

## Logging

If you are using a central log management, you can add an alert for the "Error" keyword.
Please note that syndication errors are expected so they won't be logged by default and are available via the Content Sync UI instead.
So by default only application/service errors will be logged.

# Setup database and search index

Run in a fresh or existing sync-core container:
```bash
# This will setup the database with all required indices.
yarn console install new

# This will create the index in Elasticsearch.
yarn console-previews previews create-index
```

If you want to test this locally, just prepend `docker-compose exec sync-core ` to the commands.

# Configuration options

## Both Sync Core and Broker

### Queue
- `SYNC_CORE_QUEUE_URL`: The queue that the Sync Core container(s) will use to process messages. The Broker will publish messages to this queue, and the Sync Core will publish and subscribe to messages.
- `BROKER_QUEUE_URL`: The queue that the Broker container(s) will use to process messages. The Sync Core will publish messages to this queue, and the Broker will publish and subscribe to messages.
- `MESSAGE_PREFETCH_COUNT` default `50`: How many messages to prefetch per container. If you are scaling based on CPU usage, this option must be fine-tuned to avoid both overloading, and a failure to scale (under-usage) while the queue is piling up messages. 

### Database
- `DATABASE_URL` required: The full mongodb URL, including the database name to store all data. You can provide a different database to the Broker, and the Sync Core (makes scaling easier).
- `DATABASE_RETRY_ATTEMPTS` optional, default `10`: Number of retries to connect or reconnect to the database before killing the process.
- `DATABASE_LOG_LEVEL` optional, one of `error`, `warn`, `info`, `debug`, default `warn`: Log level for the database.
- `DATABASE_ENABLE_SSL` optional, one of `true` or `false`, default *(empty)*: Usually not required if you enable ssl/tls using the query parameters of the `DATABASE_URL`. If you run into a connection timeout, try removing `ssl=true` and `tls=true` from the database URL and use these environment variables instead. Experienced this issue when using CosmosDB.
- `DATABASE_ENABLE_TLS` optional, one of `true` or `false`, default *(empty)*: Usually not required; same as `DATABASE_ENABLE_SSL`, but for a different library internally; set to the same value as your `DATABASE_ENABLE_SSL` variable.

### Error reporting (Sentry)
- `SENTRY_DSN` optional, default *(empty)*: A Sentry DSN used to report all errors to.
- `TRACES_SAMPLE_RATE` optional, default `0`: Sample rate between 0 and 1 if you want to collect performance metrics with Sentry.
- `HOSTNAME` optional, default *(empty)*: Used by Sentry to identify the host.

## Sync Core

### General
- `SERVICE_NAME` required: Must be `sync-core`.
- `SYNC_CORE_UUID` required: Provided at app.cms-content-sync.io.
- `SYNC_CORE_SECRET` required: Provided at app.cms-content-sync.io.
- `SERVICE_TYPES` default `rest,queue`: One of: `rest`, `queue` or `rest,queue`.
- `FEATURE_FLAGS` as a comma-separated list, default *(empty)*: A list of feature flags to statically configure. See below for a list of all feature flags.
- `PORT` default `8080`: The HTTP port to listen to.

### Security
- `DISABLE_HEADER_PROTECTION` default `no` (so enabled): Disable protection against commonly abused headers. Not required unless you are using customized clients.
- `RATE_LIMIT_TIME_WINDOW_IN_MINUTES` default `5`: Limit requests per client / IP.
- `RATE_LIMIT_NUMBER_OF_REQUESTS` default `2000`: Limit requests in the given time window to this number. This is *per container*, so if you are using a rate limiting WAF anyway, you can leave this to any high enough value.

### Files

#### General
- `LOCAL_FILESYSTEM` default `no`: One of `yes` or `no`. Whether you want to use the local file system for file storage (`yes`) or S3 (`no`).
- `MAX_FILE_SIZE_PREVIEW` in bytes, default `10240` (10 KB): The maximum size allowed for a preview HTML. This will be indexed in ElasticSearch, so this has significant impact on the search performance and required storage.
- `MAX_FILE_SIZE_FLOW_CONFIG` in bytes, default `5242880` (5 MB): The maximum size allowed for a Flow YAML file. So the maximum required storage for Flow configs is (number of sites) * (flows per site) * (this value). If a site exceeds this it can't connect to the Sync Core so limit with caution.
- `MAX_FILE_SIZE_CONTENT_FILE` in bytes, default `10485760` (10 MB): The maximum size allowed for a content file (a file a user uploaded to a site). Updates will be rejected if they contain a file larger than this, so limit with caution.

#### Local
- `MAX_FILE_SIZE_LOCAL_UPLOAD` in bytes, default `10485760` (10 MB): If you are using the local file system to store files, no files larger than this can be uploaded. Must be as big as the largest file size restriction above. 

#### S3
- `AWS_ACCESS_KEY_ID` default *(empty)*: If using S3, the Access Key ID from your AWS user used for uploading and downloading files.
- `AWS_SECRET_ACCESS_KEY` default *(empty)*: If using S3, see above.
- `AWS_S3_BUCKET` default *(empty)*: If using S3, the bucket to store files in.
- `AWS_REGION` default *(empty)*: If using S3, the region where the bucket has been created.

### Search
- `ELASTICSEARCH_URL` default *(empty)*: A full URL to yor ElasticSearch backend that we can use to index content items and their preview and to search for content in the Pull Dashboard. If you are not using the manual pull option you won't need this.
- `ELASTICSEARCH_ADD_KEYWORD_SUFFIX` default *(empty)*: One of: `yes`, `no`. Not required in most cases. If you forgot to setup the search index before indexing documents, Elasticsearch will not change the existing field types but rather create new fields with a `.keyword` suffix. In this case, pass `yes` as a value.

### Requests
- `REQUEST_TIMEOUT` in milliseconds, default `60000` (60 seconds): The default timeout for all requests. Can be overwritten for different kinds of requests (see below).
- `REQUEST_TIMEOUT_DOWNLOAD_PREVIEW` in milliseconds, default *(REQUEST_TIMEOUT)*: The timeout for requesting an HTML preview from a site for a given entity.
- `REQUEST_TIMEOUT_UPDATE_ENTITY` in milliseconds, default *(REQUEST_TIMEOUT)*: The timeout for sending an update request to a connected site for an entity.
- `REQUEST_TIMEOUT_DOWNLOAD_FILE` in milliseconds, default *(REQUEST_TIMEOUT)*: The timeout for downloading a file from a site.
- `REQUEST_TIMEOUT_UPLOAD_FILE` in milliseconds, default *(REQUEST_TIMEOUT)*: The timeout for uploading a file to S3 (if S3 is used).

## Broker
- `SERVICE_NAME` required: Must be `broker`.

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

You can enable Feature Flags using the environment variable `FEATURE_FLAGS` and providing a comma-separated list or by using the console command `yarn console features <enable|disable> <name> [type] [id]`.

## Dynamic usage (CLI)

To list available flags, and their current values in your installed Sync Core, use `yarn console features list`. To get the settings for an individual flag, use `yarn console features get <name> [type] [id]`.

To revert to the defaults, use `yarn console features unset <name> [type] [id]`.

## Narrow usage

Narrow down the usage of certain features to avoid an unnecessary performance impact. The `type` argument can be one of the following (defaults to `sync-core`):

- `sync-core`: The whole Sync Core, so global (default).
- `contract`: Enable/Disable the feature for the given contract.
- `project`: Enable/Disable the feature for the given project.
- `site`: Enable/Disable the feature for the given site.

All types except for the sync-core require an ID to be provided. You can get the ID from the MongoDB collections manually if needed.
