---
slug: /en/optimize/data-type-choice
sidebar_label: Data Type Choice
title: Data Type Choice
---

One of the contributors to ClickHouse speed is efficient data storage.
Choice of data types and the `ORDER BY`and `PRIMARY KEY` tuples influence the way data is stored, the speed of queries, and the cost of running ClickHouse.

## Data types

ClickHouse has many data types, and there is a link at the bottom of the page to the full list.  In this document some of the data types that are used to improve performance are included.

### LowCardinality(String)

`LowCardinality()` is a superstructure that changes the data storage method and rules of data processing of the data type provided in parens.

### Enum8 and Enum16

### Int

### Nullable()

## Testing

The utility `clickhouse-benchmark` can be used to compare table structure, hardware, etc.  This example shows the same query against two tables in a single ClickHouse Cloud service.  The only difference between the two tables is that one uses a `String` for the column `pickup_ntaname`, and the other uses a `LowCardinality(String)` for that column.

If you look at rows per second (RPS) in the results, the performance of this query when using a `LowCardinality(String)` data type is 4.5 x that of a `String` data type.  The rest of the statistics show a similar ratio.  All of the metrics are described in the `clickhouse-benchmark` documentation, which is included at the end of this document.

:::note MBS
MBS, or Mebibytes per second is a record of the number of bytes read per second. The number of bytes read per second is much higher in the table using a `String` because the `String` data type uses more storage.
:::

```bash
clickhouse-benchmark \
# highlight-next-line
 --query "select pickup_ntaname from trips_lc
          group by pickup_ntaname order by count() desc" \
 --host HOSTNAME.clickhouse.cloud \
 --secure \
 --port 9440 \
 --password abcdefghi \
 --iterations=100 \
 --delay=0
```
```response
Queries executed: 100.

HOSTNAME.clickhouse.cloud:9440, 
queries 100, 
QPS: 108.611, 
# highlight-next-line
RPS: 217185163.815, 
MiB/s: 218.904, 
result RPS: 20636.130, 
result MiB/s: 0.573.
```

```bash
clickhouse-benchmark \
# highlight-next-line
 --query "select pickup_ntaname from trips
          group by pickup_ntaname order by count() desc" \
 --host HOSTNAME.clickhouse.cloud \
 --secure \
 --port 9440 \
 --password abcdefghi \
 --iterations=100 \
 --delay=0
```
```response
Queries executed: 100.

HOSTNAME.clickhouse.cloud:9440,
queries 100, 
QPS: 24.154, 
# highlight-next-line
RPS: 48298806.021, 
MiB/s: 1389.432, 
result RPS: 4589.174, 
result MiB/s: 0.123.
```

## Read more

[Data types](/docs/en/sql-reference/data-types/index.md)

[clickhouse-benchmark](/docs/en/operations/utilities/clickhouse-benchmark.md)

[Primary indexes](/docs/en/guides/improving-query-performance/sparse-primary-indexes/sparse-primary-indexes-intro.md)

[Sampling query profiler](/docs/en/operations/optimizing-performance/sampling-query-profiler.md)

[Tuning for cost efficiency](/docs/en/manage/tuning-for-cloud-cost-efficiency.md)

