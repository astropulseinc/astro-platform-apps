# Add-ons

Managed add-ons install the Kubernetes components most clusters need. You set
a few typed options; AstroPulse picks the chart and version, installs it into
its own namespace, upgrades it and reports its health. Add-on files never hold
cloud credentials: an add-on that calls a cloud API runs as a workload identity
you name, or reads a Secret you create.

In the console: Infrastructure > Kubernetes, open the cluster, then its
**Add-ons** tab.

| File | Add-on | What it does |
|---|---|---|
| [`nginx-ingress.yaml`](nginx-ingress.yaml) | Ingress | Routes outside traffic to image applications |
| [`cert-manager.yaml`](cert-manager.yaml) | Certificates | Let's Encrypt or self-signed certificates over HTTP-01 |
| [`cert-manager-letsencrypt-dns.yaml`](cert-manager-letsencrypt-dns.yaml) | Certificates | Let's Encrypt over DNS-01, for the gateway and wildcard certificates |
| [`external-dns.yaml`](external-dns.yaml) | DNS Automation | Publishes DNS records for application and model hostnames |
| [`karpenter.yaml`](karpenter.yaml) | Node Autoscaling | Adds and removes nodes as demand changes (AWS) |
| [`karpenter-gpu-pool.yaml`](karpenter-gpu-pool.yaml) | Node Autoscaling | The same, with a GPU node pool capped by `gpuLimit` |
| [`workload-autoscaling.yaml`](workload-autoscaling.yaml) | Workload Autoscaling | Scales applications, and models on waiting requests |
| [`cost-visibility.yaml`](cost-visibility.yaml) | Cost Visibility | Usage and cost per application and model |
| [`vulnerability-scanning.yaml`](vulnerability-scanning.yaml) | Vulnerability Scanning | Known vulnerabilities in running images |
| [`model-serving.yaml`](model-serving.yaml) | Model Serving | Serves model applications behind an OpenAI-compatible API |
| [`gateway.yaml`](gateway.yaml) | Gateway | HTTPS for model endpoints, with access policies enforced |
| [`workload-queueing.yaml`](workload-queueing.yaml) | Workload Queueing | GPU quotas shared between teams and models |
| [`accelerator-metrics.yaml`](accelerator-metrics.yaml) | Accelerator Metrics | GPU utilization, memory and power per application |

Some add-ons need others first: model-serving and gateway need cert-manager,
and workload-autoscaling reads metrics from cost-visibility's Prometheus.

## Apply

Replace `<YOUR_CLUSTER_NAME>` and the other placeholders, then:

```bash
astroctl infra k8s addons apply -f cert-manager.yaml --dry-run   # checks the settings against the cluster
astroctl infra k8s addons apply -f cert-manager.yaml
astroctl infra k8s addons get <YOUR_CLUSTER_NAME>
astroctl infra k8s addons status <YOUR_CLUSTER_NAME> cert-manager
```

Edit a file and apply it again to change the add-on. Each file holds one
add-on. To see every setting an add-on accepts on your cluster:

```bash
astroctl infra k8s addons template <YOUR_CLUSTER_NAME> gateway
```

Docs: [Cluster add-ons](https://astropulse.io/docs/latest/platform/cluster-pipeline/add-ons)
