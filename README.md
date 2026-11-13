# OpenTelemetry for Beginners

Getting started with OpenTelemetry can feel overwhelming. This beginner series walks you through it step by step, from what OpenTelemetry is to building pipelines for traces, metrics, and logs with the OpenTelemetry Collector.

Watch the series on the [OpenTelemetry YouTube channel](https://youtu.be/iEEIabOha8U?feature=shared), and follow along with the code and walkthroughs in this repo.

## The JavaScript Journey

| Episode | Topic | Walkthrough |
|---|---|---|
| 1 | What is OpenTelemetry? | [Traces](javascript/traces/README.md#episode-1---what-is-opentelemetry) |
| 2 | Overview: Kickstart Your Telemetry Pipelines with OpenTelemetry | [Traces](javascript/traces/README.md#episode-2---overview-kickstart-your-telemetry-pipelines-with-opentelemetry) |
| 3 | Trace Instrumentation & Bare-Bones Collector Configuration | [Traces](javascript/traces/README.md#episode-3---trace-instrumentation--bare-bones-collector-configuration) |
| 4 | Processing Traces: OpenTelemetry Collector in Action | [Traces](javascript/traces/README.md#episode-4---processing-traces-opentelemetry-collector-in-action) |
| 5 | Metric Instrumentation & Bare-Bones Collector Configuration | [Metrics](javascript/metrics/README.md#episode-5---metric-instrumentation--bare-bones-collector-configuration) |
| 6 | Processing Metrics: OpenTelemetry Collector in Action | [Metrics](javascript/metrics/README.md#episode-6---processing-metrics-opentelemetry-collector-in-action) |
| 7 | Log Instrumentation & Bare-Bones Collector Configuration | [Logs](javascript/logs/README.md#episode-7---log-instrumentation--bare-bones-collector-configuration) |
| 8 | Processing Logs: OpenTelemetry Collector in Action | [Logs](javascript/logs/README.md#episode-8---processing-logs-opentelemetry-collector-in-action) |

## Getting started

New to the series? Start with the [Traces walkthrough](javascript/traces/README.md). It covers the prerequisites, how to clone this repo, and how to run the Roll the Dice app. Each walkthrough builds on the one before it.

## Repo layout

Each signal has its own folder under `javascript/`, with two setups that match its episodes:
- `bare-bones-setup/` instruments the app and sends telemetry through a minimal Collector configuration.
- `add-processors/` adds Collector processors to clean up, enrich, and filter that telemetry.

## Community

Have questions? The [OpenTelemetry documentation](https://opentelemetry.io/docs/) and the [OpenTelemetry Slack channel](https://opentelemetry.io/community/end-user/slack-channel/) are great places to start.
