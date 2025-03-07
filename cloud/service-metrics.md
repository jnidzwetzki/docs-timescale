---
title: Service metrics
excerpt: View metrics for your Timescale Cloud service, such as CPU, memory, and storage usage
product: cloud
keywords: [metrics, monitoring, services]
tags: [dashboard, cpu, memory, storage, disk space]
---

# Service metrics

You can view your service metrics from Timescale Cloud's
[metrics dashboard][metrics-dashboard]. This dashboard gives you service-level
information, such as CPU, memory, and storage usage.

You can also view your query-level statistics by using the pre-installed
[`pg_stat_statements`][pg-stat] extension from a PostgreSQL client.

## Metrics dashboard

Timescale Cloud provides a Metrics dashboard for managing your services. You can
see the Metrics dashboard in your Timescale Cloud account by navigating to the
`Services` section, clicking the service you want to explore, and selecting the
`Metrics` tab.

You can view metrics for your services for any of these time ranges:

*   Last hour, with one minute granularity
*   Last 24 hours, with one minute granularity
*   Last seven days, with one hour granularity
*   Last 30 days, with one hour granularity

To change the view, select the time range from the drop-down menu.

<img class="main-content__illustration" src="https://s3.amazonaws.com/assets.timescale.com/docs/images/tsc-metrics_lastmonth.png" alt="Timescale Cloud Metrics dashboard"/>

Additionally, you can turn automatic metric refreshes on and off. When automatic
metric refresh is on, the dashboard updates every thirty seconds.

In some cases, gray vertical bars display on the metrics dashboard, like this:

<img class="main-content__illustration" src="https://s3.amazonaws.com/assets.timescale.com/docs/images/tsc-metrics_graybar.png" alt="Timescale Cloud Metrics not collected"/>

This indicates that metrics have not been collected for the period shown. It
does not mean that your Timescale Cloud service was down.

## Continuous storage monitoring

Timescale Cloud continuously monitors the health and resource consumption of all
database services. You can check your health data by navigating to the `metrics`
tab in your service dashboard. These metrics are also monitored by the Timescale
operations team.

<img class="main-content__illustration" src="https://s3.amazonaws.com/assets.timescale.com/docs/images/tsc-disk-metrics.png" alt="Timescale Cloud metrics dashboard"/>

If your database exceeds a storage threshold of available resources, some
automated actions are triggered, including notifications and preventative
actions.

### Automated user alerting

When your disk usage exceeds certain thresholds, you receive an email
notification. These notifications occur at:

*   75%
*   85%
*   95%

So that you aren't overwhelmed by automated messages, the alerting thresholds use
low and high watermarks, and we limit the frequency of messages we send you
about a particular service.

### Automated overload protection

If your database continues to increase in size past these thresholds, automated
overload protection is activated when your disk becomes 99% full. When this
happens, your database is put into read-only mode, you receive an
email notification, and the Timescale Cloud console shows the changed status.

When your disk is in read-only mode, you can still query your database, but you
cannot add any new data to it. This ensures that your disk does not fill up
to 100%, and thus prevents the database from crashing due to an out of memory (OOM)
error.

With your database in read-only mode, you need to decide if you are going to
increase your storage capacity, or reduce the size of your database. When you
have done that, you can also add a retention policy, or turn on compression, to
avoid the problem occurring again in the future.

## Query-level statistics with pg_stat_statements

The `pg_stat_statements` extension gives you query-level statistics for your SQL
statements. It comes pre-installed with Timescale Cloud.

<highlight type="note">
For more information about `pg_stat_statements`, see the
[PostgreSQL documentation](https://www.postgresql.org/docs/current/pgstatstatements.html).
</highlight>

<highlight type="important">
You cannot currently enable `track_io_timing` for your database. Statistics that
depend on `track_io_timing`, such as `blk_read_time` and `blk_write_time`, are
not collected.
</highlight>

### Query the pg_stat_statements view

You can view statistics for your queries through the `pg_stat_statements`
extension, which provides a `pg_stat_statements` view. The recorded statistics
include the time spent planning and executing each query; the number of blocks
hit, read, and written; and more.

You can query the `pg_stat_statements` view as you would any PostgreSQL view.
The full view includes superuser queries, which are used by Timescale Cloud to
manage your service in the background. To view only your
queries, filter by the current user.

Connect to your database using a PostgreSQL client, such as [`psql`][psql], and
run:

```sql
SELECT * FROM pg_stat_statements WHERE pg_get_userbyid(userid) = current_user;
```

### Example usage

With `pg_stat_statements`, you can view performance statistics that help you
monitor and optimize your queries.

Here are some sample scenarios to try.

Identify the 5 longest-running queries by their mean execution time:

```sql
SELECT calls,
    mean_exec_time,
    query
FROM pg_stat_statements
WHERE pg_get_userbyid(userid) = current_user
ORDER BY mean_exec_time DESC
LIMIT 5;
```

Identifying queries with highly variable execution time:

The relative standard deviation, or the standard deviation expressed as a
percentage of the mean, measures how variable the execution time is. The higher
the relative standard deviation, the more variable the query execution time.

```sql
SELECT calls,
    stddev_exec_time/mean_exec_time*100 AS rel_std_dev,
    query
FROM pg_stat_statements
WHERE pg_get_userbyid(userid) = current_user
ORDER BY rel_std_dev DESC
LIMIT 5;
```

For more examples and detailed explanations, see the [blog post on identifying
performance bottlenecks with `pg_stat_statements`][blog-pg_stat_statements].

[metrics-dashboard]: /cloud/:currentVersion:/service-metrics/#metrics-dashboard
[pg-stat]: /cloud/:currentVersion:/service-metrics/#query-level-statistics-with-pg-stat-statements
[blog-pg_stat_statements]: <https://www.timescale.com/blog/identify-postgresql-performance-bottlenecks-with-pg_stat_statements/>
[psql]: /timescaledb/:currentVersion:/how-to-guides/connecting/about-psql/
