# Example 1

- [Example 1](#example-1)
  - [Structure](#structure)
  - [Description](#description)
  - [Images](#images)
  - [Verify your solution](#verify-your-solution)
  - [Notes](#notes)
  - [The demo-app](#the-demo-app)

## Structure

This `example` has the following structure:
* `/files`: Files that can be used to help with solving this example
* `solution`: An example solution that can be used as reference if stuck and that may be discussed at the end of the example

## Description

The goal of this example is to create a setup with 2 `demo-app` instances that send trace information to a `opentelemetry collector` which is deployed
within the same namespace.

Said collector is then to used to send the information to the Jaeger instance.

The `demo-app` should use `http` to send data to the `opentelemetry collector`

Use the learned knowledge and the [Collector Configuration Documentation](https://opentelemetry.io/docs/collector/configuration/) to configure the collector instance.

The OTLP endpoint of Jaeger is:
```yaml
      otlp:
        endpoint: "jaeger-all-in-one-collector.monitoring:4317"
        tls:
          insecure: true
```

Additionally, make sure that the `batch` `processor` is active in your collector instance.

Make sure to create `kind: Service` instances for your `demo-app` instances to have them send data to each other.

## Images

Use the following images for the tasks:
- demo-app: ghcr.io/whizus-labs/workshop-otel-introduction/otel-demo-app:latest
- otel-collector: otel/opentelemetry-collector-k8s:0.106.1


## Verify your solution

After setting up the applications, collector and producing some data, the traces and spans are available in Jaeger to be viewed.

For this make sure to start a port-forward:
```bash
kubectl -n monitoring port-forward svc/jaeger-all-in-one-query 9091:16686
```
and access the Jaeger UI using localhost in your browser.

## Notes
* To solve this task you may use the files provided in `files` as reference/support
* Use `app-deployment-example.yaml` as basis for your `demo-app` deployments
* Make sure to set a specific`OTEL_SERVICE_NAME` so that you can find your services in the Jaeger instance

## The demo-app

The `otel-demo-app` is a minimalistic application with the purpose to produce traces and spans for demonstration purposes.

An example deployment with all available `ENV` vars is shown in the `files` folder.

To produce some trace data with the application make sure that you:
1. have two deployments of `demo-app`
2. each with their own `kind: Service`
3. and that `DEMO_APP_TARGET_HOST` and `DEMO_APP_TARGET_PORT` are configured to send to the other instance (not itself)
4. access the application using `kubectl -n {your-namespace} port-forward svc/{your-service-name} {host-port}:8080
5. and perform a request of `curl http://localhost:{host-port}/send-rocket`
