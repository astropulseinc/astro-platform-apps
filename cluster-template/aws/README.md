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
- Integrated with AWS services (IAM, CloudWatch, etc.)
- Karpenter enabled for intelligent node autoscaling
- Best for: Production workloads, teams wanting managed infrastructure

### Self-Hosted (Vanilla K8s)

- Full control over control plane and nodes
- Platform manages cluster lifecycle via kOps
- Requires more operational expertise
- Best for: Specific compliance requirements, custom configurations

## Quick Start

### EKS Cluster

```bash
# 1. Connect your AWS account (deploys CloudFormation stack)
astroctl cloud aws connect \
  --account-id 123456789012 \
  --cluster-name my-cluster \
  --region us-east-1

# 2. Deploy cluster
astroctl infra k8s apply -f eks/byoa.yaml
```

### Self-Hosted Cluster

```bash
# 1. Create S3 bucket for state storage
aws s3 mb s3://my-cluster-state --region us-east-1

# 2. Deploy cluster
astroctl infra k8s apply -f vanilla-k8s/aws.yaml
```

## Documentation

- EKS: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/eks
- Self-Hosted: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/self-hosted
