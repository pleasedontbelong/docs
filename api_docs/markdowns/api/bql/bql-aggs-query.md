# BQLAggsQuery

`BQLAggsQuery` is used for [[Urls Aggregation;analysis-aggregate-urls]] to define aggregations to perform, metrics to compute, and filter to operate.

## Format
```JSON
{
  "aggs": [
    {
      "group_by": ?Array<BQLGroupBy>,
      "metrics": Array<BQLMetric>
    }
  ],
  "filters": ?BQLFilter
}
```

A `BQLAggsQuery` is composed of a list of `BQLAggregate` and an optional `BQLFilter`. An `BQLAggregate` defines some `BQLMetric` to compute. `BQLGroupBy` can be used to group URLs and compute metrics on each group.


### Group-Bys
A group-by is defined by:
  - distinct fields on which the group-by is performed.
  - ranges that define buckets for the group-by operation.

#### Distinct GroupBy
Only [[aggregables fields;analysis-datamodel?filter=agg:categorical]] can be used for distinct group-by operations. They are specified either by field name or by field, result size and sort order. By default, at lease 1000 results are returned, sorted by value.
The order key can be by `value` or `count`.

**Examples**
The following groups URLs by their `http_code`.
```JSON
"http_code"
```

This is similar to the following.
```JSON
{
  "distinct": {
    "field": "http_code",
    "size": 1000,
    "order": { "value": "asc" }
  }
}
```

The following groups URLs by their HTTP Code, returning the 10 most frequent ones (ex: 200, then 400, then 301, etc)
```JSON
{
  "distinct": {
    "field": "http_code",
    "size": 10,
    "order": { "value": "desc" }
  }
}
```

#### Range GroupBy
Only [[numerical fields;analysis-datamodel?filter=agg:numerical]] can be used for range group by operations.
The `from` boundary is inclusive, while the `to` boundary is exclusive.

**Example**
The following groups URLs by their `delay_last_byte` on two ranges (fast and slow URLs)
```JSON
{
  "range": {
    "field": "delay_last_byte",
    "ranges": [
      { "from": 0, "to": 1000 },
      { "from": 1000 }
    ]
  }
}
```

### Metrics
Metrics define the operation to compute. Except for `count`, a field on which the metric is applied must be provided. Available metrics are:
- `count`
- `sum`
- `avg`
- `min`
- `max`
- `count_true`
- `count_false`
- `count_null`
- the [[count functions;bql-functions#count-functions]] are also available.

Note: The default metric is `count`.

**Examples**

Count of URLs
```JSON
"count"
```

Sum of internal inlinks nofollow
```JSON
{ "sum": "inlinks_internal.nb.follow.total" }
```

Count of compliant URLs
```JSON
{"count_true": "compliant.is_compliant"}
```

Count of URLs without query string
```JSON
{"count_null": "main.query_string"}
```

Count of URLs with at least one page with similarity above or equal to 75%
```json
{
  "function": "count_gt",
  "args": ["content_quality.nb_simscore_pct_75", 0]
}
```

### Filters

Please refer to [[BQLFilter;bql-filter]] documentation.


## Examples
Full examples in [[Urls Aggregation documentation;analysis-aggregate-urls#example-aggregation-on-filtered-dataset]].
