summary: NATS Tutorial
id: nats-tutorial
categories: nats
tags: nats
status: Published 
authors: Adam Gardner
Feedback Link: https://dt-apac-services.github.io/site/

# NATS / Cloud Automation Tutorial

## Overview 

This hands-on tutorial will introduce [NATS](https://nats.io) and explain its foundational role in the Dynatrace Cloud Automation module.

## Prerequisites

To follow the hands-on portions of this tutorial you will need:

- Docker installed
- Ability to download an `.exe` file and (optionally) add it to your `PATH`

## Brief NATS Introduction

NATS is what is called "message oriented middleware". At it's most basic, it is a one-to-many publisher / subscriber tool.

Message *publishers* publish messages into "subjects". A Subject is created in realtime and can be any alphanumeric string, using dots as a seperator. In this way, a publisher is completely free to "create" subjects at any time.

Messages *subscribers* "listen" for messages for a particular subject.

As already mentioned, you are free to make up any subject name you want. The following are all valid message subject names:

```
time.us
foo.bar
com.dynatrace.event.agent.installed
notifications.good.things
notifications.bad.things
```

## Start a NATS Server

Now that we understand the basics of NATS, let's start up a NATS server:

```
docker run -p 8222:8222 -p 4222:4222 -ti nats:latest
```

Port `8222` is a web-based stats interface - we'll use it just to prove NATS is online.
Port `4222` is the main port you need. It's the one that messages are published on and subscribers listen via.

Point your browser to `http://localhost:8222` and you should see the web interface. This proves NATS is online. If you want, you can stop docker and relaunch just with port `4222` (or just leave it running as-is).

## Install NATS CLI

The NATS Command Line Interface tool is the utility we will use to work with NATS.

Download and extract the `exe` file for the [latest release from GitHub](https://github.com/nats-io/natscli/releases). Optionally, add it to your `PATH` too.

Running `nats --version` should provide the version number you downloaded.

## Subscribe to a Subject

Imagine your application would like to listen for messages that indicate "good things" happening. You have spoken to the developer and they've informed you that they will publish messages onto the `com.dynatrace.things.good` subject. So now you (in real life, your application or microservice) need to subscribe to that.

The syntax for subscribing to a subject is: `nats sub subject_name`

Open a new `cmd` window and type: `nats sub com.dynatrace.things.good`

```
...> nats sub com.dynatrace.things.good
14:49:15 Subscribing on com.dynatrace.things.good
```

## Publish A Message

Publish the first message to that subject. Open a new `cmd` window and again use the `nats` CLI. 

You should now have 3 cmd terminals open:

1. Server
2. Subscriber
3. Publisher

This time, to publish a message:

The syntax is: `nats pub subject_name "Your Message Here"`

Type: `nats pub com.dynatrace.things.good "This is my first good message..."`

```
...> nats pub com.dynatrace.things.good "This is my first good message..."
14:51:18 Published 32 bytes to "com.dynatrace.things.good"
```

Flick across to the subscriber cmd window and you will see this:

```
...> nats sub com.dynatrace.things.good
14:49:15 Subscribing on com.dynatrace.things.good
[#1] Received on "com.dynatrace.things.good"
This is my first good message...
```

## Another Service Subscribes to Good Events

Imagine a second service also wishes to consume the "good" events. It too can listen to `com.dynatrace.things.good` and react to these events.

The second service probably does something entirely different with teh data.

Open another new `cmd` window and subscribe to the `com.dynatrace.things.good` subject:

```
...> nats sub com.dynatrace.things.good
15:52:01 Subscribing on com.dynatrace.things.good
```

Using the publisher `cmd` terminal window, publish a second message:

```
nats pub com.dynatrace.things.good "This is my second good message..."
```

Notice how **both** subscribers receive this second message.

![nats1](assets/nats1.jpg)

## Subscribe to a different subject

Open a fifth `cmd` window and subscribe to a different subject - the subject name can be anything you like:

```
...> nats sub com.dynatrace.things.bad
14:54:25 Subscribing on com.dynatrace.things.bad
```

Publish a third message on the "good" subject. Notice how this new subscriber *does not* receive the message:

```
...> nats pub com.dynatrace.things.good "This is my third good message..."
```

![nats2](assets/nats2.jpg)

## Cool, But So What?

Keptn (part of the Cloud Automation module) is underpinned by NATS and the concepts discussed above.

Keptn generates NATS subjects and messages then orchestrates workflows that you build, based on these NATS subjects.

This gives maximum flexibility: You decide what steps occur in the workflow and Keptn will orchestrate those steps using NATS messages.

The only limitation is your imagination when it comes to building and wiring together workflows.

## Working Example #1: Broadcasting Events

Suppose you have a problem notification from Dynatrace that you wish to distribute to 5 different third party tools.

You *could* create 5 different problem integrations and have Dynatrace send to each tool.

![nats3](assets/nats3.jpg)

Alternatively, you could send the problem (once) to Cloud Automation and have those 5 different tools "listen" to that subject. Now each tool receives a copy of the problem report. One problem integration to manage in Dynatrace and an extensible pattern that is easy to maintain.

![nats3](assets/nats4.jpg)

## Working Example #2: Event Enrichment

Suppose you have multiple systems of record. These systems hold important additional information regarding your environment or infrastructure.

For example, for the `frontend` application in `production`, the `owner` is stored in system of record A as `bob@example.com`. System of record B stores further information that shows the `service_owner` to be `kate@example.com`.

This additional metadata needs to be retrieved, in realtime, for any problem coming from dynatrace for the `frontend` application in `production`. Only then can your team be notified as they'll have the info they need to action the problem.

So logically, the "flow" would look like this:

1. Problem event received from Dynatrace
2. Retrieve any available metadata from System of Record A
3. Retrieve any available metadata from System of Record B
4. Add metadata to the event
5. Pass enriched event to the teams (eg. Create an incident, send a Slack message or an email notification)

This could be modelled in Cloud Automation as follows:

```
...
sequences:
  - name: "problem-notification"
    tasks:
      - name: "enrich-event"
      - name: "distribute-event"
...
```

## Cloud Automation Lifecycle

The lifecycle of a Cloud Automation sequence is:

1. Message published to the `...triggered` subject
2. Message(s) published to the `...started` subject
3. Message(s) published to the `...finished` subject

Humans (and third party tools) interact at the sequence level and send in `.triggered` events. Cloud Automation manages and distributes the `triggered` events at the task level. In other words, we ask Cloud Automation to `trigger` the `problem-notification` sequence and Cloud Automation manages and co-ordinates the minutae of the tasks involved (`enrich-event` and `distribute-event`).

For example: When a problem is received into Cloud Automation:
- An incoming payload (eg. from Dynatrace) has the `type` set to `sh.keptn.event.problem-notification.triggered` (this is our subject like `com.dynatrace.messages.good` was earlier)
- A NATS message is automatically generated by Cloud Automation and distributed to the `sh.keptn.event.enrich-event.triggered` subject
- Both Systems of Record (A and B) listen for messages on the `sh.keptn.event.enrich-event.triggered` subject. Cloud Automation knows that two subscribers are listening for messages on this channel
- Each System of Record seperately send a `sh.keptn.event.enrich-event.started` message back to Cloud Automation
- Each System of Record seperately does it's own event enrichment
- Each System of Record seperately sends a `sh.keptn.event.enrich-event.finished` message back to Cloud Automation. Cloud Automation knows that all listening services have responded and so it can progress
- Cloud Automation combines the payloads from the steps above, generates and distributes a message on the `sh.keptn.event.distribute-event.triggered` subject.
- Your ticketing system is subscribing to messages on the `sh.keptn.event.distribute-event.triggered` so it sends back an `sh.keptn.distribute-event.started` message to notify Cloud Automation
- Your ticketing system does what is necessary then sends a message on the `sh.keptn.event.distribute-event.finished` subject back to Cloud Automation
- Cloud Automation knows that the workflow is finished and so distributes a final message on the `sh.keptn.event.problem-notification.finished` subject

The benefit of this architecture should be clear - you can scale up or swap Systems of Record providers (aka message consumers) whenever you like - the workflow remains the same and there is no additional logic required for it to "just work".

In short, you design the workflow and Cloud Automation will orchestrate that workflow for you.

![nats5](assets/nats5.jpg)

## Summary

We now have a Dynatrace-native way to build, model, orchestrate and execute arbitrary any custom sequences of tasks that customers may wish to build.

Cloud Automation natively (and currently) provides code quality gates and auto-remediation "out of the box". Over time more capabilities are expected to be added to Cloud Automation.

However, as demonstrated in this tutorial, Cloud Automation is flexible and can cater to any custom workflow your customers may require.

