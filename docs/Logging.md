---
title: Logging
has_children: false
nav_order: 6
---

# Logging

Logging can either be as plain messages or as JSON with one line per log message.

## Plain log messages (default)

The format of plain messages is:

> \[\<date>] \[\<severity>] \[\<context>] \<message>

Example plain log message:

> \[2022/02/22 01:02:03.456] \[i] \[MessageService] An example log message.

The placeholder values are:
- date: YYYY/MM/DD hh:mm:ss.SSS
- severity:
  - e: error
  - w: warning
  - i: information / notice
  - v: verbose
  - d: debug
- context: a service name consisting of \[A-Za-z0-9].
- message: a human readable text message.
  - May contain a JSON object at the end if there's additional information available that's not included in the message itself.

## JSON format

Logs one line per message as an object containing:

- timestamp: unix timestamp of the message, **including milliseconds**.
- level: one of `error`, `warn`, `info`, `verbose`, `debug`.
- context: a service name consisting of \[A-Za-z0-9].
- message: a human readable text message.
- ...: any additional information that's not included in the message, e.g. `stack` if an exception was thrown.
