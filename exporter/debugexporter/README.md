# Debug Exporter

<!-- status autogenerated section -->
| Status        |           |
| ------------- |-----------|
| Stability     | [development]: traces, metrics, logs   |
| Distributions | [core], [contrib] |
| Warnings      | [Unstable Output Format](#warnings) |
| Issues        | [![Open issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aopen%20label%3Aexporter%2Fdebug%20&label=open&color=orange&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aopen+is%3Aissue+label%3Aexporter%2Fdebug) [![Closed issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aclosed%20label%3Aexporter%2Fdebug%20&label=closed&color=blue&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aclosed+is%3Aissue+label%3Aexporter%2Fdebug) |

[development]: https://github.com/open-telemetry/opentelemetry-collector#development
[core]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol
[contrib]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib
<!-- end autogenerated section -->

Exports data to the console (stderr) via `zap.Logger`.

See also the [Troubleshooting][troubleshooting_docs] document for examples on using this exporter.

[troubleshooting_docs]: ../../docs/troubleshooting.md

## Getting Started

The following settings are optional:

- `verbosity` (default = `basic`): the verbosity of the logging export
  (detailed|normal|basic). When set to `detailed`, pipeline data is verbosely
  logged.
- `sampling_initial` (default = `2`): number of messages initially logged each
  second.
- `sampling_thereafter` (default = `1`): sampling rate after the initial
  messages are logged (every Mth message is logged).
  The default value of `1` means that sampling is disabled.
  To enable sampling, change `sampling_thereafter` to a value higher than `1`.
  Refer to [Zap docs](https://godoc.org/go.uber.org/zap/zapcore#NewSampler) for more details
  on how sampling parameters impact number of messages.

Example configuration:

```yaml
exporters:
  debug:
    verbosity: detailed
    sampling_initial: 5
    sampling_thereafter: 200
```

## Verbosity levels

The following subsections describe the output from the exporter depending on the configured verbosity level - `basic`, `normal` and `detailed`.
The default verbosity level is `basic`.

### Basic verbosity

With `verbosity: basic`, the exporter outputs a single-line summary of received data with a total count of telemetry records for every batch of received logs, metrics or traces.

Here's an example output:

```console
2023-11-10T22:49:03.510-0600    info    TracesExporter  {"kind": "exporter", "data_type": "traces", "name": "debug", "resource spans": 1, "spans": 2}
```

### Normal verbosity

With `verbosity: normal`, the exporter outputs about one line for each telemetry record, including its body and attributes.
The "one line per telemetry record" is not a strict rule.
For example, logs with multiline body will be output as multiple lines.

> [!IMPORTANT]
> Currently the `normal` verbosity is only implemented for logs.
> Metrics and traces are going to be implemented in the future.
> The current behavior for metrics and traces is the same as in `basic` verbosity.

Here's an example output:

```console
2024-05-27T12:46:22.423+0200    info    LogsExporter    {"kind": "exporter", "data_type": "logs", "name": "debug", "resource logs": 1, "log records": 1}
2024-05-27T12:46:22.423+0200    info    the message app=server
        {"kind": "exporter", "data_type": "logs", "name": "debug"}
```

### Detailed verbosity

With `verbosity: detailed`, the exporter outputs all details of every telemetry record, typically writing multiple lines for every telemetry record.

Here's an example output:

```console
2023-11-10T22:49:03.510-0600    info    TracesExporter  {"kind": "exporter", "data_type": "traces", "name": "debug", "resource spans": 1, "spans": 2}
2023-11-10T22:49:03.510-0600    info    ResourceSpans #0
Resource SchemaURL: https://opentelemetry.io/schemas/1.4.0
Resource attributes:
     -> service.name: Str(telemetrygen)
ScopeSpans #0
ScopeSpans SchemaURL:
InstrumentationScope telemetrygen
Span #0
    Trace ID       : 3bde5d3ee82303571bba6e1136781fe4
    Parent ID      : 5e9dcf9bac4acc1f
    ID             : 2cf3ef2899aba35c
    Name           : okey-dokey
    Kind           : Server
    Start time     : 2023-11-11 04:49:03.509369393 +0000 UTC
    End time       : 2023-11-11 04:49:03.50949377 +0000 UTC
    Status code    : Unset
    Status message :
Attributes:
     -> net.peer.ip: Str(1.2.3.4)
     -> peer.service: Str(telemetrygen-client)
Span #1
    Trace ID       : 3bde5d3ee82303571bba6e1136781fe4
    Parent ID      :
    ID             : 5e9dcf9bac4acc1f
    Name           : lets-go
    Kind           : Client
    Start time     : 2023-11-11 04:49:03.50935117 +0000 UTC
    End time       : 2023-11-11 04:49:03.50949377 +0000 UTC
    Status code    : Unset
    Status message :
Attributes:
     -> net.peer.ip: Str(1.2.3.4)
     -> peer.service: Str(telemetrygen-server)
        {"kind": "exporter", "data_type": "traces", "name": "debug"}
```

## Warnings

- Unstable Output Format: The output formats for all verbosity levels is not guaranteed and may be changed at any time without a breaking change.
