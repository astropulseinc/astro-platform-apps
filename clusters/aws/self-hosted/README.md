# AWS Self-Hosted Kubernetes

Platform-managed Kubernetes on AWS EC2 instances. The platform handles cluster lifecycle, upgrades, and scaling automatically.

## Template

| File | Description |
|------|-------------|
| [aws.yaml](aws.yaml) | Self-hosted K8s cluster on AWS |

## Prerequisites

1. Install [astroctl CLI](https://astropulse.io/get-started): `curl -fsSL https://astropulse.io/install.sh | bash`
2. [AWS CLI](https://docs.aws.amazon.com/cli/) for initial setup

## Quick Start

```bash
# 1. Automated setup (handles all resource provisioning)
astroctl cloud aws selfHosted setup \
  --account-id <your-aws-account-id> \
  --region us-west-2 \
  --cluster-name my-cluster

# 2. Edit aws.yaml with the values from the setup output
#    - accountId: your AWS account ID
#    - bucketName: from the setup output

# 3. Deploy the cluster
astroctl infra k8s apply -f aws.yaml

# 4. Monitor progress
astroctl infra k8s progress stream my-cluster

# 5. Access the cluster
astroctl infra k8s set-context my-cluster
```

## Configuration

| Field | Description | Source |
|-------|-------------|--------|
| `accountId` | AWS account ID (12 digits) | `aws sts get-caller-identity` |
| `bucketName` | State storage bucket | From setup output |
| `credentials.type` | Always `vault` | Managed by platform |

## Documentation

Full documentation: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/self-hosted
