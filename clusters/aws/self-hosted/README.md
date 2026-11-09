# Self-hosted Kubernetes on AWS

[`new-vpc.yaml`](new-vpc.yaml) creates a Kubernetes cluster on EC2 instances
that AstroPulse installs and manages: a control-plane node and a group of
workers. Set `networkId` to use an existing VPC instead.

## Before you apply

Set up the account once; the setup prints the bucket name for `bucketName`:

```bash
astroctl cloud aws selfHosted setup --account-id <YOUR_AWS_ACCOUNT_ID> --region <YOUR_REGION> --cluster-name <YOUR_CLUSTER_NAME>
astroctl cloud aws selfHosted status --cluster-name <YOUR_CLUSTER_NAME>   # check the setup
```

## Apply

```bash
astroctl infra k8s apply -f new-vpc.yaml --dry-run
astroctl infra k8s apply -f new-vpc.yaml
```

Docs: [Self-hosted on AWS](https://astropulse.io/docs/latest/platform/cluster-pipeline/features/provisioner/self-hosted/aws)
