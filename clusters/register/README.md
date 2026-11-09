# Register an existing cluster

[`existing-cluster.yaml`](existing-cluster.yaml) registers a cluster you
already run: EKS, GKE, AKS, on-premises or local. astroctl installs a small
agent through your current kubectl context. The agent connects out to
AstroPulse over mutual TLS, so the cluster needs no inbound firewall rule and
AstroPulse holds no credentials for it.

## Apply

```bash
kubectl config current-context        # the cluster to register
astroctl infra k8s register -f existing-cluster.yaml
# or, without a file:
astroctl infra k8s register --cluster-name <YOUR_CLUSTER_NAME>
# or another kubeconfig and context:
astroctl infra k8s register --cluster-name <YOUR_CLUSTER_NAME> --kubeconfig ~/.kube/config --context <YOUR_CONTEXT>
astroctl infra k8s get
```

Upgrades, scaling and deletion of the cluster's cloud resources stay with you.
To let AstroPulse read the cluster's cloud metadata, connect the account:
`astroctl cloud aws|gcp|azure connect ... --cluster-name <YOUR_CLUSTER_NAME>`.

Docs: [Cluster registration](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-registration)
