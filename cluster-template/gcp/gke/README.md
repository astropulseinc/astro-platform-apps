# GKE (Google Kubernetes Engine) Templates

Production-ready templates for deploying and managing GKE clusters.

## Prerequisites

Before deploying a GKE cluster, you must connect your GCP account:

```bash
astroctl cloud gcp connect \
  --project-id <your-gcp-project-id> \
  --cluster-name <cluster-name> \
  --region <region>
```

This grants the platform secure access to your GCP project.

## Templates

| Template | Description | Use Case |
|----------|-------------|----------|
| `cluster.yaml` | Basic GKE cluster with dynamic credentials | Standard production clusters |
| `byovpc.yaml` | GKE with Bring Your Own VPC | Existing network infrastructure |
| `autopilot.yaml` | GKE Autopilot (fully managed) | Hands-off node management |
| `update.yaml` | Cluster update configuration | Modify existing clusters |

## Quick Start

### 1. Basic GKE Cluster

```bash
# Edit cluster.yaml with your project ID and settings
astroctl infra k8s apply -f cluster.yaml
```

### 2. GKE with Existing VPC (BYOVPC)

First, ensure your VPC has the required subnet configuration:

```bash
# Create subnet with secondary ranges (run in your GCP project)
gcloud compute networks subnets create gke-subnet \
  --network=my-vpc \
  --region=us-central1 \
  --range=10.0.0.0/20 \
  --secondary-range=pods=10.4.0.0/14 \
  --secondary-range=services=10.0.16.0/20 \
  --enable-private-ip-google-access \
  --project=my-gcp-project-123
```

Then deploy:

```bash
astroctl infra k8s apply -f byovpc.yaml
```

### 3. GKE Autopilot

```bash
astroctl infra k8s apply -f autopilot.yaml
```

### 4. Update Existing Cluster

```bash
astroctl infra k8s update <cluster-name> -f update.yaml
```

## Configuration Reference

### Required Fields

| Field | Description | Example |
|-------|-------------|---------|
| `projectId` | GCP project ID (6-30 chars, lowercase) | `my-project-123` |
| `credentials.type` | Authentication type | `dynamic` (recommended) |

### BYOVPC Fields

| Field | Description | Format |
|-------|-------------|--------|
| `networkId` | VPC network name | 1-63 lowercase chars, starts with letter |
| `subnet.name` | Regional subnet name | 1-63 lowercase chars, starts with letter |
| `subnet.podsRange` | Secondary range name for pods | 1-63 lowercase chars |
| `subnet.servicesRange` | Secondary range name for services | 1-63 lowercase chars |

### Network Configuration

| Field | Description | Default |
|-------|-------------|---------|
| `masterIPv4CidrBlock` | Control plane CIDR | `172.16.0.0/28` |
| `authorizedNetworks` | API access CIDRs (max 50) | RFC 1918 ranges |

### CIDR Requirements

- **masterIPv4CidrBlock**: Must be exactly `/28`, RFC 1918 range
- **authorizedNetworks**: Valid CIDR format, max 50 entries
- **networkCIDR**: RFC 1918 private range (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)

## Documentation

Full documentation: https://astropulse.io/docs/latest/platform/cluster-pipeline/provisioner/managed/gke
