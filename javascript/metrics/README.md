# OpenTelemetry for Beginners - The JavaScript Journey (Metrics)

Getting started with OpenTelemetry can feel overwhelming, but this beginner series for JavaScript developers will guide you step by step. This guide covers the **metrics** signal and builds directly on the traces guide.

> **New to the series?** Start with the [Traces walkthrough](../traces/README.md), which covers Episode 1 (_What is OpenTelemetry?_), the pipeline overview, and setting up your learning environment. This metrics guide picks up from there and assumes you've met the same prerequisites and are comfortable running the Roll the Dice app.

## Resources
- [OpenTelemetry documentation](https://opentelemetry.io/docs/)
  - Ask AI (⌘+K shortcut)
  - [Language APIs and SDKs](https://opentelemetry.io/docs/languages/)
  - [Instrumentation](https://opentelemetry.io/docs/concepts/instrumentation/)
  - [OpenTelemetry Collector](https://opentelemetry.io/docs/collector/)
    - [List of OpenTelemetry Collector processors](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor)
- [Prometheus documentation](https://prometheus.io/docs/introduction/overview/)
- [OpenTelemetry YouTube channel](https://www.youtube.com/@otel-official)
  - [OpenTelemetry for Beginners series - The JavaScript Journey](https://youtu.be/iEEIabOha8U?feature=shared)
- [OpenTelemetry Slack channel](https://opentelemetry.io/community/end-user/slack-channel/)

# Episode 6 - Back to Basics: Metric Instrumentation and Bare-Bones OpenTelemetry Collector Configuration

So far, everything we've done has centered on traces. 

We instrumented the app, pushed those traces through the OpenTelemetry Collector, and visualized them in Jaeger. 

<img width="2504" height="1406" alt="image" src="https://github.com/user-attachments/assets/4b95fe0d-10d0-4e13-adef-70a8f031fb7e" />

Traces are just one of the signals OpenTelemetry can collect. Over the next few episodes, we'll follow a similar journey with metrics.

So what are metrics? At their core, they're numeric measurements tracked over time, like the number of requests our app has served or how long each of those requests took.

Just like with traces, we'll use auto-instrumentation to generate those metrics. No new tooling, no extra code.

<img width="2501" height="1404" alt="image" src="https://github.com/user-attachments/assets/bf423794-7d0e-4a77-bf3d-c63887a94764" />

For now, we'll keep things deliberately simple. Configure a bare-bones Collector that receives metrics and forwards them to Prometheus, where they'll be stored and visualized.

We'll also explore what those metrics look like before making any changes to them.

In the following episode, we'll build on that foundation by introducing processors to transform our metrics before they're exported.

Let's get everything set up.

## Run the learning environment locally

If you've been following along, you should already have two terminals open, one running the Roll the Dice app and another running Docker.

We'll stop both and switch to the `metrics/bare-bones-setup` folder. Then we'll install the new dependencies for this example, and start everything again.

> **Just joining for metrics?** First, make sure you have installed:
> - [Node.js](https://nodejs.org/en/download/)
> - [Docker Desktop](https://www.docker.com/products/docker-desktop/)
> - [Docker Compose](https://docs.docker.com/compose/install/)
>
> Then head to the [Traces walkthrough](../traces/README.md) to clone the repo and set up the two terminals, and come back here.

**Terminal 1 - Roll the Dice app:**
```bash
# stop the running app
Ctrl + C

# move to the metrics bare-bones folder
cd ../../metrics/bare-bones-setup

# install this folder's dependencies, then start
npm install
npm start
```

**Terminal 2 - Docker (Collector + Jaeger + Prometheus):**
```bash
# stop and remove the traces stack
docker compose down

# move to the metrics bare-bones folder
cd ../../metrics/bare-bones-setup

# bring the stack up, bare-bones metrics setup (adds Prometheus)
docker compose up
```

Once everything is running, let's generate some metrics.

**Generate some metric data**

Head back to the Roll the Dice app at http://localhost:8080/rolldice and give it a few refreshes. Every reload returns a random roll between 1 and 6, exactly like tossing a real die.

![Roll the dice mov](https://github.com/user-attachments/assets/32f80dc2-93b0-4578-91db-17b316a79760)

As those requests land, keep an eye on the Docker terminal. The Collector's logs start filling up with metric data. That's the confirmation we're after. The app is emitting telemetry, and the Collector is picking it up.

<img width="1226" height="1125" alt="image" src="https://github.com/user-attachments/assets/155a4e4c-3de6-4a72-9984-714a900eab64" />

With data flowing, let's dig into how the app is producing it in the first place.

## Instrumentation recap

To generate telemetry, we first need to instrument our app. Using OpenTelemetry, we can instrument our code in two primary ways:
- **Automatic instrumentation** (AKA zero-code solutions)
- **Manual instrumentation** (AKA code-based solutions)

We covered both in the trace episodes, so we won't rehash them here. What matters for us today is that the same auto-instrumentation that gave us traces also generates metrics, so in this episode we'll lean on auto-instrumentation again to generate metrics and visualize them in Prometheus.

## The packages (package.json)

Switch back to the code editor. Make sure you have the metrics/bare-bones-setup folder open, then navigate to package.json.

Most of what's listed here should ring a bell from the trace episodes. Two entries are new, and both exist specifically to handle metrics. They're the Metrics SDK and the OTLP metric exporter.

<img width="1997" height="1067" alt="image" src="https://github.com/user-attachments/assets/cf171456-4863-4ff3-96ef-ccbdba8af2a9" />

- `@opentelemetry/sdk-metrics` is the Metrics SDK. It periodically collects and exports the metrics our app generates.
- `@opentelemetry/exporter-metrics-otlp-grpc` is the OTLP metric exporter. It sends those metrics to the OpenTelemetry Collector over gRPC.

The Metrics SDK gathers and ships our measurements on a schedule, and the exporter hands them off to the Collector. Between them, metrics are produced automatically and delivered to the Collector without us having to write any of that code ourselves.

Let's see how we use those packages to generate metrics. Switch to the instrumentation.js file. 

## instrumentation.js

This file prepares our app to generate metrics and send them to the local Collector.

```
const opentelemetry = require('@opentelemetry/sdk-node');

const {
  getNodeAutoInstrumentations,
} = require('@opentelemetry/auto-instrumentations-node');

const {
  OTLPTraceExporter,
} = require('@opentelemetry/exporter-trace-otlp-grpc');

const {
  OTLPMetricExporter,
} = require('@opentelemetry/exporter-metrics-otlp-grpc');

const {
  PeriodicExportingMetricReader,
} = require('@opentelemetry/sdk-metrics');

const sdk = new opentelemetry.NodeSDK({
  traceExporter: new OTLPTraceExporter({
    url: 'http://localhost:4317',
  }),
  metricReader: new PeriodicExportingMetricReader({
    exporter: new OTLPMetricExporter({
      url: 'http://localhost:4317',
    }),
    exportIntervalMillis: 10000,
  }),
  instrumentations: [getNodeAutoInstrumentations()],
});

sdk.start();
```

Boiled down, this file walks through four steps:

1. Import the OpenTelemetry SDK, the automatic instrumentation module, and the metric exporter, along with the periodic metric reader.
2. Initialize the SDK by creating a new `NodeSDK` instance. This is where we configure how telemetry is created and where it's sent.
3. Configure automatic instrumentation and export our metrics to the local Collector. We do this with a `PeriodicExportingMetricReader`, which gathers our metrics and sends them on a fixed interval (in our setup, every ten seconds) to the Collector listening on port `4317`, the standard OTLP endpoint.
4. Start the SDK. From this point on, the app can generate and send metrics.

Now that our app is set up to generate metrics, let's look at the Collector configuration that receives and exports them. You can find it by navigating to the otel directory and opening the otel-collector-config.yaml file.

## OpenTelemetry Collector Configuration

**otel/otel-collector-config.yaml**
```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

exporters:
  debug:
    verbosity: detailed

  otlp/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true

  prometheus:
    endpoint: 0.0.0.0:8889

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [debug, otlp/jaeger]
    metrics:
      receivers: [otlp]
      exporters: [debug, prometheus]
```

Our Collector configuration is intentionally minimal. It's made up of three main components:
1. Receivers
2. Exporters
3. Service

If you followed along with the trace episodes, this structure should look familiar. The receiver, in fact, hasn't changed at all. What's different this time lives in the exporter and the metrics pipeline, and those are exactly what we'll dig into next.

**Exporters define where the Collector sends telemetry data.** 

In this setup, we use two exporters for our metrics.
```
exporters:
  debug:
    verbosity: detailed

  prometheus:
    endpoint: 0.0.0.0:8889
```
- The **`debug` exporter** prints detailed telemetry data directly to the Collector's logs. For metrics, this shows us each metric name, its data points, and their attributes. It's very useful during development because it lets us see exactly what's flowing through the Collector.

<img width="1150" height="1051" alt="image" src="https://github.com/user-attachments/assets/91210710-9e0e-43d0-a5a4-74f8c6673a37" />

- The **`prometheus` exporter** is where things diverge.

```
exporters:
  debug:
    verbosity: detailed

  otlp/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true

  prometheus:
    endpoint: 0.0.0.0:8889
```

Traces went to Jaeger. Metrics head to Prometheus, a backend built specifically for them. 

The mechanism flips, too. Where the trace exporter _pushes_ data out to Jaeger, the Prometheus exporter simply _exposes_ an endpoint on port `8889` and lets Prometheus come scrape it. Once it's there, we can query and visualize metrics in the Prometheus UI.

Let's tie everything together. 

**The `service` component defines how data moves through the Collector.**
```
service:
  pipelines:
    metrics:
      receivers: [otlp]
      exporters: [debug, prometheus]
```
- In this metrics pipeline, the OTLP receiver accepts metric data from the app.
- The `debug` exporter logs those metrics to the terminal.
- The `prometheus` exporter exposes them to be scraped by Prometheus.

Together, these components define the path our metrics take through the Collector.

## Explore your metrics in Prometheus

With the configuration in place, it's time to watch it work. 

Open the Prometheus UI at http://localhost:9090. This is where we'll spend the rest of the section, visualizing the metrics our app produces as they travel through the Collector and land in Prometheus.

<img width="2430" height="768" alt="image" src="https://github.com/user-attachments/assets/ef8a19c9-48dc-4f14-a25b-870373f3744d" />

Let's run a query for one of the metrics our app generates automatically:
```
http_server_duration_milliseconds_count
```
This is a running count of the requests our app has handled. As we roll the dice, this HTTP server metric updates, and we didn't write any code to produce it. Auto-instrumentation generated it for us.

Copy and paste the query above and run it in the Prometheus UI.

Prometheus gives us two ways to read the result. 

The **Table** view shows the metric's most recent value.

<img width="2428" height="698" alt="image" src="https://github.com/user-attachments/assets/fc50617c-2fab-4af8-834c-4457671c7d1d" />

The **Graph** view plots it over time so we can watch the count climb with each new roll. 
<img width="1945" height="1133" alt="image" src="https://github.com/user-attachments/assets/d861c7ab-1d6f-44de-a37c-4b8caab62698" />

This is one of the key strengths of metrics. They let us observe how our app behaves over time using numeric measurements.

But the metric value is only part of the story. Let's take a look at the labels that come with it.

### Metric labels

In the same way trace spans came with attributes, each metric arrives with **labels** attached. 

They add extra context onto every measurement, such as the HTTP route, the request method, the status code.  

They let us filter and break down our metrics to answer questions about how many requests hit the /rolldice route, or how many returned an error.

<img width="2427" height="737" alt="image" src="https://github.com/user-attachments/assets/e4dbec9f-249e-4ec3-baeb-0358d249bb90" />

That said, not every label pulls its weight. Some are added automatically, `net_host_name` and `net_host_port` among them. 

They can be useful, but they also feed into cardinality, and cardinality has a cost. Every distinct combination of labels spins up its own time series, which quietly drives up storage and spend. 

We'll tackle how to trim this back in the next episode.

### Resource attributes (where the metric came from)

Labels tell us _what_ got measured, but there's a second layer worth looking at. 

Alongside them, every batch of metrics carries a set of **resource attributes** that describe _where_ the data came from, the app or service behind it, along with details about its host, process, and environment.

To find them, jump back to the Docker terminal where the Collector is logging its debug output, and use your terminal's search function to find the `Resource attributes` section.
(Our bare-bones Prometheus exporter doesn't publish a `target_info` metric, so this debug output is the clearest window into resource metadata. Think of it as the metrics equivalent of Jaeger's "Process" view.)

Since the logs scroll by fast, there's a screenshot below to make it easier to follow along.

<img width="2508" height="1409" alt="image" src="https://github.com/user-attachments/assets/1ecbac45-5bb5-4184-8da1-f18d5f911d4a" />

A handful of these are genuinely useful, while others are more than we really need. In our case that extra detail includes host identifiers, the process owner, and the entire command used to launch the app, all added automatically. 

Some of it is sensitive, some just noise. 

We'll clean these up in the next episode using processors:
- `host.arch`
- `host.id`
- `host.name`
- `process.command`
- `process.command_args`
- `process.executable.path`
- `process.owner`
- `process.pid`

> **Tip:** If the Resource block has scrolled off, refresh http://localhost:8080/rolldice a few times. The Collector exports metrics about every ten seconds, so the Resource block will appear again in the debug output.

To recap, we used auto-instrumentation to produce metrics, then configured a bare-bones OpenTelemetry Collector to receive them and pass them along to Prometheus. 

Along the way we got familiar with the labels and resource attributes attached to our telemetry. The pipeline is up and running, which means we're ready for the more interesting part, actually reshaping the data. 

In the next episode, processors take center stage, and we'll use them to tidy up, enrich, and transform our metrics before they leave the Collector.

# Episode 7 - Processing Metrics: OpenTelemetry Collector in Action

In the last episode, we instrumented our app and set up a bare-bones OpenTelemetry Collector to receive and export metrics. Along the way, we noticed that our metrics carried extra labels and resource metadata we didn't really need, some of it sensitive, some of it just noise.

<img width="2504" height="1405" alt="image" src="https://github.com/user-attachments/assets/bab79b62-026d-433f-ac62-79f58ccefb08" />

In this episode, we'll fix that using **processors**, which run inside the Collector and let us modify, enrich, or filter telemetry before it's exported.

<img width="2505" height="1409" alt="image" src="https://github.com/user-attachments/assets/ce0dd8eb-1259-4d73-84cc-3be29412adc5" />

## Project folders

1. `bare-bones-setup/` (**Episode 6**)
- Instruments the Roll the Dice app and sends metrics to the OpenTelemetry Collector.
- The Collector exports metrics to Prometheus with no additional processing.

2. `add-processors/` (**Current episode**)
- Uses the same setup as `bare-bones-setup`, but applies processors to incoming metrics.
- These processors enrich resource metadata, drop metrics we don't need, remove low-value or sensitive labels, rename a metric to match current conventions, and batch metrics for more efficient exporting.

**In the terminal running the Roll the Dice app, stop the app and navigate to the `add-processors` folder:**
```
# stop the app
Ctrl + C

# navigate to the add-processors folder
cd ../add-processors

# install dependencies and start the app
npm install
npm start
```

**In the terminal running Docker, stop and restart the Collector, Jaeger, and Prometheus:**
```
# stop and remove the running containers
docker compose down

# navigate to the add-processors folder
cd ../add-processors

# restart with the updated configuration
docker compose up
```
The Collector restart is the important step here. It's what loads the new processors config.

**Generate some metric data**

Head back to the Roll the Dice app in your browser and refresh it a few times to push fresh metrics into the newly configured Collector. 

![Roll the dice mov](https://github.com/user-attachments/assets/32f80dc2-93b0-4578-91db-17b316a79760)

Then switch over to the terminal running Docker, where you'll see those metrics moving through the OpenTelemetry Collector in the debug output.

<img width="1224" height="1131" alt="image" src="https://github.com/user-attachments/assets/378f2f29-acfe-4d28-9069-1076e145336a" />

With data flowing again, let's open up the configuration that's now processing it.

Switch back to the code editor. Make sure you have the metrics/add-processors folder open. Then navigate to the otel directory and open the otel-collector-config.yaml file.

## New OpenTelemetry Collector Configuration

Back in the bare-bones setup, this file had just three top-level sections, the receivers, the exporters, and the service block that organizes our pipelines. Now a fourth one joins them, `processors`, which is where each processor gets defined.

Because we're building on top of the traces episodes, the file lays out both a traces pipeline and a metrics pipeline, and the two lean on a couple of shared processors. Our attention here is on the **metrics** pipeline, which puts five processors to work.

Each one has a distinct job, so we'll walk through them one at a time, in the order `resource`, `attributes/metrics`, `filter/exclude_metrics`, `metricstransform`, and `batch`. The order they actually run in inside the pipeline is a little different, and we'll come back to that once we've seen what each one does.

**otel/otel-collector-config.yaml**
```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

processors:
  # Add/modify resource-level attributes (applies to both traces and metrics)
  resource:
    attributes:
      - key: deployment.environment.name
        value: local
        action: insert
      - key: host.arch
        action: delete
      - key: host.id
        action: delete
      - key: host.name
        action: delete
      - key: process.command
        action: delete
      - key: process.command_args
        action: delete
      - key: process.executable.path
        action: delete
      - key: process.owner
        action: delete
      - key: process.pid
        action: delete

  # Modify span attributes (for traces)
  attributes/traces:
    actions:
      - key: http.user_agent
        action: delete
      - key: net.host.ip
        action: delete
      - key: net.peer.ip
        action: delete
      - key: net.host.port
        action: delete
      - key: net.peer.port
        action: delete

  # Modify metric datapoint attributes (for metrics)
  attributes/metrics:
    actions:
      - key: net.host.port
        action: delete

  # Filter out unwanted metrics to reduce cardinality
  filter/exclude_metrics:
    error_mode: ignore
    metrics:
      metric:
        - 'name == "v8js.memory.heap.space.physical_size"'

  # Rename and transform metrics
  metricstransform:
    transforms:
      - include: http.server.duration
        action: update
        new_name: http.server.request.duration

  # Batch for efficient export
  batch:
    timeout: 5s
    send_batch_size: 512

exporters:
  debug:
    verbosity: detailed

  otlp/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true

  prometheus:
    endpoint: 0.0.0.0:8889

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [resource, attributes/traces, batch]
      exporters: [debug, otlp/jaeger]
    metrics:
      receivers: [otlp]
      processors: [resource, filter/exclude_metrics, attributes/metrics, metricstransform, batch]
      exporters: [debug, prometheus]
```

With the full config in view, here's the role each of the five processors plays for our metrics.

**1. The `resource` processor modifies metadata about the service or host.**

You already saw this one in the traces processing episode, and it's the very same processor here, shared by both the traces and metrics pipelines. 

```
processors:
  # Add/modify resource-level attributes (applies to both traces and metrics)
  resource:
    attributes:
      - key: deployment.environment.name
        value: local
        action: insert
      - key: host.arch
        action: delete
      - key: host.id
        action: delete
      - key: host.name
        action: delete
      - key: process.command
        action: delete
      - key: process.command_args
        action: delete
      - key: process.executable.path
        action: delete
      - key: process.owner
        action: delete
      - key: process.pid
        action: delete
```

On the way in, it adds a `deployment.environment.name` attribute and sets it to `local`. That makes it obvious at a glance which environment our metrics came from. It also uses the `delete` action to remove the host and process attributes we saw in the bare-bones debug output. Those were the ones that were sensitive, redundant, or just noise.

To see the difference, we'll look at the Collector's debug output rather than the Prometheus UI. Resource attributes describe the app producing the telemetry. In this setup, they are not exposed as Prometheus labels, so the debug output is the clearest place to compare before and after. 

In the screenshot, the raw telemetry sits on the left and the processed version on the right. The `deployment.environment.name` attribute has been added (green box). The unnecessary host and process attributes are gone (red boxes).

<img width="2458" height="760" alt="image" src="https://github.com/user-attachments/assets/5c707ece-3a70-4571-86bc-a7fd44df8540" />

**2. The `attributes/metrics` processor modifies the labels attached to each metric.**
```
attributes/metrics:
    actions:
      - key: net.host.port
        action: delete
```
Where the resource processor works on the telemetry's source, this one works on the labels attached to each metric. We use it to delete `net.host.port`, a label that describes the network rather than anything the app itself is doing. Dropping it keeps our metrics centered on application behavior, and it lowers cardinality at the same time.

Because metric labels show up as Prometheus labels, this is easy to verify there. Looking back at the bare-bones query for `http_server_duration_milliseconds_count`, Prometheus returned three time series, and every one of them carried a `net_host_port` label. 

<img width="2511" height="732" alt="image" src="https://github.com/user-attachments/assets/ee907835-df72-4879-b754-6f04c639ce76" />

Now switch back to the live Prometheus UI and run the HTTP request duration query again:
```
http_server_request_duration_milliseconds_count
```
This time the metric comes back without the `net_host_port` label. That's the effect of the attributes processor.

<img width="2505" height="591" alt="image" src="https://github.com/user-attachments/assets/9794bd70-9a05-4107-b7b5-0aa52e14c5da" />

**3. The `filter/exclude_metrics` processor drops entire metrics we don't need.**
```
filter/exclude_metrics:
    error_mode: ignore
    metrics:
      metric:
        - 'name == "v8js.memory.heap.space.physical_size"'
```
So far we've trimmed a single label. This processor goes a step further and drops an entire metric. 

Not every metric our app emits is useful, and every one we hold onto adds to storage, query cost, and overall telemetry volume. 

In our config, we're removing a low-level V8 runtime metric, `v8js.memory.heap.space.physical_size`, which reports the physical size of individual heap spaces.

That's more detail than this app needs. The memory signals that actually matter, like heap used and heap limit, stay put. Inside the pipeline we run this filter **early**, ahead of the label cleanup and renaming, so we don't spend effort on metrics we're about to drop anyway.

To confirm it worked, we can query the metric in Prometheus before and after. Beforehand the query returns the metric as expected. 

<img width="2502" height="1326" alt="image" src="https://github.com/user-attachments/assets/5684fa7d-b418-4271-83db-46b9c7f63cd6" />

Once the processor is in place the same query comes back empty, because the Collector filtered it out before export.
<img width="2498" height="1189" alt="image" src="https://github.com/user-attachments/assets/929c6991-88c3-4496-bcbd-d3e860fe2516" />

**4. The `metricstransform` processor reshapes metrics, including renaming them.**
```
metricstransform:
    transforms:
      - include: http.server.duration
        action: update
        new_name: http.server.request.duration
```
OpenTelemetry's semantic conventions keep evolving, and metric names occasionally shift from one version to the next. Using this processor, we rename `http.server.duration` to `http.server.request.duration` so our telemetry stays current, and we do it without touching a line of application code.

We can confirm the rename in Prometheus by querying the old name and the new name. Querying the old name returns data before the processor is applied.

<img width="2501" height="1306" alt="image" src="https://github.com/user-attachments/assets/4f30d9a0-d762-48ee-9c51-246332c436e0" />

But afterward that same query comes back empty, since the metric no longer goes by that name. 
<img width="2498" height="1195" alt="image" src="https://github.com/user-attachments/assets/7ad770b8-7349-457d-962e-6239add16935" />

Query the new name instead and the data reappears.

<img width="2499" height="1191" alt="image" src="https://github.com/user-attachments/assets/c0c67874-8db2-4b4b-a727-804dba827a34" />

One detail worth calling out is that the name in Prometheus doesn't match the one in our config. In the Collector configuration it's `http.server.request.duration`, but in Prometheus it shows up as `http_server_request_duration_milliseconds_count` (highlighted in orange). 

<img width="2502" height="1124" alt="image" src="https://github.com/user-attachments/assets/f952efc3-31d7-41b8-a976-6a52f37e7b5e" />

That's the Prometheus exporter doing its job. On the way out it rewrites names to follow Prometheus conventions, swapping dots for underscores and tacking on the unit and metric-type suffix. That's how `http.server.request.duration` ends up as `http_server_request_duration_milliseconds_count`.

<img width="2505" height="1407" alt="image" src="https://github.com/user-attachments/assets/aff1d31c-9bd3-4207-9778-38d13402997e" />

**5. The `batch` processor groups telemetry into batches before exporting.**
```
batch:
    timeout: 5s
    send_batch_size: 512
```
We first introduced this one in the traces processing episode, and it behaves identically here. It gathers telemetry into groups before export. That's far more efficient than shipping each item on its own. Batching is a standard best practice for any production-bound Collector. 

You'll spot it in both pipelines. In the traces pipeline it groups spans, and in the metrics pipeline it groups metric data points. Each pipeline handles its own signal on its own, so spans and metrics are never bundled into the same batch.

_Note: the OpenTelemetry Collector is migrating batching into the exporter's sending queue, and the standalone batch processor is slated for deprecation. For now it remains a common, widely used choice._

**The `service` component is what ties everything together, connecting our receivers, processors, and exporters into an actual pipeline.**

One thing to keep in mind is that defining a processor up in the top-level `processors` section only makes it available. It doesn't put it to work. A processor runs only when we list it inside a pipeline, and that list pulls double duty. It decides both which processors run and the order they run in.
```
service:
  pipelines:
    metrics:
      receivers: [otlp]
      processors: [resource, filter/exclude_metrics, attributes/metrics, metricstransform, batch]
      exporters: [debug, prometheus]
```

**IMPORTANT**

Processors run in the exact order they're listed in the pipeline, so for our metrics that's `resource`, then `filter/exclude_metrics`, then `attributes/metrics`, then `metricstransform`, and finally `batch`.
- Filtering comes **early** on purpose. We drop the metrics we don't want before spending any effort transforming the ones we're keeping.
- `batch` is listed **last** so it groups the fully-processed telemetry right before it leaves the Collector.

With these processors in place, our Collector is no longer just passing data through. It's actively transforming our telemetry, adding useful context, dropping metrics we don't need, removing low-value or sensitive labels, renaming metrics to match current conventions, and preparing our metrics for efficient export.

In this episode, we put processors to work transforming our metrics inside the OpenTelemetry Collector. 

In the next one, we'll shift our focus to the third signal, logs, and see how to collect, process, and export them with OpenTelemetry.
