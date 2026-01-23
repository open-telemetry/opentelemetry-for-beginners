# OpenTelemetry for Beginners - The JavaScript Journey

Getting started with OpenTelemetry can feel overwhelming, but this beginner series for JavaScript developers will guide you step by step.

First, we’ll answer the question: What is OpenTelemetry?

# Episode 1 - What is OpenTelemetry?
🎬 Watch this video to learn what OpenTelemetry is and why it matters.

[![Watch the video](https://img.youtube.com/vi/iEEIabOha8U/0.jpg)]([https://www.youtube.com/watch?v=fZRwVwCvLAg](https://youtu.be/iEEIabOha8U))

Now that you’ve covered the basics in Episode 1, let’s move on to Episode 2 to explore the architecture and objectives that will kickstart your trace pipeline.

# Episode 2 - Overview: Kickstart Your Trace Pipeline with OpenTelemetry
## Learning Environment Architecture
<img width="1906" alt="image" src="https://github.com/user-attachments/assets/71773bfa-e488-4e8f-92fd-d36468827a75" />

## Objectives 
- Instrument a Node.js app using the Auto Instrumentation Module to generate traces and send them to the OpenTelemetry Collector.
- Configure the OpenTelemetry Collector to receive, process, and export traces to the Jaeger backend.
- Use the Jaeger UI to visualize and verify that the traces have been correctly processed.

**Note:**
- Docker runs the OTel Collector and Jaeger side by side with our app for easy setup and integration. 

<img width="1918" alt="image" src="https://github.com/user-attachments/assets/a398be28-3bbc-4895-b343-8cbc09fac606" />

**Before we dive into fancy processing, we need to know what our data actually looks like.**

To get there, we’ll set up a baseline trace flow,  just the essentials.

<img width="2554" height="1435" alt="image" src="https://github.com/user-attachments/assets/f6713274-9dab-423d-91d9-fd7462eb60d4" />

**Next, we’ll keep the same setup and introduce processors to modify trace data, then verify the results in the Jaeger UI.**
<img width="2558" height="1436" alt="image" src="https://github.com/user-attachments/assets/ec01118b-e02e-49ef-80bb-913b0f66811a" />

## Resources
- [OpenTelemetry documentation](https://opentelemetry.io/docs/)
  - Ask AI (⌘+K shortcut)
  - [Language APIs and SDKs](https://opentelemetry.io/docs/languages/)
  - [Instrumentation](https://opentelemetry.io/docs/concepts/instrumentation/)
  - [OpenTelemetry Collector](https://opentelemetry.io/docs/collector/)
    - [List of OpenTelemetry Collector processors](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor) 
- [OpenTelemetry YouTube channel](https://www.youtube.com/@otel-official)
  - [OpenTelemetry for Beginners series - The JavaScript Journey](https://youtu.be/iEEIabOha8U?feature=shared)
- [OpenTelemetry Slack channel](https://opentelemetry.io/community/end-user/slack-channel/)

Now that you’ve covered the pipeline architecture in Episode 2, let’s move on to Episode 3 to set up the learning environment.

#  Episode 3 - Set Up Your Learning Environment: Ready, Steady, Trace!

## Run the learning environment locally
**Before getting started, make sure you have installed:**
- [Node.js](https://nodejs.org/en/download/) 
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Docker Compose](https://docs.docker.com/compose/install/)
  
**Clone the project**
```
# Choose a directory of your choice
git clone https://github.com/open-telemetry/opentelemetry-for-beginners.git
cd opentelemetry-for-beginners/javascript/traces/bare-bones-setup
```
**Start the server**

Execute the following commands:
```
npm install
npm start
```
**Verify the app is running**

In your browser, go to the following URL: http://localhost:8080/rolldice

Refresh the page multiple times. This app will generate a random number from 1–6, just as if you were rolling a die.

![Roll the dice mov](https://github.com/user-attachments/assets/32f80dc2-93b0-4578-91db-17b316a79760)

**Using Docker, run the OpenTelemetry Collector and Jaeger**

In this series, we’ll refer to the OpenTelemetry Collector simply as the Collector.
Before getting started, make sure Docker Desktop is open and running.

<img width="2548" height="1440" alt="image" src="https://github.com/user-attachments/assets/34b5d38f-01b9-4294-a61e-33c6f40e3dda" />

```
# In a different terminal, within the project directory
docker compose up --build 
```
**Refresh the Roll the Dice app page multiple times to send traces to the Collector**

Take a look at the terminal that is running Docker.

You will be able to see the logs of traces that are flowing through the Collector.

<img width="997" height="990" alt="image" src="https://github.com/user-attachments/assets/183a32a9-2752-4f56-a770-b76292019859" />

**Verify that the Collector is sending traces to the Jaeger backend**
1. Go to the following URL (http://localhost:16686/) to access the Jaeger UI. 

2. Click on the "Service" section (orange box) to view all the services that are sending traces to Jaeger.

<img width="2560" height="1296" alt="image" src="https://github.com/user-attachments/assets/2fb3c82f-d65b-4400-a8a9-2a71a51658b0" />

In our setup, the service name was set to "OTel4Beginners". 

Select the service "OTel4Beginners" then click on the "Find Traces" button (blue arrow).

If you don't see the service name "OTel4Beginners", refresh the Roll the Dice app page a few times. Verify that the Collector is receiving telemetry by checking its logs (terminal running Docker), then refresh the Jaeger UI page again.

You’ll now see traces generated by the application flowing through the Collector and into Jaeger.
<img width="2544" height="1291" alt="image" src="https://github.com/user-attachments/assets/eeb78946-8d58-4b4f-a6b3-1061bd06b4a8" />

With your environment set up and a chance to visualize traces generated by your app in Jaeger, you might be wondering: how does your application actually create these traces? That’s where `instrumentation` comes in.

# Episode 4 - Back to Basics: Trace Instrumentation and Bare-Bones OTel Collector Configuration

To observe your application or infrastructure, we first need to instrument it. In other words, we’re enabling it to generate traces, metrics, and logs.

Using OpenTelemetry, we can instrument your code in two primary ways:
- Automatic instrumentation (AKA zero-code solutions)
- Manual instrumentation (AKA code-based solutions)

`Automatic instrumentation` is ideal for getting started or when we can’t modify the app itself. It automatically captures telemetry from the libraries your app uses and the environment it runs in…

`Manual instrumentation` gives you deeper insight by generating telemetry directly from your app. Using the OpenTelemetry API, you can create custom traces, metrics, and logs that build on the data collected automatically.

In this episode, we will focus on `automatic instrumentation` so we can generate traces and visualize them in Jaeger. 

In our setup, the following OTel packages have been installed:  
<img width="2087" height="1145" alt="image" src="https://github.com/user-attachments/assets/b3848c07-d99d-4f92-9817-551fc66bf1ef" />

`@opentelemetry/sdk-node` is the core OpenTelemetry SDK for Node.js. It gives our app the tools it needs to generate, manage, and collect telemetry such as traces and metrics.

With `@opentelemetry/auto-instrumentations-node`, popular Node.js libraries and frameworks are instrumented automatically, allowing our app to generate telemetry with zero code changes.

`@opentelemetry/exporter-trace-otlp-grpc` sends the telemetry our app generates to any OTLP-compatible collector or backend over gRPC, moving trace data out of our app so it can be processed, stored, and visualized.

**instrumentation.js**

This file prepares your app to generate traces through auto-instrumentation and send this trace data to a collector or backend.

```
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
  instrumentations: [getNodeAutoInstrumentations()],
});

sdk.start();
```

`instrumentation.js` performs four main tasks:

1. Import the OpenTelmetry packages required for tracing.
```
const opentelemetry = require('@opentelemetry/sdk-node');
const { getNodeAutoInstrumentations } = require('@opentelemetry/auto-instrumentations-node');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-trace-otlp-grpc');
```

2. Initialize the tracing system by creating a new `NodeSDK` instance.

```
const sdk = new opentelemetry.NodeSDK({
  // configuration goes here
});
```

3. Configure automatic instrumentation and export traces to the local OpenTelemetry Collector.

```
const sdk = new opentelemetry.NodeSDK({
  traceExporter: new OTLPTraceExporter({
    url: 'http://localhost:4317',
  }),
  instrumentations: [getNodeAutoInstrumentations()],
});

```
4. Start the SDK to begin recording and sending traces to the Collector.
```
sdk.start();
```
**IMPORTANT**

- The instrumentation setup and configuration must run **before** your application code.
  - A common way to ensure this is by using the `--require` flag.
- In a properly instrumented application, the service name is set using an environment variable, for example `OTEL_SERVICE_NAME=OTel4Beginners`.
- To ensure this, we added the following `start` script to `package.json` (see below).
  
<img width="2552" height="1369" alt="image" src="https://github.com/user-attachments/assets/c2d377c1-6b03-4b6c-9dc1-f9f419d77f95" />

## OpenTelemetry Collector Configuration

In this series, we’ll refer to the OpenTelemetry Collector simply as the **Collector**.
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

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [debug, otlp/jaeger]
```
**Our Collector configuration is made up of three components:**
1. Receivers
2. Exporters
3. Service 

**Receivers tell the Collector how to accept incoming telemetry data**
```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

```
- The OTLP receiver is configured to accept telemetry over both gRPC (4317) and HTTP (4318).
- This allows our app to send trace data to the Collector using standard OTLP endpoints.
  
**Exporters send data from the Collector to the OTLP-compliant backend(s) of your choice.**
```
exporters:
  debug:
    verbosity: detailed

  otlp/jaeger:
    endpoint: jaeger:4317
    tls:
      insecure: true
```
- The `debug` exporter prints the telemetry data to the Collector’s logs in a detailed way.
  - It displays what data is flowing through the Collector and is useful for debugging or development. 
  
<img width="997" height="990" alt="image" src="https://github.com/user-attachments/assets/183a32a9-2752-4f56-a770-b76292019859" />
 
- The **`otlp/jaeger` exporter** forwards telemetry to a Jaeger backend running at port **4317**.

**The `service` component defines how data moves through the Collector.**
```
service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [debug, otlp/jaeger]
```
- Our configuration defines a pipeline for traces.
- The traces sent from the app is received by the **`otlp` receiver**.
- The **`debug` exporter** logs traces to the terminal where the Collector is running.
- The **`otlp/jaeger` exporter** forwards traces to Jaeger. 

**With the exporters in place, let’s switch to the Jaeger UI to see those traces in action.** 
<img width="2547" height="1259" alt="image" src="https://github.com/user-attachments/assets/15e023bd-234c-455d-8606-a667ef7de5de" />

**Click on one of the traces (orange box)**
<img width="2541" height="1266" alt="image" src="https://github.com/user-attachments/assets/ea763fb5-9e58-4e09-b39b-118e94929684" />

**Click on its root span (GET/rolldice span)**
<img width="2560" height="492" alt="image" src="https://github.com/user-attachments/assets/7e64318e-b710-43a7-8aa9-27679fbb4a6f" />

**Expand the `Tags` and `Process` sections to view the metadata about traces collected from the app**
<img width="2560" height="652" alt="image" src="https://github.com/user-attachments/assets/787ec066-a512-4a71-8145-c626fcdf3e0c" />

**The `Tags` section shows details about what happened during a request.** 

<img width="2545" height="1322" alt="image" src="https://github.com/user-attachments/assets/6ce09abd-55bd-46a0-94a6-e3e33e9dc91d" />

It includes span attributes(e.g., routes, methods, errors) to help you understand app behavior.

This trace includes some sensitive or unnecessary network details, such as `http.user_agent`, IP addresses, and other low-value information like ports.
The actual values have been blurred in the screenshots, but the attribute types shown reflect what is included by default.

These attributes will be addressed and cleaned up in a later episode using OpenTelemetry Collector processors:
- http.user_agent
- net.host.ip
- net.host.port
- net.peer.ip
- net.peer.port

**The `Process` section shows information about the app or service that created the trace.**

<img width="2557" height="1292" alt="image" src="https://github.com/user-attachments/assets/a8db5812-9c12-47e3-a87e-f41a8c722fc9" />

This section contains some sensitive or low-value host and process details.
While the values are blurred in the screenshots, the attribute types shown are included by default.

These will be cleaned up in the next episode using processors:

  - host.arch
  - host.id
  - host.name
  - process.command
  - process.command_args
  - process.executable.path
  - process.owner
  - process.pid

Now that traces are being generated and exported through the Collector, we can start processing that data before it is sent to the backend.

# Episode 5 - Processing Traces: OpenTelemetry Collector in Action

## Project folders 

<img width="2560" height="1439" alt="Image" src="https://github.com/user-attachments/assets/c7333330-f10e-4535-9251-c4f2fc7f82d4" />

1. `bare-bones-setup/` (**Episode 4**)
- Instruments the Roll the Dice app and sends traces to the OpenTelemetry Collector.
- The Collector forwards traces to Jaeger for storage and visualization with no additional processing.

<img width="2558" height="1440" alt="Image" src="https://github.com/user-attachments/assets/26f24e22-e97c-4b67-85c4-c8145f541472" />

2. `add-processors/` (**Current episode**)
- Uses the same setup as `bare-bones-setup`, but applies processors to incoming traces.
- These processors enrich resource metadata, remove low-value or sensitive attributes, and batch traces for more efficient exporting.
  
**Navigate to the `add-processors` folder to apply the processor configuration:**
```
# from the javascript/traces directory
cd ../add-processors
``` 
**In the terminal running Docker, stop and restart the OpenTelemetry Collector and Jaeger.**
```
# stop the running containers
CTRL + C

# restart with the updated configuration
docker compose up --build 
```
This reloads the Collector with the updated processor configuration. 

**Refresh the Roll the Dice app page multiple times to send traces to the newly configured OpenTelemetry Collector.**
![Roll the dice mov](https://github.com/user-attachments/assets/32f80dc2-93b0-4578-91db-17b316a79760)

**Check the Collector logs to verify that traces are flowing through the Collector.**
<img width="890" height="997" alt="image" src="https://github.com/user-attachments/assets/a8c0a4d1-9244-40da-8b8c-1f508547e9cb" />

**Open the Jaeger UI and take a look at the new traces coming in.**
<img width="2542" height="1267" alt="image" src="https://github.com/user-attachments/assets/7f4a5cb5-f59f-4d77-b640-7cf0a8e7eb1a" />

Now that we’ve confirmed the newly transformed traces are flowing through the Collector, let’s examine the updated configuration to understand how trace data is processed before export.

After that, we’ll look at the new traces in more detail to see how the processors have transformed them.

## New OpenTelemetry Collector Configuration

### Three processors have been added to the existing Collector configuration to modify trace data before export:
1. `resource` 
2. `attributes` 
3. `batch` 

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

  attributes:
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

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [resource, attributes, batch]
      exporters: [debug, otlp/jaeger]
```
### Processors modify, filter, or enrich telemetry data within the Collector before it is exported.

**1. The `resource` processor modifies metadata about the service or host.**
```
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
```
**In this configuration, the resource processor inserts a new resource attribute called `deployment.environment.name` and sets its value to `local`.**

This makes it easier to identify where traces are coming from when working across multiple environments.

**Newly processed trace**
<img width="2560" height="994" alt="image" src="https://github.com/user-attachments/assets/a057f3c0-8af6-4eb6-a435-c6f2c30290ca" />

**In addition, the following resource attributes were deleted to remove low-value or sensitive host and process metadata.**  

```
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
```

This helps reduce noise, improve privacy, and keep trace data focused.

**Old traces from the original Collector configuration:**
<img width="2558" height="1289" alt="image" src="https://github.com/user-attachments/assets/a177df0b-4183-4f02-8ffa-1aad6f8684b5" />

**New traces from the new Collector configuration:**
<img width="2560" height="984" alt="image" src="https://github.com/user-attachments/assets/1c710cf6-b81f-4aa7-849d-d721e8ce2f55" />

**2. The `attributes` processor modifies, adds, or removes span attributes.**

The following span attributes were deleted to keep span data focused on application behavior rather than client or network details.

```
attributes:
    actions:
      - key: http.user_agent
        action: delete
      - key: net.host.ip
        action: delete
      - key: net.host.port
        action: delete
      - key: net.peer.ip
        action: delete
      - key: net.peer.port
        action: delete
```

**Old traces from the original Collector configuration:**
<img width="2549" height="1247" alt="image" src="https://github.com/user-attachments/assets/f0aee2dc-c218-4966-b867-eb4161523ad0" />

**New traces from the new Collector configuration:**
<img width="2560" height="1190" alt="image" src="https://github.com/user-attachments/assets/7980e73b-af1f-4336-aedb-49e1eb1257c9" />

**3. The `batch` processor groups telemetry data into batches before exporting.**
```
batch:
    timeout: 5s
    send_batch_size: 512

```
As a best practice, the `batch` processor should almost always be included in production-ready Collector configurations.

Adjust these parameters to fit your specific use case.

**The service component was updated to tie the receivers, processors, and exporters together into a traces pipeline.**

```
service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [resource, attributes, batch]
      exporters: [debug, otlp/jaeger]
```

**IMPORTANT**

In the `service` component, processors are applied in the order they are listed in the pipeline.

The `batch` processor should be listed **last** so it can batch the final version of the data before exporting.

In the next episode, we’ll shift focus from traces to metrics and walk through how to collect, process, and export application metrics using the OpenTelemetry Collector.
