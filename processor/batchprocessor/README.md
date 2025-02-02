# Batch Processor

| Status                   |                       |
| ------------------------ | --------------------- |
| Stability                | traces [beta]         |
|                          | metrics [beta]        |
|                          | logs [beta]           |
| Supported pipeline types | traces, metrics, logs |
| Distributions            | [core], [contrib]     |

The batch processor accepts spans, metrics, or logs and places them into
batches. Batching helps better compress the data and reduce the number of
outgoing connections required to transmit the data. This processor supports
both size and time based batching.

It is highly recommended to configure the batch processor on every collector.
The batch processor should be defined in the pipeline after the `memory_limiter`
as well as any sampling processors. This is because batching should happen after
any data drops such as sampling.

Please refer to [config.go](./config.go) for the config spec.

The following configuration options can be modified:
- `send_batch_size` (default = 8192): Number of spans, metric data points, or log
records after which a batch will be sent regardless of the timeout.
- `timeout` (default = 200ms): Time duration after which a batch will
be sent regardless of size.  If set to zero, `send_batch_size` is
ignored as data will be sent immediately, subject to only `send_batch_max_size`.
- `send_batch_max_size` (default = 0): The upper limit of the batch size.
  `0` means no upper limit of the batch size.
  This property ensures that larger batches are split into smaller units.
  It must be greater than or equal to `send_batch_size`.

Examples:

This configuration contains one default batch processor and a second
with custom settings.  The `batch/2` processor will buffer up to 10000
spans, metric data points, or log records for up to 10 seconds without
splitting data items to enforce a maximum batch size.

```yaml
processors:
  batch:
  batch/2:
    send_batch_size: 10000
    timeout: 10s
```

This configuration will enforce a maximum batch size limit of 10000
spans, metric data points, or log records without introducing any
artificial delays.

```yaml
processors:
  batch:
    send_batch_max_size: 10000
    timeout: 0s
```

Refer to [config.yaml](./testdata/config.yaml) for detailed
examples on using the processor.

[beta]: https://github.com/open-telemetry/opentelemetry-collector#beta
[contrib]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib
[core]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol
