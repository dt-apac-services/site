summary: FluentD Hands On Tutorial
id: fluentd-tutorial
categories: fluentdf
tags: fluentd
status: Published 
authors: Adam Gardner
Feedback Link: https://dt-apac-services.github.io/site/

# fluentd-demo

## Overview 

Getting started with Dynatrace Generic Log Ingestion with [fluentd](https://fluentd.org).

Generic log ingestion is currently **only** available for Dynatrace SaaS.

**This tutorial will not work with Managed.**

## Architecture

You will install the fluentd service on your laptop. This will read a log file and communicate to an ActiveGate.

Log lines will be pushed to the ActiveGate and then to your Dynatrace SaaS tenant.

![fluentd dynatrace architecture](assets/fluentd-demo-system-architecture.png)

## Overview

In this demo scenario fluentd will be installed as a service on your laptop. An environment ActiveGate will be installed.

This can either be installed on a standalone VM (eg. in the cloud) or on your local machine.

Fluentd will be configured to send it's output to the ActiveGate API endpoint.


The Fluentd service will watch the log file you specify, parse each log line as it is written and push that log line up to Dynatrace (via the ActiveGate).

## Create Log Ingest Token
Create a Dynatrace API token with `Ingest Logs` permissions

## Install ActiveGate

Install a standard environment ActiveGate either on your laptop or a cloud VM. If installing on a cloud VM, ensure that port `9999` is open on the ActiveGate. Fluentd will need to send data from your laptop to the REST API on this ActiveGate.

Check that the **log monitoring** module is enabled (it should be by default):

![log monitoring enabled](assets/log_monitoring_enabled.png)

## Install fluentd

Install fluentd on your laptop.

- Download and install the [windows msi](https://www.fluentd.org/download)
- Ensure the fluentd windows service is running

![fluentd service running](assets/fluentd_running.png)


- Open the td-agent command prompt (not standard CMD window) as administrator

![td agent cmd](assets/td_agent_cmd.png)


- Install the Dynatrace fluentd plugin: `td-agent-gem install fluent-plugin-dynatrace`

## Create Log File
Create an empty log file in any location you want. Make a note of the location as you'll need it later.

## Configure fluentd
Open `C:\opt\td-agent\etc\td-agent\td-agent.conf` in a notepad as an administrator.

Place the following content at the bottom of the file.

Note: The `*.log.pos` file doesn't have to exist. fluentd will create it for you.

```
<source>
  @type tail
  path C:/path/to/your/log/file.log
  pos_file C:/path/to/your/log/file.log.pos
  tag first.*
  <parse>
    @type regexp
	  expression /^\[(?<time>[^\]]+)\] (?<loglevel>[^\s]+) (?<payload>.+)$/
  </parse>
</source>

<match first.**>
  @type              dynatrace
  active_gate_url    https://ACTIVEGATE_IP:9999/e/DT_TENANT_ID/api/v2/logs/ingest
  api_token          dt0c01.***
  ssl_verify_none    true
</match>
```

## Restart FluentD

Restart the fluentd Windows service so it picks up your changes.

## Write log file lines

Copy and paste these lines into your log file. Saving after each line.

```
[2021-09-06 10:00:00 AEST] INFO This is my first log line...
[2021-09-06 10:00:01 AEST] INFO This is my second log line...
[2021-09-06 10:00:02 AEST] INFO This is my third log line...
[2021-09-06 10:00:03 AEST] INFO This is my fourth log line...
[2021-09-06 10:00:04 AEST] INFO This is my fifth log line...
[2021-09-06 10:00:05 AEST] INFO This is my sixth log line...
[2021-09-06 10:00:06 AEST] INFO This is my seventh log line...
```

## See Results in Dynatrace

Navigate to the Logs screen and wait a few moments. After a while, your log lines will appear.

![logs in dynatrace](assets/dt_logs.png)