# EKS (Amazon Elastic Kubernetes Service) Templates

Production-ready templates for deploying and managing EKS clusters.

## Prerequisites

Before deploying an EKS cluster, you must connect your AWS account:

```bash
astroctl cloud aws connect \
  --account-id <your-aws-account-id> \
  --cluster-name <cluster-name> \
  --region <region>
```

This grants the platform secure access to your AWS account.

## Templates

| Template | Description | Use Case |
|----------|-------------|----------|
| `eks.yaml` | Basic EKS cluster with dynamic credentials | Standard production clusters |
| `eks-byovpc.yaml` | EKS with Bring Your Own VPC | Existing network infrastructure |
| `eks-update.yaml` | Cluster update configuration | Modify existing clusters |

## Quick Start

### 1. Basic EKS Cluster

```bash
# Edit eks.yaml with your account ID and settings
astroctl infra k8s apply -f eks.yaml
```

### 2. EKS with Existing VPC (BYOVPC)

Ensure your VPC has:
- Subnets in at least 2 availability zones
- Public subnets tagged with `kubernetes.io/role/elb=1`
- Private subnets tagged with `kubernetes.io/role/internal-elb=1`
- All subnets tagged with `kubernetes.io/cluster/<cluster-name>=shared`

```bash
astroctl infra k8s apply -f eks-byovpc.yaml
```

### 3. Update Existing Cluster

```bash
astroctl infra k8s update <cluster-name> -f eks-update.yaml
```

## Configuration Reference

### Required Fields

| Field | Description | Example |
|-------|-------------|---------|
| `accountId` | AWS account ID (12 digits) | `123456789012` |
| `credentials.type` | Authentication type | `dynamic` (recommended) |

### BYOVPC Fields

| Field | Description | Notes |
|-------|-------------|-------|
| `vpcId` | VPC ID | `vpc-0123456789abcdef0` |
| `vpcCIDR` | VPC CIDR block | `192.168.0.0/16` |
| `subnets` | Map of AZ to subnet list | Must include public and/or private subnets |

### Subnet Configuration

Each availability zone requires a list of subnets:

```yaml
subnets:
  us-east-1a:
    - type: public
      id: subnet-xxx
      cidr: "10.0.1.0/24"
    - type: private
      id: subnet-yyy
      cidr: "10.0.2.0/24"
```

## Features

- **Karpenter**: Automatically enabled for intelligent node autoscaling
- **Managed Node Groups**: AWS-managed node lifecycle
- **Pod-to-AWS Authentication**: Secure workload identity for pods

## Documentation

Full documentation: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/eks
