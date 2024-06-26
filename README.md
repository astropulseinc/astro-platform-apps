
# Astro Platform Application Deployment Guide

## Download astroctl CLI
TBD

## Create Organization (Requires Admin Role)
To create an organization:
```
astroctl org create test-org
```

## Get the Organization ID (Requires Admin Role)
To list the organization and get the ID:
```
astroctl org list
```

## Generate API Key to Interact with Astro Platform
To generate an API key:
```
astroctl auth login --org-id <org_id>
```

## Available Clusters
To list available clusters for AWS:
```
astroctl clusters list -p aws
```
To list available clusters for GCP:
```
astroctl clusters list -p gcp
```

### Note
If there are no clusters available, ask the administrator to deploy a cluster (requires `platform-admin/admin` role).

## Check for Available Clusters
To check if any cluster is available for AWS:
```
astroctl clusters list -p aws
```
To check if any cluster is available for GCP:
```
astroctl clusters list -p gcp
```
Wait until clusters are available.

## Deploy Application Profiles
Note: Update the `app-profiles/selected-cluster.yaml` file to include the `clusterName` of your choice. To find the cluster names for your organization, run:
```
astroctl clusters list -p aws  # for AWS
astroctl clusters list -p gcp  # for GCP
```
Then apply the application profile:
```
astroctl app profile apply -f app-profiles/
```

## Deploy Hello-world Application
To deploy the hello-world application:
```
astroctl app apply -f apps/hello-world/demo.yaml
```

### Status Check
To check the status of the hello-world application:
```
astroctl app status hello-world
```

### Events
To view CI/CD events:
```
astroctl app events hello-world -ojson
```
To view Kubernetes events:
```
astroctl app events hello-world -k ojson
```

### Logs
To view logs:
```
astroctl app logs hello-world -ojson
```

### Access
To find the HTTP endpoint, run:
```
astroctl app get hello-world | grep endpoint
```

## Remote Kubernetes Access
1. Find the `clusterName`:
```
astroctl app status hello-world | grep clusterName
```
2. Set the context with the retrieved `clusterName`:
```
astroctl clusters set-context <clusterName>
```
This will generate the kubeconfig and change the context. Now, you can run standard `kubectl` commands.

To delete the kubeconfig for your cluster:
```
astroctl clusters delete-context <clusterName>
```

## Deploying Services
These examples provide popular cloud-native services that help manage the application lifecycle. Note that the platform does not create any external access configuration for applications when deploying through `helm` (helm repo), `repository` (helm from git code), or `yaml`.

### Cert-Manager
To deploy Cert-Manager:
```
astroctl app apply -f apps/cert-manager/cert-manager.yaml
```

### Grafana
To deploy Grafana:
```
astroctl app apply -f apps/grafana/grafana.yaml
```

### Prometheus
To deploy Prometheus:
```
astroctl app apply -f apps/prometheus/prometheus.yaml
```
Use `astroctl app logs/events` and remote access to debug if needed.

### Deploy CFK Operator (for Confluent Data Streaming)
To deploy the CFK Operator:
```
astroctl app apply -f apps/operators/cfk/cfk.yaml
```
Use `astroctl app logs/events` and remote access to debug if needed.

### Deploy Kafka Cluster (CFK)
To deploy a Kafka Cluster:
```
astroctl app apply -f apps/confluent-kafka/cfk.yaml
```
Note: The logs subcommand is not available from `astroctl` for this deployment.
