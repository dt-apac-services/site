## Create Dynatrace Items

1. Create a Synthetic script to monitor `http://example.com`.
2. Create an SLO to monitor the availability percentage of the synthetic test. Set the threshold to 98.00% and 99.00% (warning)
3. Create a dashboard and place two tiles on the dashboard:
    1) The SLO above
    2) The synthetic test response time

Make a note of the dashboard ID.

![dashboard](./assets/images/dashboard.png)

## Create a Dynatrace API Token

With the following permissions:

- `entities.read` (Read entities)
- `metrics.read` (Read metrics)
- `problems.read` (Read problems)
- `securityProblems.read` (Read security problems)
- `slo.read` (Read SLO)
- `DataExport` (Access problem and event feed, metrics, and topology)
- `DTAQLAccess` (User sessions)
- `Read configuration` (Read configuration)
- `WriteConfig` (Write configuration)
