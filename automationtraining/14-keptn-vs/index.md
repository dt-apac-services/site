## Keptn vs.

A number of questions have come up around "Keptn vs. Tool X" so this page aims to address that.

> TL:DR Keptn is a tool orchestrator not a replacement. Think "Keptn and..." not "Keptn or..."

## Keptn and Pipeline Tooling (Jenkins, Azure DevOps, CircleCI etc.)
At first glance, a Keptn sequence as seen above could look a lot like a pipeline. But you are in full control of which tools execute for each task.

- Want CircleCI to action the `deployment` task? Fine - have CircleCI listen for the `deployment.triggered` and run an entire pipeline in response.
- A different team uses Jenkins instead? Easy. In their project, Jenkins can listen for the `deployment.triggered` event. The sequence is identical but the two teams are **empowered** to use different tooling.

## Keptn and Load Testing Tools (JMeter, NeoLoad, BlazeMeter, k6 etc.)
Out-of-the box, Keptn comes with a [JMeter service](https://github.com/keptn/keptn/blob/master/jmeter-service/chart/templates/deployment.yaml#L89) which listens for the `test.triggered` event.

Prefer a different load testing tool? Uninstall the Jmeter Service and install a service that listens for the `test.triggered` event and you're done.

## Keptn and Observability Tools (Prometheus, Dynatrace, Splunk etc.)
Keptn uses observability tools (more generally, tools that provide metrics) during the `evaluation` task. The `evaluation` task is an out-of-the-box Keptn feature which provides a quality gate. This quality gate can comprise any metric from any tool.

During creation of a Keptn project, Keptn is configured to know which backend metric provider it should use for the particular project. For example, Prometheus may be chosen as the metric store. You will also be required to install the relevant service which listens for the `get-sli.triggered` cloudevent.

During the `evaluation` task, Keptn will automatically issue a `get-sli.triggered` command. Your metric provider will then react and retrieve metrics from the backend of your choice. After all, the Prometheus Service should know how to retrieve Prometheus metrics!

Just like any other Keptn service, should you want to change monitoring provider, simply uninstall the existing service (eg. Prometheus service) and install the new service (eg. Dynatrace service). Now quality gate metrics will be retrieved from Dynatrace and not Prometheus.

## Discover Integrations

All Keptn integrations are listed on the [integrations page](https://keptn.sh/docs/integrations/).

Don't see your tool listed? You have 3 options:

1. Use the [Webhook service](https://github.com/keptn/keptn/tree/master/webhook-service) to trigger your tool
1. Use the [Job Executor service](https://github.com/keptn-contrib/job-executor-service) to run your container or script
1. Develop a custom Keptn service:
  - There's a Go template [here](https://github.com/keptn-sandbox/keptn-service-template-go) and / or
  - Raise a [GitHub issue on the integrations repo](https://github.com/keptn/integrations/issues) and / or
  - [Join #help-integrations on Slack](https://slack.keptn.sh/) for guidance and assistance