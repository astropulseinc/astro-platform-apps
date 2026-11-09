# GCP Self-Hosted Kubernetes

Platform-managed Kubernetes on GCP Compute Engine instances. The platform handles cluster lifecycle, upgrades, and scaling automatically.

## Template

| File | Description |
|------|-------------|
| [gcp.yaml](gcp.yaml) | Self-hosted K8s cluster on GCP |

## Prerequisites

1. Install [astroctl CLI](https://astropulse.io/get-started): `curl -fsSL https://astropulse.io/install.sh | bash`
2. [Google Cloud CLI](https://cloud.google.com/sdk/docs) for initial setup

## Quick Start

```bash
# 1. Automated setup (handles all resource provisioning)
astroctl cloud gcp selfHosted setup \
  --project-id <your-gcp-project-id> \
  --region us-central1 \
  --cluster-name my-cluster

# 2. Edit gcp.yaml with the values from the setup output
#    - accountId: your GCP project ID
#    - bucketName: from the setup output

# 3. Deploy the cluster
astroctl infra k8s apply -f gcp.yaml

# 4. Monitor progress
astroctl infra k8s progress stream my-cluster

# 5. Access the cluster
astroctl infra k8s set-context my-cluster
```

## Configuration

| Field | Description | Source |
|-------|-------------|--------|
| `accountId` | GCP project ID | `gcloud config list --format="value(core.project)"` |
| `bucketName` | State storage bucket | From setup output |
| `credentials.type` | Always `vault` | Managed by platform |

## Documentation

Full documentation: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/self-hosted
