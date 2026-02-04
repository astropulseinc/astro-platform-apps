# GCP Cluster Templates

Templates for deploying Kubernetes clusters on Google Cloud Platform.

## Available Options

| Directory | Type | Description |
|-----------|------|-------------|
| [gke/](./gke/) | **Managed (GKE)** | Google Kubernetes Engine - fully managed K8s |
| [vanilla-k8s/](./vanilla-k8s/) | Self-Hosted | Platform-managed K8s on GCP Compute Engine |

## Choosing Between GKE and Self-Hosted

### GKE (Recommended for most users)

- Google manages the control plane
- Automatic security updates and patches
- Integrated with GCP services (IAM, Cloud Logging, etc.)
- Options: Standard mode or Autopilot (fully managed)
- Best for: Production workloads, teams wanting managed infrastructure

### Self-Hosted (Vanilla K8s)

- Full control over control plane and nodes
- Platform manages cluster lifecycle via kOps
- Requires more operational expertise
- Best for: Specific compliance requirements, custom configurations

## Quick Start

### GKE Cluster

```bash
# 1. Connect your GCP account (sets up Workload Identity Federation)
astroctl cloud gcp connect \
  --project-id my-project-123 \
  --cluster-name my-cluster \
  --region us-central1

# 2. Deploy cluster
astroctl infra k8s apply -f gke/cluster.yaml
```

### Self-Hosted Cluster

```bash
# 1. Create GCS bucket for state storage
gsutil mb -l us-central1 gs://my-cluster-state

# 2. Deploy cluster
astroctl infra k8s apply -f vanilla-k8s/gcp.yaml
```

## Documentation

- GKE: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/gke
- Self-Hosted: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/self-hosted
