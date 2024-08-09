# Example 3

- [Example 3](#example-3)
  - [Structure](#structure)
  - [Description](#description)
    - [install OpenTelemetry Operator Helm Chart with ArgoCD](#install-opentelemetry-operator-helm-chart-with-argocd)
  - [Images](#images)
  - [Verify your solution](#verify-your-solution)
  - [Notes](#notes)
  - [The demo-app](#the-demo-app)
  - [Additional helpful links](#additional-helpful-links)


## Structure

This `example` has the following structure:
* `/files`: Files that can be used to help with solving this example
* `solution`: An example solution that can be used as reference if stuck and that may be discussed at the end of the example

## Description

The goal of this example is to deploy the `opentelemetry-operator` helm chart and maintain otel resources with the operator.

You want to create an instrumentation (see Instrumentation CR), a collector (see OpenTelemetryCollector CR) and a sidecar (OpenTelemetryCollector CR in sidecar-mode).
All collectors except the sidecar exports to the Jaeger instance.

Use the learned knowledge and the [OpenTelemetry Operator Documentation](https://github.com/open-telemetry/opentelemetry-operator#opentelemetry-auto-instrumentation-injection) to fulfill this example.

Following command will install the `opentelemetry-operator` helm chart with ArgoCD into your cluster:

### install OpenTelemetry Operator Helm Chart with ArgoCD
```
kubectl apply -f files/operator.yaml
```

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
* Make sure to set a specific `OTEL_SERVICE_NAME` so that you can find your services in the Jaeger instance
* The `OpenTelemetry Operator` requires the `cert-manager` to function correctly, this one was already installed during the `pre-setup`.
* The `OpenTelemetry Operator` is installed CLUSTER-WIDE!
  * If you share the cluster with other people, install the Operator as a group and not have everyone try on their own!

## The demo-app

The `otel-demo-app` is a minimalistic application with the purpose to produce traces and spans for demonstration purposes.

An example deployment with all available `ENV` vars is shown in the `files` folder.

To produce some trace data with the application make sure that you:
1. have two deployments of `demo-app`
2. each with their own `kind: Service`
3. and that `DEMO_APP_TARGET_HOST` and `DEMO_APP_TARGET_PORT` are configured to send to the other instance (not itself)
4. access the application using `kubectl -n {your-namespace} port-forward svc/{your-service-name} {host-port}:8080
5. and perform a request of `curl http://localhost:{host-port}/send-rocket`


## Additional helpful links
- [Injecting Auto-instrumentation OTEL](https://opentelemetry.io/docs/kubernetes/operator/automatic/)
- [Injecting Sidecar Container](https://github.com/open-telemetry/opentelemetry-operator?tab=readme-ov-file#sidecar-injection)
