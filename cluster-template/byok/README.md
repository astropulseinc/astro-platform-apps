# Bring Your Own Kubernetes (BYOK) — Cluster Registration

Register any existing Kubernetes cluster with Astro Platform. No provisioning needed — just connect your cluster.

## Supported Clusters

Works with any Kubernetes cluster:
- **Managed**: EKS, GKE, AKS
- **Self-hosted**: kOps, Rancher, Kubespray
- **On-premises**: bare metal, VMware
- **Local**: Kind, Minikube, Docker Desktop

## Quick Start

```bash
# Register a cluster (auto-detects type and region)
astroctl infra k8s register --cluster-name my-cluster

# Or use a YAML file
astroctl infra k8s register -f register.yaml
```

## Split-Team Workflow

For enterprise teams where platform and infrastructure teams are separate:

```bash
# Platform team: register (no kubectl access needed)
astroctl infra k8s register --cluster-name prod-cluster --no-install

# Infrastructure team: install agent (has kubectl access)
astroctl infra k8s register agent --cluster-name prod-cluster
```

## Dry Run

Review the agent manifest before deploying:

```bash
astroctl infra k8s register --cluster-name my-cluster --dry-run
```

## After Registration

Once registered, the cluster is a first-class citizen in the platform:

```bash
# Deploy applications
astroctl app apply -f app.yaml

# Monitor cluster
astroctl infra k8s get my-cluster

# Connect cloud provider (optional, for upgrades/scaling)
astroctl cloud aws connect --cluster-name my-cluster --account-id 123456789012 --region us-west-2
```

## Web Console

You can also register clusters through the [Web Console](https://astropulse.io/console) UI.

## Documentation

- [Cluster Registration Guide](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-registration)
- [Blog: Bring Your Own Kubernetes](https://astropulse.io/blog/bring-your-own-kubernetes)
