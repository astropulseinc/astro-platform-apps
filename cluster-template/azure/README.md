# Azure Cluster Templates

Templates for deploying Kubernetes clusters on Azure using Astro Platform.

## AKS (Azure Kubernetes Service)

| Template | Description |
|----------|-------------|
| [aks/byoa.yaml](aks/byoa.yaml) | AKS cluster with dynamic credentials via federated identity (recommended) |

## Prerequisites

1. Install [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/)
2. Install [Astro Platform CLI](https://astropulse.io/docs/latest/cli/astroctl)
3. Run `astroctl cloud azure connect` to setup federated identity

## Quick Start

```bash
# Setup Azure federated identity
astroctl cloud azure connect \
  --subscription-id <subscription-id> \
  --cluster-name my-aks-cluster \
  --region eastus

# Deploy the cluster
astroctl infra k8s apply -f aks/byoa.yaml

# Monitor progress
astroctl infra k8s progress stream my-aks-cluster

# Get kubeconfig
astroctl infra k8s set-context my-aks-cluster
```

## Web Console

For the easiest experience, use the [Web Console](https://astropulse.io/console) to create and manage AKS clusters with a visual interface.
