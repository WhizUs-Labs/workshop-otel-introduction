# Open Telemety Introduction Workshop

This repository contains the tasks and solutions for the practical part of the `Open Telemetry Introduction` workshop.

- [Open Telemety Introduction Workshop](#open-telemety-introduction-workshop)
  - [Using this repository](#using-this-repository)
  - [Notes regarding the examples](#notes-regarding-the-examples)
  - [Install required CLIs](#install-required-clis)
    - [Helm](#helm)
    - [Kubectl](#kubectl)


## Using this repository

1. Before doing anything else, make sure that your clusters have all pre-requisites installed, to do this, follow the instruction in `pre-setup`
2. Now that your cluster is ready, the examples in `example-1`, `example-2` and `example-3` can be tackled.

## Notes regarding the examples

* All examples are making you send data to an internally deployed Jaeger instance, which means, that if you did not follow the `pre-setup` instructions, you will not be able to do this!
* The `OpenTelemetry Operator` in `example-3` requires the `cert-manager` to function correctly, this one is already installed during the `pre-setup`.
* The `OpenTelemetry Operator` is installed CLUSTER-WIDE!
  * If you share the cluster with other people, install the Operator as a group and not have everyone try on their own!

## Install required CLIs

The examples in this repository require the following CLIs to be installed on your device.
Each of the following sub-sections represents a required CLI.

### Helm

Follow the direction in the [Helm install docs](https://helm.sh/docs/intro/install/).

### Kubectl

Follow the direction in the [kubectl install docs](https://kubernetes.io/docs/tasks/tools/#kubectl).