# AWS Cluster Templates

Templates for deploying Kubernetes clusters on Amazon Web Services.

## Available Options

| Directory | Type | Description |
|-----------|------|-------------|
| [eks/](./eks/) | **Managed (EKS)** | Amazon Elastic Kubernetes Service - fully managed K8s |
| [vanilla-k8s/](./vanilla-k8s/) | Self-Hosted | Platform-managed K8s on EC2 instances |

## Choosing Between EKS and Self-Hosted

### EKS (Recommended for most users)

- AWS manages the control plane
- Automatic security updates and patches
- Karpenter enabled for intelligent node autoscaling
- Best for: Production workloads, teams wanting managed infrastructure

### Self-Hosted (Vanilla K8s)

- Full control over control plane and nodes
- Platform manages cluster lifecycle automatically
- Best for: Specific compliance requirements, custom configurations

## Quick Start

### EKS Cluster

```bash
# 1. Connect your AWS account
astroctl cloud aws connect \
  --account-id 123456789012 \
  --cluster-name my-cluster \
  --region us-east-1

# 2. Deploy cluster
astroctl infra k8s apply -f eks/byoa.yaml
```

### Self-Hosted Cluster

```bash
# 1. Automated setup (handles all resource provisioning)
astroctl cloud aws selfHosted setup \
  --account-id 123456789012 \
  --region us-east-1 \
  --cluster-name my-cluster

# 2. Deploy cluster
astroctl infra k8s apply -f vanilla-k8s/aws.yaml
```

## Documentation

- EKS: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/eks
- Self-Hosted: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/self-hosted
