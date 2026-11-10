# Azure AKS

[`new-vnet.yaml`](new-vnet.yaml) creates an AKS cluster in a new virtual
network, with one system node pool. The first node group is the system pool:
its name is lowercase letters and digits, at most 12 characters, and it runs
OnDemand.

## Before you apply

Connect your subscription, then run the script the command saves:

```bash
az account show --query id -o tsv   # your subscription ID
az group create --name <YOUR_RESOURCE_GROUP> --location <YOUR_REGION>   # if you need one
astroctl cloud azure connect --subscription-id <YOUR_SUBSCRIPTION_ID> --resource-group <YOUR_RESOURCE_GROUP> --cluster-name <YOUR_CLUSTER_NAME> --region <YOUR_REGION>
```

## Apply

```bash
astroctl infra k8s apply -f new-vnet.yaml --dry-run
astroctl infra k8s apply -f new-vnet.yaml
```

In the console: Infrastructure > Kubernetes, then **New cluster**.

Docs: [AKS](https://astropulse.io/docs/latest/platform/cluster-pipeline/features/provisioner/managed/aks)
