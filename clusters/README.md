# Cluster Templates

Ready-to-use YAML templates for deploying Kubernetes clusters across AWS, GCP, and Azure. You can also register any existing cluster.

## Providers

| Provider | Directory | Templates | Description |
|----------|-----------|-----------|-------------|
| AWS | [aws/](./aws/) | EKS, Self-Hosted | Managed EKS or platform-managed clusters |
| GCP | [gcp/](./gcp/) | GKE, GKE Autopilot, Self-Hosted | Managed GKE (Standard or Autopilot) or platform-managed clusters |
| Azure | [azure/](./azure/) | AKS | Managed AKS clusters |
| Any | [byok/](./byok/) | Register | Register any existing Kubernetes cluster |

## Quick Start

```bash
# 1. Install CLI
curl -fsSL https://astropulse.io/install.sh | bash

# 2. Login
astroctl auth login

# 3. Connect your cloud account
astroctl cloud aws connect --account-id <id> --cluster-name my-cluster --region us-east-1

# 4. Deploy a cluster (update the YAML with your values first)
astroctl infra k8s apply -f aws/eks/byoa.yaml

# 5. Monitor progress
astroctl infra k8s progress stream my-cluster

# 6. Access the cluster
astroctl infra k8s set-context my-cluster
```

## All Templates

| File | Provider | Type | Description |
|------|----------|------|-------------|
| [aws/eks/byoa.yaml](aws/eks/byoa.yaml) | AWS | EKS | Standard EKS cluster with dynamic credentials |
| [aws/eks/byo-vpc.yaml](aws/eks/byo-vpc.yaml) | AWS | EKS | EKS with existing VPC |
| [aws/eks/eks-update.yaml](aws/eks/eks-update.yaml) | AWS | EKS | Update an existing EKS cluster |
| [aws/vanilla-k8s/aws.yaml](aws/vanilla-k8s/aws.yaml) | AWS | Self-Hosted | Platform-managed K8s on EC2 |
| [gcp/gke/cluster.yaml](gcp/gke/cluster.yaml) | GCP | GKE | Standard GKE cluster with dynamic credentials |
| [gcp/gke/autopilot.yaml](gcp/gke/autopilot.yaml) | GCP | GKE Autopilot | Fully managed (no node management) |
| [gcp/gke/byovpc.yaml](gcp/gke/byovpc.yaml) | GCP | GKE | GKE with existing VPC |
| [gcp/gke/update.yaml](gcp/gke/update.yaml) | GCP | GKE | Update an existing GKE cluster |
| [gcp/vanilla-k8s/gcp.yaml](gcp/vanilla-k8s/gcp.yaml) | GCP | Self-Hosted | Platform-managed K8s on Compute Engine |
| [azure/aks/byoa.yaml](azure/aks/byoa.yaml) | Azure | AKS | Standard AKS cluster with dynamic credentials |
| [byok/register.yaml](byok/register.yaml) | Any | Registration | Register any existing cluster |

## Documentation

- [Cluster Management](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-mgmt)
- [Get Started](https://astropulse.io/get-started)
