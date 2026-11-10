# Amazon EKS

| File | Creates |
|---|---|
| [`new-vpc.yaml`](new-vpc.yaml) | An EKS cluster in a new VPC with one managed node group |
| [`existing-vpc.yaml`](existing-vpc.yaml) | An EKS cluster in a VPC you already have |
| [`gpu-node-group.yaml`](gpu-node-group.yaml) | An EKS cluster with a platform node group and an NVIDIA L4 GPU node group |
| [`update-node-groups.yaml`](update-node-groups.yaml) | A change to an existing cluster's node groups and tags |

## Before you apply

Connect your AWS account once per account and region. The command writes a
CloudFormation stack for you to deploy:

```bash
aws sts get-caller-identity --query Account --output text   # your account ID
astroctl cloud aws connect --account-id <YOUR_AWS_ACCOUNT_ID> --cluster-name <YOUR_CLUSTER_NAME> --region <YOUR_REGION>
```

For `existing-vpc.yaml`, list one public and one private subnet for every
availability zone in the region. `astroctl cloud aws generate-vpc` writes a
template for such a VPC.

For `gpu-node-group.yaml`, check the region has GPUs you have quota for:
`astroctl cloud aws accelerators list -r <YOUR_REGION>`. To launch GPU nodes only
while models need them, use [`../../../add-ons/karpenter-gpu-pool.yaml`](../../../add-ons/karpenter-gpu-pool.yaml) instead.

## Apply

```bash
astroctl infra k8s apply -f new-vpc.yaml --dry-run
astroctl infra k8s apply -f new-vpc.yaml
astroctl infra k8s update <YOUR_CLUSTER_NAME> -f update-node-groups.yaml --dry-run
```

In the console: Infrastructure > Kubernetes, then **New cluster**.

Docs: [EKS](https://astropulse.io/docs/latest/platform/cluster-pipeline/features/provisioner/managed/eks)
