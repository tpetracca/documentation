---
title: Monitor-based SLOs
kind: documentation
description: "Use Monitors to define the Service Level Objective"
further_reading:
- link: "/monitors/"
  tag: "Documentation"
  text: "More information about Monitors"
- link: "https://www.datadoghq.com/blog/define-and-manage-slos/#monitor-based-slo"
  tag: "Blog"
  text: "Best practices for managing your SLOs with Datadog"
---

## Overview
To build an SLO from new or existing Datadog monitors, create a monitor-based SLO.

Time-based data applies well to monitor-based SLOs. Using a monitor-based SLO, you can calculate the Service Level Indicator (SLI) by dividing amount of time your system exhibits good behavior by the total time.

{{< img src="monitors/service_level_objectives/grouped_monitor_based_slo.png" alt="monitor-based SLO example"  >}}

## Prerequisites

To create a monitor-based SLO, you need an existing Datadog monitor. To set up a new monitor, go to the [monitor creation page][1].

Datadog monitor-based SLOs support the following monitor types:
- Metric Monitor Types (Metric, Integration, APM Metric, Anomaly, Forecast, Outlier)
- Synthetic
- Service Checks (open beta)

## Setup

On the [SLO status page][2], select **New SLO**.

Under **Define the source**, select **Monitor Based**.

### Define queries

In the search box, start typing the name of a monitor. A list of matching monitors appears. Click on a monitor name to add it to the source list.

### Set your SLO targets

Select a **target** percentage, **time window**, and optional **warning** level.

The target percentage specifies the portion of time the underlying monitor(s) of the SLO should be in an OK state.

The time window specifies the rolling time period over which the target should be tracked.

Depending on the value of the SLI, the Datadog UI displays the SLO status in a different font color:
- While the SLI remains above the target, the UI displays the SLO status in a green font.
- When the SLI falls below the target, the UI displays the SLO status in a red font.
- If you included a warning level, and the SLI falls above the warning but below the target level, the UI displays the SLO status in a yellow font.

The number of decimal places you can specify for your monitor-based SLOs differs depending on the time window you choose:
- 7-day and 30-day targets allow up to two decimal places.
- 90-day targets allow up to three decimal places.

In the details UI for the SLO, Datadog will display two decimal places for 7-day and 30-day targets, and three decimal places for 90-day targets.

Datadog evaluates monitors once a minute, which limits the granularity of monitor-based SLOs. If you need more granularity, consider using [metric-based SLOs][3] instead.

These limits are in place because monitor-based SLO error budget is specified in terms of time. A 99.999% target for a 7-day and 30-day targets results in error budgets of 6 seconds and 26 seconds, respectively. Monitors evaluate every minute, thus the granularity of a monitor-based SLO is also 1 minute. This means one alert would fully consume and overspend the 6 second or 26 second error budgets in the previous examples (for use cases requiring more granularity, consider using [metric-based SLOs][3] instead). Furthermore, error budgets this small are likely unrealistic goals for you to try to achieve in practice.

### Add name and tags

Choose a name and extended description for your SLO. Select any tags you would like to associate with your SLO.

### Example

For example, if you have a Metric Monitor that is configured to alert when user request latency is greater than 250ms, you could set a monitor-based SLO on that monitor. Let’s say you choose an SLO target of 99% over the past 30 days. What this means is that the latency of user requests should be less than 250ms 99% of the time over the past 30 days. To set this up, you would:

1. Select a single monitor or,
2. Select multiple monitors (up to 20) or,
3. Select a single [multi-alert monitor][4] and select specific monitor groups (up to 20) to be included in SLO calculation using the **Calculate on selected groups** toggle.

**Example:** You might be tracking the uptime of a physical device. You have already configured a metric monitor on `host:foo` using a custom metric. This monitor might also ping your on-call team if it's no longer reachable. To avoid burnout you want to track how often this host is down.

