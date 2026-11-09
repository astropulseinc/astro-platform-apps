# Self-hosted Kubernetes on Google Cloud

[`new-vpc.yaml`](new-vpc.yaml) creates a Kubernetes cluster on Compute Engine
instances that AstroPulse installs and manages: a control-plane node and a
group of Spot workers. Set `networkId` to use an existing VPC network instead.

## Before you apply

Set up the project once; the setup prints the bucket name for `bucketName`:

```bash
astroctl cloud gcp selfHosted setup --project-id <YOUR_GCP_PROJECT_ID> --region <YOUR_REGION> --cluster-name <YOUR_CLUSTER_NAME>
astroctl cloud gcp selfHosted status --cluster-name <YOUR_CLUSTER_NAME>   # check the setup
```

## Apply

```bash
astroctl infra k8s apply -f new-vpc.yaml --dry-run
astroctl infra k8s apply -f new-vpc.yaml
```

Docs: [Self-hosted on Google Cloud](https://astropulse.io/docs/latest/platform/cluster-pipeline/features/provisioner/self-hosted/gcp)
