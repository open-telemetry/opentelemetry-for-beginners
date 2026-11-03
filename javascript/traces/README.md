# OpenTelemetry for Beginners - The JavaScript Journey (Traces)

Getting started with OpenTelemetry can feel overwhelming, but this beginner series for JavaScript developers will guide you step by step.

This guide is where the series begins. It covers what OpenTelemetry is, an overview of the pipelines we'll build, and the **traces** signal. From there, the [Metrics walkthrough](../metrics/README.md) and the [Logs walkthrough](../logs/README.md) build on everything we set up here.

## Prerequisites

Before getting started, make sure you have installed:
- [Node.js](https://nodejs.org/en/download/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/), which includes Docker Compose

> **On Linux without Docker Desktop?** Install [Docker Engine](https://docs.docker.com/engine/install/) and the [Docker Compose plugin](https://docs.docker.com/compose/install/linux/) instead.

## Resources
- [OpenTelemetry documentation](https://opentelemetry.io/docs/)
  - Ask AI (⌘+K shortcut)
  - [Language APIs and SDKs](https://opentelemetry.io/docs/languages/)
  - [Instrumentation](https://opentelemetry.io/docs/concepts/instrumentation/)
  - [Traces](https://opentelemetry.io/docs/concepts/signals/traces/)
  - [OpenTelemetry Collector](https://opentelemetry.io/docs/collector/)
    - [List of OpenTelemetry Collector processors](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor)
- [Jaeger documentation](https://www.jaegertracing.io/docs/)
- [OpenTelemetry YouTube channel](https://www.youtube.com/@otel-official)
  - [OpenTelemetry for Beginners series - The JavaScript Journey](https://youtu.be/iEEIabOha8U?feature=shared)
- [OpenTelemetry Slack channel](https://opentelemetry.io/community/end-user/slack-channel/)

# Episode 1 - What is OpenTelemetry?

🎬 Watch this video to learn what OpenTelemetry is and why it matters.

[![Watch the video](https://img.youtube.com/vi/iEEIabOha8U/0.jpg)](https://youtu.be/iEEIabOha8U)

Now that we've covered the basics, let's get a sneak peek at what we'll be building throughout the series.

# Episode 2 - Overview: Kickstart Your Telemetry Pipelines with OpenTelemetry

In this episode, we'll walk through the telemetry pipelines we're going to build and see how our learning environment is set up.

We'll also check out the series repo and everything you'll need to follow along with the hands-on exercises.

## What we're building

<img width="2049" height="1150" alt="What we are building: the Roll the Dice app with instrumentation, the OTel Collector (receive, process, and export), an observability backend for storage, and an observability frontend for visualization" src="images/what-we-are-building.jpg" />

Our setup has four main parts:
- **Roll the Dice**, a simple Node.js app. We'll use OpenTelemetry **instrumentation** to generate traces, metrics, and logs from it.
- The **OpenTelemetry Collector**, which receives, processes, and exports the telemetry from our app.
- An **observability backend**, where our telemetry is stored.
- An **observability frontend**, where we visualize and explore our telemetry.

Together, these four pieces make up a telemetry pipeline. We'll build one for each signal.

**Traces pipeline**

<img width="1280" height="714" alt="Traces pipeline: traces flow from the Roll the Dice app to the OTel Collector, then to Jaeger for storage and visualization" src="images/traces-pipeline.gif" />

We'll start with traces. We'll use OpenTelemetry's automatic instrumentation to generate traces from our app. 

Then we'll configure the Collector to receive, process, and export them to **Jaeger**, where we'll store and explore them.

**Metrics pipeline**

<img width="1280" height="714" alt="Metrics pipeline: metrics flow from the Roll the Dice app to the OTel Collector, then to Prometheus for storage and visualization" src="images/metrics-pipeline.gif" />

Next, we'll build our metrics pipeline. We'll follow a similar process, but this time we'll use **Prometheus** to store and visualize our metrics.

**Logs pipeline**

<img width="1280" height="714" alt="Logs pipeline: logs flow from the Roll the Dice app to the OTel Collector, then to OpenSearch for storage and OpenSearch Dashboards for visualization" src="images/logs-pipeline.gif" />

Finally, we'll build our logs pipeline. This time, we'll do something a little different and get our first taste of **manual instrumentation**, adding a few lines of code to our app instead of relying on automatic instrumentation.

The rest will look familiar. Our logs travel through the Collector to **OpenSearch**, where we'll store and explore them.

And that completes our three pipelines!

<img width="1280" height="714" alt="Pipelines for all three signals: traces to Jaeger, metrics to Prometheus, and logs to OpenSearch, all through the OTel Collector" src="images/all-three-signals.gif" />

## Our learning environment

<img width="1280" height="714" alt="Learning environment: the Roll the Dice app runs locally, while Docker Compose runs the OTel Collector and the observability backends and frontends" src="images/learning-environment.gif" />

We'll run our Roll the Dice app locally on our computer.

We'll use Docker Compose to run the Collector, along with our observability backends and their visualization tools. This makes it easy to start and manage everything we need for the hands-on exercises.

Here's where you'll find each backend once it's running:

| Signal | Backend | URL |
|---|---|---|
| Traces | Jaeger | http://localhost:16686 |
| Metrics | Prometheus | http://localhost:9090 |
| Logs | OpenSearch Dashboards | http://localhost:5601 |

You won't need all of these right away. Each part of the series only starts the backends it needs, so our environment grows as we add each signal.

## Our two-step approach

For each signal, we'll follow the same two steps. Let's use our traces pipeline as an example.

**Step 1: The bare-bones setup**

<img width="1280" height="714" alt="Bare-bones traces pipeline: the Collector receives traces from the Roll the Dice app and exports them to Jaeger" src="images/bare-bones-traces-pipeline.gif" />

Before we dive into processing, we need to know what our data actually looks like.

So first, we'll build a bare-bones setup with just the essentials to get our telemetry flowing. We'll instrument our app to generate traces, then configure the Collector to receive them and export them to Jaeger.

From there, we'll explore our traces in Jaeger to see what they look like before any processing.

**Step 2: Enter the processors**

<img width="1280" height="714" alt="Traces pipeline with processors: the Collector receives, processes, and exports traces to Jaeger" src="images/traces-pipeline-with-processors.gif" />

Once we've explored our traces, we'll build on that setup by adding **processors** to the Collector configuration. Processors let us modify, enrich, and filter our telemetry before it's exported.

We'll apply different processors to make the changes we want, then head back to Jaeger to see the results.

We'll follow this same approach for metrics and logs. That's why each signal's folder in the repo has two versions, `bare-bones-setup/` and `add-processors/`.

## The series repo

<!-- TODO screenshot: series repo on GitHub, showing the javascript/traces, metrics, and logs folders -->

Everything we'll use in this series lives in this repo. Each signal has its own folder with a README like this one:

```
javascript/
├── traces/
│   ├── README.md
│   ├── bare-bones-setup/
│   └── add-processors/
├── metrics/
│   ├── README.md
│   ├── bare-bones-setup/
│   └── add-processors/
└── logs/
    ├── README.md
    ├── bare-bones-setup/
    └── add-processors/
```

Once you've installed the [prerequisites](#prerequisites), clone the repo so you're ready to follow along:

```bash
# choose a directory of your choice, then clone the repo
git clone https://github.com/open-telemetry/opentelemetry-for-beginners.git
```

In the next episode, we'll start up our learning environment and build our first trace pipeline with a bare-bones setup.

# Episode 3 - Trace Instrumentation & Bare-Bones Collector Configuration

In this episode, we'll build our first pipeline, starting with traces!

## What is a trace?

<img width="1280" height="714" alt="A trace for one request: the GET /rolldice root span covers the whole request, with child spans for middleware and the request handler laid out over time" src="images/what-is-a-trace.gif" />

A **trace** follows a single request as it moves through our app. Each step along the way is recorded as a **span**.

Together, these spans show us how the request moved through our app and how much time it spent at each step.

## The bare-bones setup

<img width="1280" height="714" alt="Bare-bones traces pipeline: the Collector receives traces from the Roll the Dice app and exports them to Jaeger" src="images/bare-bones-traces-pipeline.gif" />

As we'll do with each signal, we'll start simple. We'll instrument our app to generate traces, then configure a bare-bones OpenTelemetry Collector, or **the Collector** for short, to receive them and export them to Jaeger.

This lets us explore our traces before we add any processing.

## Run the learning environment

We'll use two terminals: one for the Roll the Dice app and one for Docker.

**Terminal 1: start the app**

From the directory where you cloned the repo, run:

```bash
cd opentelemetry-for-beginners/javascript/traces/bare-bones-setup
npm install
npm start
```

<img width="1423" height="690" alt="Terminal output of npm install and npm start, with OTEL_SERVICE_NAME=OTel4Beginners highlighted in the start command" src="images/run-app-terminal.png" />

When the app starts, notice our service name, `OTel4Beginners`. We'll see where it comes from [later in this episode](#the-start-script).

**Roll the dice**

In your browser, go to http://localhost:8080/rolldice.

Each time we refresh the page, the app generates a random number between 1 and 6, just like rolling a die. 

It's intentionally simple, so we can focus on our telemetry instead of how the app works.

<img width="1540" height="568" alt="Refreshing the Roll the Dice app at localhost:8080/rolldice, which shows a new random number from 1 to 6 each time" src="images/roll-the-dice.gif" />

**Terminal 2: start the Collector and Jaeger**

Make sure Docker Desktop is running. Then open a second terminal and run these commands from the directory where you cloned the repo:

```bash
cd opentelemetry-for-beginners/javascript/traces/bare-bones-setup
docker compose up
```

<img width="1976" height="718" alt="Docker Desktop with the engine running, next to a terminal running docker compose up in the bare-bones-setup folder" src="images/docker-compose-up.png" />

**Generate some traces**

Once the Collector and Jaeger are running, head back to the Roll the Dice app and refresh the page a few more times. Each refresh sends a request to our app and generates a trace.

<img width="1540" height="568" alt="Refreshing the Roll the Dice app at localhost:8080/rolldice, which shows a new random number from 1 to 6 each time" src="images/roll-the-dice.gif" />

As you refresh, watch the terminal running Docker. You'll see trace data appear as the Collector receives it.

<img width="740" height="836" alt="Collector debug output in the Docker terminal, showing request handler spans with their trace IDs, timestamps, and attributes" src="images/collector-debug-output.png" />

So our app is generating traces, and the Collector is receiving them. But how does our app actually generate these traces?

## What is instrumentation?

The answer is **instrumentation**. Instrumentation enables our app to generate telemetry, like traces, metrics, and logs.

OpenTelemetry gives us different ways to instrument an application.

Here are the ones we'll use in this series:

| | Automatic instrumentation | Manual instrumentation |
|---|---|---|
| Also known as | Zero-code solutions | Code-based solutions |
| Code changes | None | Add code |
| Captures | Libraries and runtime | Our app's custom logic |

**Automatic instrumentation**, or auto-instrumentation for short, is ideal when you're getting started or when changing the app isn't an option. 

It captures telemetry from our app's libraries and runtime environment, giving us useful visibility with very little setup.

**Manual instrumentation** gives us deeper insight. It lets us generate custom telemetry directly from our app logic, alongside the data collected by auto-instrumentation.

For traces, we'll use auto-instrumentation. Let's take a look at the setup that makes it possible.

## The OTel packages

Open the `javascript/traces/bare-bones-setup` folder in your code editor, then open `package.json`.

<img width="1471" height="793" alt="package.json with the three OpenTelemetry dependencies highlighted" src="images/package-json-packages.png" />

We've installed three OTel packages for this setup:

- **`@opentelemetry/sdk-node`** is the OTel SDK for Node.js. It brings together the components we need to collect and export telemetry.
- **`@opentelemetry/auto-instrumentations-node`** adds instrumentation for popular libraries and frameworks, so we can generate telemetry without changing our app code.
- **`@opentelemetry/exporter-trace-otlp-grpc`** is the OTLP trace exporter. It sends traces over gRPC to the Collector or an OTLP-compatible backend.

Together, these packages let our app generate traces automatically and send them to the Collector.

## instrumentation.js

This file sets up our app to generate traces and send them to the Collector.

```js
const opentelemetry = require('@opentelemetry/sdk-node');

const {
  getNodeAutoInstrumentations,
} = require('@opentelemetry/auto-instrumentations-node');

const {
  OTLPTraceExporter,
} = require('@opentelemetry/exporter-trace-otlp-grpc');

const sdk = new opentelemetry.NodeSDK({
  traceExporter: new OTLPTraceExporter({
     url: 'http://localhost:4317', 
  }),
  instrumentations: [
    getNodeAutoInstrumentations({
      '@opentelemetry/instrumentation-router': { enabled: false },
    }),
  ],
});

sdk.start();
```

It does four main things:

1. **Imports the three OTel packages** we just saw in `package.json`.
   - The OTel SDK, the auto-instrumentation package, and the OTLP trace exporter.
2. **Creates a Node SDK instance** and configures the exporter to send traces to the Collector.
   - We use port `4317`, the default port for OTLP over gRPC.
3. **Enables auto-instrumentation** for common Node.js libraries.
   - We've turned off router instrumentation because Express instrumentation already covers those steps. Keeping both enabled would create duplicate spans.
4. **Starts the SDK** so our app can generate and send traces.

## The start script

Back in `package.json`, we've added a custom `start` script:

```json
"start": "OTEL_SERVICE_NAME=OTel4Beginners node --require ./instrumentation.js app.js"
```

<img width="1928" height="848" alt="package.json start script with the OTEL_SERVICE_NAME environment variable and the --require flag highlighted, next to the terminal where the app was started" src="images/package-json-start-script.png" />

It does two things:

1. **`OTEL_SERVICE_NAME=OTel4Beginners`** gives our service its name (red).
   - This tells the backend which service produced the telemetry.
   - We set the name through an environment variable so we can configure it without changing our app code.
   - That's the name we saw when we started the app, and we'll use it to find our traces in Jaeger.
2. **`--require ./instrumentation.js`** loads `instrumentation.js` before our app code runs (blue).
   - This order matters because the instrumentation needs to be ready when the app starts.

## Collector configuration

Now that our app is set up to generate traces, let's see how the Collector receives and exports them.

```yaml
# otel/otel-collector-config.yaml
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

  otlp_grpc/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [debug, otlp_grpc/jaeger]
```

We're keeping this configuration bare-bones, so it only has receivers, exporters, and service pipelines.

### Receivers

```yaml
# otel/otel-collector-config.yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
```

Receivers tell the Collector how to accept incoming telemetry.

- We're using the OTLP receiver with gRPC on port `4317` and HTTP on port `4318`. These are the default ports for OTLP.
- Our app sends traces over gRPC, so it uses port `4317`, matching the exporter setup in `instrumentation.js`.

### Exporters

```yaml
# otel/otel-collector-config.yaml
exporters:
  debug:
    verbosity: detailed

  otlp_grpc/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true
```

Exporters define where the Collector sends telemetry data. In this setup, we're using two exporters.

- The **`debug`** exporter prints our trace data to the Collector's output.
  - With `verbosity` set to `detailed`, we can inspect individual spans and their details.
  - That's what we saw in the terminal running Docker when we refreshed the app.
  - It helps us check what's flowing through the Collector during development and debugging.
- The **`otlp_grpc/jaeger`** exporter sends our traces to Jaeger, where we can store and visualize them.
  - The `otlp_grpc` part identifies the exporter type, an OTLP exporter that sends data over gRPC. The name after the slash helps us tell exporters of the same type apart.
  - The endpoint `jaeger:4317` points to our Jaeger service inside Docker, using the default port for OTLP over gRPC.
  - We've set `insecure` to `true` to turn off TLS for our local demo. In production, we'd use TLS to secure the connection.

### Service

```yaml
# otel/otel-collector-config.yaml
service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [debug, otlp_grpc/jaeger]
```

Defining receivers and exporters alone doesn't enable them. We need to include them in a pipeline, and that's where the `service` section comes in. It ties everything together by defining how data moves through the Collector.

In our traces pipeline:
- The **`otlp`** receiver accepts traces from our app over gRPC and passes them to both exporters.
- The **`debug`** exporter prints them to the terminal.
- The **`otlp_grpc/jaeger`** exporter sends them to Jaeger, where we can store and visualize them.

<img width="1280" height="714" alt="Traces pipeline: the Roll the Dice app sends traces to the Collector's otlp receiver over gRPC. Inside the traces pipeline, the receiver passes them to both the debug exporter, which prints them to the terminal, and the otlp_grpc/jaeger exporter, which sends them to Jaeger to store and visualize" src="images/collector-configuration.gif" />

## Explore our traces in Jaeger

Now let's check that our traces are reaching Jaeger.

**1. Find our traces**

Go to http://localhost:16686 to access the Jaeger UI. In the **Service** dropdown (orange box), select `OTel4Beginners`, the service name we set in our start script. Then click **Find Traces** (blue arrow).

<img width="1561" height="816" alt="Jaeger search page with OTel4Beginners selected in the Service dropdown and an arrow pointing to the Find Traces button" src="images/jaeger-search.png" />

> **Don't see `OTel4Beginners`?** Refresh the Roll the Dice app a few more times. Check that trace data appears in the terminal running Docker, then refresh Jaeger and try again.

**2. Look at the results**

Now we can see the traces generated by our app, flowing through the Collector and into Jaeger.

<img width="1750" height="835" alt="Jaeger search results: a chart of traces plotted by time and duration, and a list of OTel4Beginners GET /rolldice traces with 2 spans each" src="images/jaeger-results.png" />

- The chart at the top shows our traces as dots, plotted by when each request happened and how long it took.
- Each result in the list is a trace, representing one request to our app.
- Each trace is made up of spans, the steps that make up that request.

Click on one of the traces (orange box) to take a closer look.

**3. Read the trace timeline**

<img width="1842" height="409" alt="Jaeger trace timeline: the GET /rolldice root span highlighted in yellow, with the request handler child span highlighted in blue below it" src="images/jaeger-trace-timeline.png" />

This is the trace timeline. It brings the [trace diagram](#what-is-a-trace) to life with real trace data.

- At the top is our **root span**, `GET /rolldice` (yellow box), which covers the whole request.
- Below it is a **child span** (blue box) showing the work done by our request handler.
- Each bar shows how long that span took.

Our diagram also showed a middleware step, but our app is simple, so there's only one step here. In a real app, you'd see many more spans, like database calls or requests to other services.

**4. Open the root span**

Click on the root span, `GET /rolldice`. The span opens up to show its details, in two sections: **Tags** and **Process** (orange box). Expand both to take a closer look.

<img width="1935" height="491" alt="The GET /rolldice span expanded in Jaeger, showing the collapsed Tags and Process sections" src="images/jaeger-span-details.png" />

### Tags: span attributes

<img width="1776" height="852" alt="The expanded Tags section of the GET /rolldice span, with client.address, network.peer.address, network.peer.port, server.port, and user_agent.original highlighted" src="images/jaeger-tags.png" />

The **Tags** section gives us details about the request. These are **span attributes**, like the route, method, and status code. They help us understand how our app handled the request.

We can also see details like the user agent, IP addresses, and ports that we may not need. Some could reveal information about users or their devices in production. We're running locally, but some values have been blurred in the screenshots to show what we'd want to handle carefully.

In the next episode, we'll use processors to remove these attributes (orange boxes):
- `client.address`
- `network.peer.address`
- `network.peer.port`
- `server.port`
- `user_agent.original`

### Process: resource attributes

<img width="1650" height="834" alt="The expanded Process section of the GET /rolldice span, with host and process attributes highlighted and their values blurred" src="images/jaeger-process.png" />

The **Process** section shows details about the service that created this telemetry, like the host and the running process. These are called **resource attributes**. Instead of describing the request, they describe the source of our telemetry.

Like the Tags section, this includes details we may not need, such as host identifiers and process details. Some could reveal information about our machines in production, so their values have been blurred in the screenshots.

In the next episode, we'll use processors to remove these too (orange boxes):
- `host.arch`
- `host.id`
- `host.name`
- `process.command`
- `process.command_args`
- `process.executable.path`
- `process.owner`
- `process.pid`

And that's our first pipeline up and running! In the next episode, we'll add processors to modify, enrich, and clean up our traces before they reach Jaeger.

# Episode 4 - Processing Traces: OpenTelemetry Collector in Action

In the last episode, we built our first pipeline and explored our traces in Jaeger. 

Along the way, we spotted [span attributes](#tags-span-attributes) and [resource attributes](#process-resource-attributes) we don't need. We also noticed our traces were missing the environment they came from.

In this episode, we'll fix that using **processors**.

## Adding processors

<img width="1280" height="714" alt="Adding processors: inside the Collector's traces pipeline, traces go from the otlp receiver through the resource processor, which adds the environment and drops host and process details, then the attributes/traces processor, which drops client and network details. The otlp_grpc/jaeger exporter groups traces with its sending queue, then sends them to Jaeger" src="images/adding-processors.gif" />

Processors run inside the Collector, between the receiver and the exporter. They let us change our telemetry before it leaves the Collector.

In this episode, we'll use them to:
- Add the environment our traces came from and drop host and process details we don't need.
- Drop client and network details that are sensitive or noisy.

We'll also configure our Jaeger exporter to send traces in batches.

All of this happens in the Collector, so we won't need to touch our app's code.

## Project folders

1. `javascript/traces/bare-bones-setup/` (**Episode 3**)
   - Instruments the Roll the Dice app and sends traces to the Collector.
   - The Collector exports traces to Jaeger with no additional processing.

2. `javascript/traces/add-processors/` (**Current episode**)
   - Uses the same app and instrumentation as `bare-bones-setup`.
   - Adds processors to the Collector configuration and batches traces before exporting them to Jaeger.

## Run the updated setup

If you've been following along, you should still have two terminals open: one running the Roll the Dice app and one running Docker.

**Terminal 1: restart the app**

> First, press **Ctrl + C** to stop the running app.

```bash
cd ../add-processors
npm install
npm start
```

The `add-processors` folder has its own dependencies, so we run `npm install` before starting the app.

**Terminal 2: restart the Collector and Jaeger**

```bash
docker compose down
cd ../add-processors
docker compose up
```

**Generate some traces**

Once everything is running again, head back to the Roll the Dice app at http://localhost:8080/rolldice and refresh the page a few times.

<img width="1540" height="568" alt="Refreshing the Roll the Dice app at localhost:8080/rolldice, which shows a new random number from 1 to 6 each time" src="images/roll-the-dice.gif" />

In the terminal running Docker, we'll see our traces flowing through the Collector. Notice `deployment.environment.name` near the top (orange box). That's our first hint that something has changed.

<img width="1798" height="1796" alt="Collector debug output showing the resource attributes of a processed trace, with deployment.environment.name set to local highlighted, followed by GET /rolldice spans with fewer span attributes" src="images/collector-debug-output-processed.png" />

**Check Jaeger**

Go to http://localhost:16686, select `OTel4Beginners` in the **Service** dropdown (orange box), and click **Find Traces** (blue arrow).

<img width="2048" height="1065" alt="Jaeger search page with OTel4Beginners selected in the Service dropdown, an arrow pointing to the Find Traces button, and results showing 10 OTel4Beginners GET /rolldice traces with 2 spans each" src="images/jaeger-results-processed.png" />

> **Don't see new traces right away?** Give it a few seconds, then refresh. We'll see why when we get to [batching](#batching-in-the-exporter).

We can't tell from this overview alone, but these traces are no longer raw.

The Collector processed them before sending them to Jaeger. As we walk through the Collector configuration, we'll compare what a trace looked like before with what it looks like now.

## Collector configuration

In your code editor, open the `javascript/traces/add-processors` folder. Then open `otel/otel-collector-config.yaml`.

```yaml
# otel/otel-collector-config.yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

processors:
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

  attributes/traces:
    actions:
      - key: user_agent.original
        action: delete
      - key: client.address
        action: delete
      - key: network.peer.address
        action: delete
      - key: network.peer.port
        action: delete
      - key: server.port
        action: delete

exporters:
  debug:
    verbosity: detailed

  otlp_grpc/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true
    sending_queue:
      sizer: items
      batch:
        flush_timeout: 5s
        min_size: 512

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [resource, attributes/traces]
      exporters: [debug, otlp_grpc/jaeger]
```

Our bare-bones configuration had receivers, exporters, and a `service` section. Here, we've added a `processors` section with the `resource` and `attributes/traces` processors.

### The resource processor

The `resource` processor lets us change **resource attributes**, the attributes that describe where our telemetry came from. In Jaeger, these are the attributes we saw in the [Process](#process-resource-attributes) section.

We're using it to add our environment and remove details we don't need.

**Add the environment**

```yaml
# otel/otel-collector-config.yaml
processors:
  resource:
    attributes:
      - key: deployment.environment.name
        value: local
        action: insert
```

Each entry follows the same pattern:
- **`key`** is the attribute we want to change. Here, it's `deployment.environment.name`.
- **`value`** is set to `local` because our app is running locally.
- **`action`** tells the Collector what to do. The `insert` action adds the attribute if it's missing.

That way, we can tell which environment our traces came from.

**Remove host and process details**

```yaml
# otel/otel-collector-config.yaml
processors:
  resource:
    attributes:
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

These are the host and process details we highlighted in the last episode. Each entry uses the `delete` action, so the Collector removes that attribute before our traces reach Jaeger.

We're keeping details we still find useful, like the Node.js runtime version.

**Before and after**

<img width="2048" height="946" alt="The Process section of a raw trace next to a processed trace. The raw trace has host and process attributes highlighted. The processed trace has deployment.environment.name set to local, and the highlighted attributes are gone" src="images/jaeger-process-before-after.jpg" />

The processed trace now includes `deployment.environment.name`, set to `local`. The host and process details we deleted are gone, and the useful runtime details are still there.

### The attributes processor

```yaml
# otel/otel-collector-config.yaml
processors:
  attributes/traces:
    actions:
      - key: user_agent.original
        action: delete
      - key: client.address
        action: delete
      - key: network.peer.address
        action: delete
      - key: network.peer.port
        action: delete
      - key: server.port
        action: delete
```

The `attributes` processor lets us change **span attributes**, the details about the request itself. In Jaeger, these are the attributes we saw in the [Tags](#tags-span-attributes) section.

We don't need the user agent, IP addresses, or ports for this demo, and some of them could reveal information about our users. So we've listed them here with the `delete` action.

The `/traces` part of the name works the same way as in `otlp_grpc/jaeger`. It's a name we chose to tell processors of the same type apart.

**Before and after**

<img width="2048" height="770" alt="The Tags section of a raw trace next to a processed trace. The raw trace has client.address, network.peer.address, network.peer.port, server.port, and user_agent.original highlighted. In the processed trace, those attributes are gone" src="images/jaeger-tags-before-after.jpg" />

The client and peer addresses, the server port, and the user agent are gone. The details about the request itself are still there.

### Batching in the exporter

```yaml
# otel/otel-collector-config.yaml
exporters:
  debug:
    verbosity: detailed

  otlp_grpc/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true
    sending_queue:
      sizer: items
      batch:
        flush_timeout: 5s
        min_size: 512
```

We've made one more change in the exporters to batch our traces. **Batching** lets the Collector send telemetry in fewer, larger requests, which reduces network overhead as traffic grows.

We set up the `otlp_grpc/jaeger` exporter in the last episode. What's new is its `sending_queue`, which handles batching:
- **`flush_timeout: 5s`** is how long the exporter can wait before sending a batch, even if it isn't full.
- **`min_size: 512`** with **`sizer: items`** tells the exporter to send a batch once it reaches 512 items. For traces, those items are spans.

Whichever comes first, 512 spans or the 5-second timeout, triggers the export.

That explains the delay we saw in Jaeger earlier. A few dice rolls won't generate 512 spans, so our batch is sent when the timeout expires. The `debug` exporter doesn't batch, which is why our traces showed up in the terminal right away.

These values are a starting point. We can adjust them to suit our needs.

> **Seen a `batch` processor in other examples?** Batching used to be configured as a separate processor. The Collector is moving batching into the exporter's sending queue, which is what we're using here.

### Service

```yaml
# otel/otel-collector-config.yaml
service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [resource, attributes/traces]
      exporters: [debug, otlp_grpc/jaeger]
```

As we saw in the last episode, the `service` section is where everything comes together. In our traces pipeline:
- The **`otlp`** receiver accepts traces from our app.
- The **`resource`** processor adds our environment and removes the host and process details we don't need.
- The **`attributes/traces`** processor removes client and network details from our spans.
- The **`debug`** exporter prints the processed traces to the terminal.
- The **`otlp_grpc/jaeger`** exporter groups them with its sending queue and sends them to Jaeger.

The Collector runs processors in the order they're listed. 

For these two, the order doesn't matter since they change different attributes. Later in the series, we'll see a pipeline where it does.

<img width="1280" height="714" alt="Traces pipeline with processors: the Roll the Dice app sends traces to the Collector's otlp receiver. The resource processor adds the environment and drops host and process details, then the attributes/traces processor drops client and network details. The processed traces go to the debug exporter, which prints them to the terminal, and the otlp_grpc/jaeger exporter, which groups them with its sending queue and sends them to Jaeger" src="images/putting-it-all-together-processors.gif" />

And that's how our raw traces become processed traces, all without changing a single line of our app's code!

In the next episode, we'll move on to our second signal, metrics. We'll instrument our app for metrics and build a bare-bones pipeline for them.

When you're ready, head over to the [Metrics walkthrough](../metrics/README.md).
