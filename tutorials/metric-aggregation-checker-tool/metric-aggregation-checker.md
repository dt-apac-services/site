summary: Metric Aggregation Checker Tool
id: metric-aggregation-checker-tool
categories: dynatrace
tags: dynatrace, metrics
status: Published 
authors: Adam Gardner
Feedback Link: https://dt-apac-services.github.io/site/

# Metric Aggregation Checker

## Overview 

The Dynatrace Data Explorer provides the possibility of visually charting all aggregations for all metrics. However, not all metrics support all aggregations.

The data explorer will transparently chart a metric using that metrics default aggregation. This can lead to misleading results.

For example, if you chart the 90th percentile for a metric which does not support percentiles - a chart will still be generated, but the data will be innaccurate.

This small utility will output the supported aggregations, default aggregation for whatever metric you provide.

This provides the capability for automation to ensure that the metric being used really does support your chosen aggregation and that your chart will be accurate.

## Usage

You will need an API token with `v2 Read Metrics` permissions.

`dt_url`, `dt_api_token` and `dt_metric_id` are all mandatory parameters.

`dt_display` and `dt_logging` are *optional* parameters.

`dt_display`choices are `all`, `unit`, `defaultAggregation` or `aggregations`. Default = `all`

`dt_logging` is used for debug purposes to add additional logging. `set dt_logging=yes`. Defaults to `off` (no additional logging).

```
docker run --rm ^
--env dt_url=https://abc12345.live.dynatrace.com ^
--env dt_api_token=dt0c01.****** ^
--env dt_metric_id=builtin:apps.web.actionCount.category ^
--env dt_display=all ^
adamgardnerdt/metric-aggregation-checker:v0.1
```

### Sample Output

### dt_display = all
```
{
  'displayName': 'Action count (by Apdex category) [web]',
  'unit': 'Count',
  'aggregations': ['auto', 'value'],
  'defaultAggregation': {'type': 'value'}
}
```

### dt_display = unit
```
{
  'displayName': 'Action count (by Apdex category) [web]',
  'unit': 'Count'
}
```

### dt_display = defaultAggregation
```
{
  'displayName': 'Action count (by Apdex category) [web]',
  'defaultAggregation': {'type': 'value'}
}
```

### dt_display = aggregations
```
{
  'displayName': 'Action count (by Apdex category) [web]',
  'aggregations': ['auto', 'value']
}
```

## Source Code and Improvements

- Source code (and improvements most welcome via Pull Requests): [this repo](https://github.com/dt-apac-services/metric-aggregation-checker)
- Docs improvements welcome via Pull Request to [this repo](https://github.com/dt-apac-services/site)