# Google GKE

| File | Creates |
|---|---|
| [`new-vpc.yaml`](new-vpc.yaml) | A GKE Standard cluster in a new VPC with one node group |
| [`existing-vpc.yaml`](existing-vpc.yaml) | A GKE Standard cluster in a VPC network you already have |
| [`autopilot.yaml`](autopilot.yaml) | A GKE Autopilot cluster: Google runs the nodes |
| [`gpu-node-group.yaml`](gpu-node-group.yaml) | A GKE Standard cluster with a platform node group and an NVIDIA L4 GPU node group |
| [`update-node-groups.yaml`](update-node-groups.yaml) | A change to an existing cluster's node groups and tags |

## Before you apply

Enable the GKE API and connect your project once:

```bash
gcloud config get-value project   # your project ID
gcloud services enable container.googleapis.com --project <YOUR_GCP_PROJECT_ID>
astroctl cloud gcp connect --project-id <YOUR_GCP_PROJECT_ID> --cluster-name <YOUR_CLUSTER_NAME> --region <YOUR_REGION>
```

For `existing-vpc.yaml`, the subnet needs two secondary IP ranges, one for pods
and one for services. For `gpu-node-group.yaml`, check the region has GPUs you
have quota for: `astroctl cloud gcp accelerators list -r <YOUR_REGION>`.

## Apply

```bash
astroctl infra k8s apply -f new-vpc.yaml --dry-run
astroctl infra k8s apply -f new-vpc.yaml
astroctl infra k8s update <YOUR_CLUSTER_NAME> -f update-node-groups.yaml --dry-run
```

In the console: Infrastructure > Kubernetes, then **New cluster**.

Docs: [GKE](https://astropulse.io/docs/latest/platform/cluster-pipeline/features/provisioner/managed/gke)
