---
title: Configuration
has_children: false
nav_order: 4
---

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

### Logging
- `LOG_LEVEL` optional, default *info*: Either `error`, `warn`, `info`, `verbose` or `debug`.
- `LOG_FORMAT` optional, default `plain`: Either `plain` or `json`.

## Health checks
- `HEALTH_EVENT_LOOP_SAMPLE_INTERVAL` optional, default `50`: How often to run the event loop check in milliseconds, i.e. CPU usage based on ticks of the app.
- `HEALTH_EVENT_LOOP_LIMIT` optional, default `500`: How long a tick may last before it's considered unhealthy in milliseconds, i.e. the allowed delay for the sample tick to run.
- `HEALTH_DNS_URL` optional, default `https://www.google.com/`: What URL to use for DNS health checks. Should be an external, public service to be comparable to your sites but shouldn't be your sites to avoid cascading failures.

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
- `REQUEST_MAX_REDIRECTS` default `5`: The max number of redirects to follow when making a request against a site.
- `REQUEST_MAX_RESPONSE_BODY_SIZE` in bytes, default `4194304` (4MB): The max allowed response body size when making a request against a site to update or retrieve an entity or entity list.
- `DEFAULT_MAX_REQUESTS_PER_MINUTE_PER_SITE` default `20`: How many requests will be made to a site per minute by default. Users can overwrite this setting per site or set another default for all sites in their project.
- `DEFAULT_MAX_PARALLEL_REQUESTS_PER_SITE` default `10`: How many requests will be made to a site in parallel by default. Users can overwrite this setting per site or set another default for all sites in their project. This is only relevant for requests that couldn't be processed immediately (because the per-minute limit throttled them or because they failed) and is only used by the cron that processes these messages once a minute.

## Broker
- `SERVICE_NAME` required: Must be `broker`.
