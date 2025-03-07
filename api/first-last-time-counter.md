---
api_name: first_time() | last_time()
excerpt: Get the first and last timestamps seen by `CounterSummary` aggregates
topics: [hyperfunctions]
tags: [hyperfunctions, finance]
api:
  license: community
  type: function
  experimental: true
  toolkit: true
hyperfunction:
  family: metric aggregation
  type: accessor
  aggregates:
    - counter_agg()
# fields below will be deprecated
api_category: hyperfunction
api_experimental: true
toolkit: true
hyperfunction_family: 'metric aggregation'
hyperfunction_subfamily: 'counter and gauge aggregation'
hyperfunction_type: accessor
---

import Experimental from 'versionContent/_partials/_experimental.mdx';

# first_time, last_time <tag type="toolkit" content="Toolkit" /><tag type="experimental-toolkit" content="Experimental" />

This pair of functions returns the timestamps of the first and last points in a `CounterSummary` aggregate.

```sql
first_time(
    cs CounterSummary
) RETURNS TIMESTAMPTZ
```

```sql
last_time(
    cs CounterSummary
) RETURNS TIMESTAMPTZ
```

<Experimental />

## Required arguments

|Name| Type |Description|
|-|-|-|
|`cs`|CounterSummary|The input CounterSummary from a previous `counter_agg` (point form) call, often from a continuous aggregate|

## Returns

|Column|Type|Description|
|-|-|-|
|`first_time`|`TIMESTAMPTZ`|The time of the first point in the `CounterSummary`|

|Column|Type|Description|
|-|-|-|
|`last_time`|`TIMESTAMPTZ`|The time of the last point in the `CounterSummary`|

## Sample usage

This example produces a CounterSummary from timestamps and associated values, then applies the `first_time` and `last_time` accessors:

```sql
WITH t as (
    SELECT
        time_bucket('1 day'::interval, ts) as dt,
        counter_agg(ts, val) AS cs -- get a CounterSummary
    FROM table
    GROUP BY time_bucket('1 day'::interval, ts)
)
SELECT
    dt,
    first_time(cs) -- extract the timestamp of the first point in the CounterSummary
    last_time(cs) -- extract the timestamp of the last point in the CounterSummary
FROM t;
```
