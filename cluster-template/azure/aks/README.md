# AKS (Azure Kubernetes Service) Templates

Production-ready templates for deploying and managing AKS clusters.

## Prerequisites

Before deploying an AKS cluster, connect your Azure subscription:

```bash
astroctl cloud azure connect \
  --subscription-id <your-subscription-id> \
  --resource-group <your-resource-group> \
  --cluster-name <cluster-name> \
  --region <region>
```

This grants the platform secure access to your Azure subscription.

## Template

| File | Description | Use Case |
|------|-------------|----------|
| [byoa.yaml](byoa.yaml) | AKS cluster with dynamic credentials | Standard production clusters |

## Quick Start

```bash
# 1. Get your Azure subscription ID
az account show --query id --output tsv

# 2. Connect your Azure subscription
astroctl cloud azure connect \
  --subscription-id <subscription-id> \
  --resource-group <resource-group> \
  --cluster-name my-aks-cluster \
  --region eastus

# 3. Edit byoa.yaml with your subscription ID and resource group

# 4. Deploy the cluster
astroctl infra k8s apply -f byoa.yaml

# 5. Monitor progress
astroctl infra k8s progress stream my-aks-cluster

# 6. Access the cluster
astroctl infra k8s set-context my-aks-cluster
```

## Configuration

| Field | Description | Example |
|-------|-------------|---------|
| `subscriptionId` | Azure Subscription ID (UUID) | `00000000-0000-0000-0000-000000000000` |
| `resourceGroup` | Azure Resource Group name | `rg-astro-production` |
| `credentials.type` | Authentication type | `dynamic` (recommended) |

## Documentation

Full documentation: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/aks
