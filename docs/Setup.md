---
title: Setup
has_children: true
nav_order: 3
---

# Setup

We recommend you start with a local setup to test that everything is working as expected and to get familiar with the setup.
Starting with a simpler, local setup also allows you to circle back to it and see how it works locally if you run into any issues setting up your cluster.

## Prepare setup

### Registry access

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

## Log in to the Docker registry

Login to our Docker registry:

```bash
aws ecr get-login-password --region eu-central-1 --profile edge-box | docker login --username AWS --password-stdin 949515072404.dkr.ecr.eu-central-1.amazonaws.com/cms-content-sync/sync-core
```

...or if you are using the CLI v1:
```bash
$(aws ecr get-login --no-include-email --region eu-central-1 --profile edge-box)
```

## Download images

You can use `docker pull` to pull the images from our registry after logging in. We are using semver tags
for versioning. So if the current release is `2.1.3` then the following tags are all pointing to it:
- `2.1.3`
- `2.1`
- `2`

If you are storing our images in your own registry we recommend you either use the major or minor tag to
pull from us and push to your own registry. Alternatively, you can also query for new images:
```
aws ecr list-images --profile=edgebox --registry-id=949515072404 --region=eu-central-1 --repository-name=cms-content-sync/sync-core --filter=tagStatus=TAGGED --max-items=1000 --output=text --query 'reverse(sort_by(imageIds, &imageTag))[?starts_with(imageTag, `2.`) == `true`].[imageTag]'
```
This will show all released images for the Sync Core 2.x.
