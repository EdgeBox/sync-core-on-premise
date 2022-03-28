---
title: Introduction
has_children: false
nav_order: 1
---

# Introduction

## Support

If you need any help with this, please contact us at [support.cms-content-sync.io](https://support.cms-content-sync.io/).

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

You can do this separation later, too, as the sync-core service can simply run in parallel to the sync-core-queue and sync-core-rest services.
