summary: Key Request Automation Tool
id: key-request-automation-tool
categories: dynatrace
tags: dynatrace, key requests
status: Published
authors: Adam Gardner
Feedback Link: https://dt-apac-services.github.io/site/

# Key Request Automation Tool

## Overview 

This tool will allow you to create Key Requests:

1. Before traffic has even arrived on a services
2. At massive scale
3. In an automated way (eg. via a pipeline)

![key request tool 1](assets/key_request_tool_1.png)

## Build CSV

Create a file formatted (with a header row).

Seperator **must** be a pipe `|` and the header row names must match what is shown below.

- The `entitySelector` field can be any valid Dynatrace entity selector.
- The `request_name` (one per line) must match what is shown in the Dynatrace UI.

```
entitySelector|request_name
type(SERVICE),tag(owned_by:owner@me.com)|/pageone.html
type(SERVICE),tag(application:frontend),tag(environment:production)|/pagetwo.html
```

## Apply Configuration

You will need an API token with `v2 Read Entities` and `v2 Write Settings` permissions.

You must run the docker command from the same directory as your CSV file.

`dt_url`, `dt_api_token` and are mandatory parameters.

`dt_filename` and `dt_logging` are *optional* parameters. If `dt_filename` is not set, defaults to `input.csv`.

`dt_logging` is used for debug purposes to add additional logging. `set dt_logging=yes`. Defaults to `off` (no additional logging).

```
docker run --rm ^
-v %cd%:/app ^
--env dt_url=https://abc12345.live.dynatrace.com ^
--env dt_api_token=dt0c01.***** ^
--env dt_filename=input.csv ^
--env dt_logging=yes ^
adamgardnerdt/key-request-creator:v0.1
```