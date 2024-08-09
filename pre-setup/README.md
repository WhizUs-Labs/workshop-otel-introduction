# Pre-Setup

This repository contains the preperatory steps to get a K8S cluster ready for use with the examples.
Follow the instructions shown below.

## Table of Contents
- [Pre-Setup](#pre-setup)
  - [Table of Contents](#table-of-contents)
  - [Prepare Cluster](#prepare-cluster)
    - [Use of a pre-configured/pre-prepared K8S cluster](#use-of-a-pre-configuredpre-prepared-k8s-cluster)
    - [A Kind cluster](#a-kind-cluster)
    - [An Exoscale cluster](#an-exoscale-cluster)
  - [Install setup](#install-setup)
    - [Install Components](#install-components)
  - [Access services locally with port-forward](#access-services-locally-with-port-forward)
    - [Jaeger](#jaeger)
    - [ArgoCD](#argocd)
    - [Grafana](#grafana)
  - [Retrieve default secrets](#retrieve-default-secrets)

## Prepare Cluster

This repository describes three ways in which K8S cluster may be provided:
* Use of a pre-configured/pre-prepared K8S cluster
* A Kind cluster
* An Exoscale cluster

### Use of a pre-configured/pre-prepared K8S cluster

Configure the cluster in your kubeconfig and set the active context to the received context:
```bash
kubectl config use-context <context>
```

### A Kind cluster

Create the cluster with the following command:
```yaml
kind create cluster --name otel-demo
```
Kind will automatically update your kubeconfig und active context.

### An Exoscale cluster

Create a cluster based on the following command (all parameters are also available in the UI):
```bash
exo compute sks create ws-otel-intro-<a number> \
--cni calico \
--service-level starter \
--nodepool-size <size-of-your-pool> \
--zone at-vie-1 \
--nodepool-security-group <security-group>
```

The `<security-group>` security group must have the following rules defined:
| Type        | Protocol | Port: from/to | Source                 |
| ----------- | -------- | ------------- | ---------------------- |
| INGRESS     | TCP      | 443/443       | 0.0.0.0/0              |
| INGRESS     | TCP      | 30000/32767   | 0.0.0.0/0              |
| INGRESS     | UDP      | 30000/32767   | 0.0.0.0/0              |
| INGRESS     | UDP      | 4789/4789     | `<this security group>`|
| INGRESS     | TCP      | 10250/10250   | `<this security group>`|

## Install setup

### Install Components

Install ArgoCD:
```yaml
helm repo add argo https://argoproj.github.io/argo-helm
```
```yaml
helm install --create-namespace -n argocd argo-cd argo/argo-cd
```

Access ArgoCD to verify that it works:
```yaml
# get the passwort for argocd UI
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
```yaml
# create port-forward to open argocd UI
kubectl port-forward service/argo-cd-argocd-server -n argocd 8080:443
# go to the browser (localhost:8080) and log in
# username=admin
# password={see above}
```

Install the cert-manager:
```yaml
kubectl apply -f pre-setup/gitops/argocd/cert-manager
```

Install Jaeger Operator:
```yaml
kubectl apply -f pre-setup/gitops/argocd/jaeger-operator/jaeger-operator.yaml
```

Wait for Jaeger to become ready:
```yaml
kubectl get -n monitoring pod -l='app.kubernetes.io/name=jaeger-operator'
```

Install Jaeger all-in-one:
```yaml
kubectl apply -f pre-setup/gitops/argocd/jaeger-operator/jaeger-all-in-one.yaml
```
```yaml
# wait till pods are ready
kubectl get -n monitoring pod -l='app.kubernetes.io/name=jaeger-all-in-one'
```

## Access services locally with port-forward

### Jaeger
```yaml
kubectl port-forward svc/jaeger-all-in-one-query -n monitoring 9091:16686
```

### ArgoCD
```yaml
kubectl port-forward svc/argocd-helm-server -n argocd 9090:80
```

### Grafana
```yaml
kubectl port-forward svc/prom-grafana -n monitoring 9000:80
```


## Retrieve default secrets


```yaml
# get admin password for Grafana UI
kubectl -n monitoring get secret grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

# get admin password for ArgoCD UI
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
