# Bring Your Own Kubernetes (BYOK) — Cluster Registration

Register any existing Kubernetes cluster with Astro Platform. No cloud credentials needed — just kubectl access.

> **Prefer a visual interface?** You can also register clusters through the [Web Console](https://astropulse.io/console).

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

## What Happens

A lightweight agent is deployed into your cluster. It connects outbound to the platform via an encrypted mTLS tunnel. No firewall changes required. Your cluster appears in the platform within seconds.

## Split-Team Workflow

For enterprise teams where platform and infrastructure teams are separate:

```bash
# Platform team: register only (no kubectl access needed)
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

Once registered, the cluster is a first-class citizen — same capabilities as platform-provisioned clusters:

```bash
# Deploy applications
astroctl app apply -f app.yaml

# Monitor cluster
astroctl infra k8s get my-cluster
```

### Optional: Connect Cloud Provider

Cloud provider access is separate and optional. Only needed for operations like version upgrades and scaling:

```bash
# AWS
astroctl cloud aws connect --account-id 123456789012 --cluster-name my-cluster --region us-west-2

# GCP
astroctl cloud gcp connect --project-id my-project --cluster-name my-cluster --region us-central1

# Azure
astroctl cloud azure connect --subscription-id <id> --resource-group <rg> --cluster-name my-cluster --region eastus
```

The platform generates a setup script you run in your cloud account. After that, credentials are auto-managed.

## Documentation

- [Cluster Registration Guide](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-registration)
- [Blog: Bring Your Own Kubernetes](https://astropulse.io/blog/bring-your-own-kubernetes)
