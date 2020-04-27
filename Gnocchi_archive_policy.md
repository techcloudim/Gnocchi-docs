## How to define archive policies

The [archive policies](https://gnocchi.xyz/glossary.html#term-archive-policy) define how the [metrics](https://gnocchi.xyz/glossary.html#term-metric) are aggregated and how long they are stored. Each [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy) definition is expressed as the number of points over a [timespan](https://gnocchi.xyz/glossary.html#term-timespan).

If your [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy) defines a policy of 10 points with a [granularity](https://gnocchi.xyz/glossary.html#term-granularity) of 1 second, the [time series](https://gnocchi.xyz/glossary.html#term-time-series) archive will keep up to 10 seconds, each representing an aggregation over 1 second. This means the [time series](https://gnocchi.xyz/glossary.html#term-time-series) will at maximum retain 10 seconds of data between the more recent point and the oldest point. That does not mean it will be 10 consecutive seconds: there might be a gap if data is fed irregularly.

**There is no expiry of data relative to the current timestamp. Data is only expired according to timespan.**

Each [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy) also defines which [aggregation methods](https://gnocchi.xyz/glossary.html#term-aggregation-method) will be used. The default is set to default_aggregation_methods which is by default set to *mean*, *min*, *max*, *sum*, *std*, *count*.

Therefore, both the [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy) and the [granularity](https://gnocchi.xyz/glossary.html#term-granularity) entirely depends on your use case. Depending on the usage of your data, you can define several [archive policies](https://gnocchi.xyz/glossary.html#term-archive-policy). A typical low grained use case could be:

```
1440 points with a granularity of 1 minute = 24 hours
```

The worst case scenario for storing compressed data points is 8.04 bytes per point, whereas best case scenario can compress up to 0.05 bytes per point. Knowing that, it is possible to compute the worst case scenario for storage in order to plan for data storage capacity.

An archive policy of 1440 points would need 1440 points × 8.04 bytes = 11.3 KiB per [aggregation method](https://gnocchi.xyz/glossary.html#term-aggregation-method). If you use the 6 standard [aggregation method](https://gnocchi.xyz/glossary.html#term-aggregation-method) proposed by Gnocchi, your [metric](https://gnocchi.xyz/glossary.html#term-metric) will take up to 6 × 11.3 KiB = 67.8 KiB of disk space per metric.

Be aware that the more definitions you set in an [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy), the more CPU it will consume. Therefore, creating an [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy) with 2 definitons (e.g. 1 second granularity for 1 day and 1 minute granularity for 1 month) may consume twice CPU than just one definition (e.g. just 1 second granularity for 1 day).

### Default archive policies

By default, 4 [archive policies](https://gnocchi.xyz/glossary.html#term-archive-policy) are created when calling gnocchi-upgrade: *bool*, *low*, *medium* and *high*. The name both describes the storage space and CPU usage needs.

The bool [archive policy](https://gnocchi.xyz/glossary.html#term-archive-policy) is designed to store only boolean values (i.e. 0 and 1). It only stores one data point for each second (using the last [aggregation method](https://gnocchi.xyz/glossary.html#term-aggregation-method)), with a one year retention period. The maximum optimistic storage size is estimated based on the assumption that no other value than 0 and 1 are sent as [measures](https://gnocchi.xyz/glossary.html#term-measure). If other values are sent, the maximum pessimistic storage size is taken into account.

- low
  - 5 minutes granularity over 30 days
  - aggregation methods used: default_aggregation_methods
  - maximum estimated size per metric: 406 KiB
- medium
  - 1 minute granularity over 7 days
  - 1 hour granularity over 365 days
  - aggregation methods used: default_aggregation_methods
  - maximum estimated size per metric: 887 KiB
- high
  - 1 second granularity over 1 hour
  - 1 minute granularity over 1 week
  - 1 hour granularity over 1 year
  - aggregation methods used: default_aggregation_methods
  - maximum estimated size per metric: 1 057 KiB
- bool
  - 1 second granularity over 1 year
  - aggregation methods used: *last*
  - maximum optimistic size per metric: 1 539 KiB
  - maximum pessimistic size per metric: 277 172 KiB
