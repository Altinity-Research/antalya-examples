# Project Antalya Helm Chart

This is an opinionated Helm chart used to deploy Project Antalya ClickHouse® components in an EKS cluster.

## Prerequisites

* Helm 3.7.0 or later
* EKS cluster

## Installation

To install the chart, run:

```bash
helm upgrade --install --create-namespace --namespace antalya antalya-swarm-demo .
```

**Warning:** This chart does not set the `Retain` PVC policy. If you uninstall the chart or remove the helm release or ClickHouseInstallation resources from the cluster, all data on the coordinator (vector) cluster will be lost.

## Assumptions

This chart assumes that you used the terrafrom configuration in this repo in order to provision your cluster. If you used another method, or if you are running outside of EKS, you will need to update the tolerations and node selectors in the values.yaml file to match your cluster.