Example: `99% of the time requests should have a latency of less than 300ms over the past 7 days`.

## Status calculation

{{< img src="monitors/service_level_objectives/overall_uptime_calculation.png" alt="overall uptime calculation"  >}}

The overall status can be considered as a percentage of the time where **all** monitors or **all** the calculated groups in a single multi-alert monitor are in the `OK` state. It is not the average of the aggregated monitors or the aggregated groups, respectively.

Consider the following example for 3 monitors (this is also applicable to a monitor-based SLO based on a single multi-alert monitor):

| Monitor            | t1 | t2 | t3    | t4 | t5    | t6 | t7 | t8 | t9    | t10 | Status |
|--------------------|----|----|-------|----|-------|----|----|----|-------|-----|--------|
| Monitor 1          | OK | OK | OK    | OK | ALERT | OK | OK | OK | OK    | OK  | 90%    |
| Monitor 2          | OK | OK | OK    | OK | OK    | OK | OK | OK | ALERT | OK  | 90%    |
| Monitor 3          | OK | OK | ALERT | OK | ALERT | OK | OK | OK | OK    | OK  | 80%    |
| **Overall Status** | OK | OK | ALERT | OK | ALERT | OK | OK | OK | ALERT | OK  | 70%    |

This can result in the overall status being lower than the average of the individual statuses.

### Exceptions for synthetic tests
In certain cases, there is an exception to the status calculation for monitor-based SLOs that are comprised of one grouped Synthetic test. Synthetic tests have optional special alerting conditions that change the behavior of when the test enters the ALERT state and consequently impact the overall uptime:

- Wait until the groups are failing for a specified number of minutes (default: 0)
- Wait until a specified number of the groups are failing (default: 1)
- Retry a specified number of times before a location's test is considered a failure (default: 0)

By changing any of these conditions to something other than their defaults, the overall status for a monitor-based SLO using just that one Synthetic test could appear to be better than the aggregated statuses of the Synthetic test's individual groups.

For more information on Synthetic test alerting conditions, visit the Synthetic Monitoring [documentation][5].

### Impact of manual and automatic resolution, and muting

SLO calculations do not take into account when a monitor is resolved manually or as a result of the **_After x hours automatically resolve this monitor from a triggered state_** setting. If these are important tools for your workflow, consider cloning your monitor, removing auto-resolve settings and `@-notification`s, and using the clone for your SLO.

Datadog recommends against using monitors with `Alert Recovery Threshold` and `Warning Recovery Threshold` as they can also affect your SLO calculations and do not allow you to cleanly differentiate between a SLI's good behavior and bad behavior.

TODO Muting

## Underlying monitor and SLO histories

SLOs based on the metric monitor types have a feature called SLO Replay that will backfill SLO statuses with historical data pulled from the underlying monitors' metrics and query configurations. This means that if you create a new Metric Monitor and set an SLO on that new monitor, rather than having to wait a full 7, 30 or 90 days for the SLO's status to fill out, SLO Replay will trigger and look at the underlying metric of that monitor and the monitor's query to get the status sooner. SLO Replay also triggers when the underlying metric monitor's query is changed (e.g. the threshold is changed) to correct the status based on the new monitor configuration. As a result of SLO Replay recalculating an SLO's status history, the monitor's status history and the SLO's status history may not match after a monitor update.

**Note:** SLO Replay is not supported for SLOs based on Synthetic tests or Service Checks.

## Other considerations

Confirm you are using the preferred SLI type for your use case. Datadog supports monitor-based SLIs and metric-based SLIs as [described in the SLO metric documentation][3].

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://app.datadoghq.com/monitors#create
[2]: https://app.datadoghq.com/slo
[3]: /monitors/service_level_objectives/metric/
[4]: /monitors/create/types/metric/?tab=threshold#alert-grouping
[5]: /synthetics/api_tests/?tab=httptest#alert-conditions
