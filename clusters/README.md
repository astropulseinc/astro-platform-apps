# Clusters

Create a Kubernetes cluster with AstroPulse, or register one you already run.

In the console: Infrastructure > Kubernetes, then **New cluster**.

| Folder | Cluster |
|---|---|
| [`aws/eks/`](aws/eks/) | Amazon EKS: new VPC, existing VPC, GPU node group, node group update |
| [`aws/self-hosted/`](aws/self-hosted/) | Kubernetes on EC2 instances AstroPulse manages |
| [`gcp/gke/`](gcp/gke/) | Google GKE Standard and Autopilot: new VPC, existing VPC, GPU node group, node group update |
| [`gcp/self-hosted/`](gcp/self-hosted/) | Kubernetes on Compute Engine instances AstroPulse manages |
| [`azure/aks/`](azure/aks/) | Azure AKS in a new virtual network |
| [`register/`](register/) | Any existing cluster: EKS, GKE, AKS, on-premises or local |

## Apply

A new cluster needs your cloud account connected first; each folder's README
gives the command. Then:

```bash
astroctl infra k8s apply -f aws/eks/new-vpc.yaml --dry-run   # checks the account, quota and settings
astroctl infra k8s apply -f aws/eks/new-vpc.yaml
astroctl infra k8s progress stream <YOUR_CLUSTER_NAME>
astroctl infra k8s set-context <YOUR_CLUSTER_NAME>            # kubectl access
```

Docs: [Cluster management](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-mgmt)
