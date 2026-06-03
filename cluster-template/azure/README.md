# Azure Cluster Templates

Templates for deploying Kubernetes clusters on Azure using Astro Platform.

> **Prefer a visual interface?** Use the [Web Console](https://astropulse.io/console) to create and manage AKS clusters — no CLI or YAML needed.

## AKS (Azure Kubernetes Service)

| Template | Description |
|----------|-------------|
| [aks/byoa.yaml](aks/byoa.yaml) | AKS cluster with dynamic credentials via federated identity (recommended) |

## Prerequisites

1. Install [Astro Platform CLI](https://astropulse.io/get-started): `curl -fsSL https://astropulse.io/install.sh | bash`
2. [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/) for kubeconfig access

## Quick Start

```bash
# 1. Connect Azure subscription — generates a setup script you run in Azure
astroctl cloud azure connect \
  --subscription-id <subscription-id> \
  --resource-group <resource-group> \
  --cluster-name my-aks-cluster \
  --region eastus

# 2. Run the generated setup script in your Azure subscription
#    (the platform handles credential management from this point)

# 3. Deploy the cluster
astroctl infra k8s apply -f aks/byoa.yaml

# 4. Monitor progress
astroctl infra k8s progress stream my-aks-cluster

# 5. Get kubeconfig
astroctl infra k8s set-context my-aks-cluster
```

## How It Works

1. `cloud azure connect` generates a setup script for your Azure subscription
2. You run the setup script — this creates the federated identity trust
3. The platform auto-manages credentials from that point (dynamic, keyless)
4. `infra k8s apply` provisions the AKS cluster using the trust relationship

## Documentation

Full documentation: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/aks
