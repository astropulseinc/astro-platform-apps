
# Astro Platform Application Deployment Guide

Astro Platform simplifies Kubernetes cluster management and application deployment across AWS, GCP, and Azure.

## Getting Started

> **Prefer a visual interface?** Use the [Astro Console](https://astropulse.io/console) for an intuitive UI experience to manage clusters and applications. The console provides the same capabilities with a user-friendly dashboard, real-time monitoring, and guided workflows.
>
> This guide is for developers who prefer the CLI approach.

## Prerequisites

Before you begin, ensure you have:

- **Cloud Account**: AWS, GCP, or Azure account with appropriate permissions
- **astroctl CLI**: Install with `curl -fsSL https://astropulse.io/install.sh | bash`
- **kubectl**: For direct Kubernetes cluster access ([install guide](https://kubernetes.io/docs/tasks/tools/))

## Quick Start

**Option A — Provision a new cluster (AWS EKS)**
```bash
# 1. Install CLI + login
curl -fsSL https://astropulse.io/install.sh | bash
astroctl auth login

# 2. Link your AWS account (one-time per cluster)
astroctl cloud aws connect --account-id <YOUR_AWS_ACCOUNT_ID> --cluster-name my-cluster --region us-east-1

# 3. Deploy cluster (update accountId and region in the yaml first)
astroctl infra k8s apply -f cluster-template/aws/eks/byoa.yaml

# 4. Access and deploy
astroctl infra k8s set-context my-cluster
astroctl app apply -f apps/hello-world/demo.yaml
astroctl app status hello-world
```

**Option B — Register an existing cluster (any provider)**
```bash
# 1. Install CLI + login
curl -fsSL https://astropulse.io/install.sh | bash
astroctl auth login

# 2. Register (deploys a lightweight agent via your current kubectl context)
astroctl infra k8s register --cluster-name my-cluster

# 3. (Optional) Link cloud provider for upgrade/scale/delete operations
#    AWS:   astroctl cloud aws connect --account-id <ID> --cluster-name my-cluster --region <region>
#    GCP:   astroctl cloud gcp connect --project-id <PROJECT> --cluster-name my-cluster --region <region>
#    Azure: astroctl cloud azure connect --subscription-id <ID> --resource-group <RG> --cluster-name my-cluster --region <region>

# 4. Deploy
astroctl app apply -f apps/hello-world/demo.yaml
astroctl app status hello-world
```

## Cluster Templates

| Provider | Template | Description |
|----------|----------|-------------|
| AWS | [cluster-template/aws/eks/byoa.yaml](cluster-template/aws/eks/byoa.yaml) | EKS with dynamic credentials |
| AWS | [cluster-template/aws/eks/byo-vpc.yaml](cluster-template/aws/eks/byo-vpc.yaml) | EKS with existing VPC |
| Azure | [cluster-template/azure/aks/byoa.yaml](cluster-template/azure/aks/byoa.yaml) | AKS with dynamic credentials |
| GCP | [cluster-template/gcp/gke/cluster.yaml](cluster-template/gcp/gke/cluster.yaml) | GKE with dynamic credentials |
| GCP | [cluster-template/gcp/gke/autopilot.yaml](cluster-template/gcp/gke/autopilot.yaml) | GKE Autopilot |
| BYOK | [cluster-template/byok/register.yaml](cluster-template/byok/register.yaml) | Register any existing cluster |

## Table of Contents
1. [Download astroctl CLI](#download-astroctl-cli)
2. [Generate API Key](#generate-api-key-to-interact-with-astro-platform)
3. [Deploy an EKS Cluster](#deploy-an-eks-cluster)
4. [Register an Existing Cluster (BYOK)](#register-an-existing-cluster-byok)
5. [Manage Clusters](#available-clusters-and-their-state)
6. [Deploy Application Profiles](#deploy-application-profiles)
7. [Managed Cluster Add-ons](#managed-cluster-add-ons) — recommended: one-command ingress / TLS / DNS / autoscaling
8. [External Access Setup — manual alternative (Optional)](#bring-your-own-external-access-optional)
   - [Kubernetes NGINX Ingress Controller](#kubernetes-nginx-ingress-controller)
   - [TLS Certificate](#tls-certificate)
   - [AWS Specific Setup](#aws)
   - [Google Cloud DNS Setup](#google-cloud-dns)
   - [External DNS](#external-dns)
9. [Deploy Hello-world Application](#deploy-hello-world-application)
10. [Remote Kubernetes Access](#remote-kubernetes-access)
11. [Deploying Services](#deploying-services)
   - [Cert-Manager](#cert-manager)
   - [Grafana](#grafana)
   - [Prometheus](#prometheus-operator)
   - [Clickhouse](#clickhouse)
   - [External Secrets](#external-secrets)
   - [Confluent Operator (CFK)](#confluent-operator-cfk)
   - [Strimzi Kafka](#strimzi-kafka)
   - [Otel Collector](#otel-collector)
   - [Kube State Metrics](#kube-state-metrics)
12. [Kubernetes Nginx Ingress Controller](#kubernetes-nginx-ingress-controller-1)
13. [Application Debugging](#debugging)
14. [Reference](#reference)

## Download astroctl CLI
```
curl -fsSL https://astropulse.io/install.sh | bash
```

## Authenticate
Log in with your organization credentials:
```
astroctl auth login
```

## Deploy an EKS Cluster

First, link your AWS account, then deploy the cluster.

```bash
# Step 1 — link your AWS account (one-time per cluster)
# Find your account ID: aws sts get-caller-identity --query Account --output text
astroctl cloud aws connect --account-id <YOUR_AWS_ACCOUNT_ID> --cluster-name my-cluster --region us-east-1

# Step 2 — deploy (update accountId and region in the YAML first)
astroctl infra k8s apply -f cluster-template/aws/eks/byoa.yaml
```

For GKE or AKS clusters, use the equivalent connect command:
```bash
# GKE: find project ID with: gcloud config get-value project
astroctl cloud gcp connect --project-id <GCP_PROJECT_ID> --cluster-name my-cluster --region us-central1
astroctl infra k8s apply -f cluster-template/gcp/gke/cluster.yaml

# AKS: find subscription ID with: az account show --query id --output tsv
astroctl cloud azure connect --subscription-id <SUBSCRIPTION_ID> --resource-group <RESOURCE_GROUP> --cluster-name my-cluster --region eastus
astroctl infra k8s apply -f cluster-template/azure/aks/byoa.yaml
```

For different cluster types, see the [documentation](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-mgmt).

## Register an Existing Cluster (BYOK)

Already have a Kubernetes cluster on AWS, GCP, Azure, or on-premises? Register it without reprovisioning.

```bash
# Register using your current kubectl context
astroctl infra k8s register --cluster-name my-cluster

# Or using a specific kubeconfig/context
astroctl infra k8s register --cluster-name my-cluster --kubeconfig ~/.kube/config --context my-context
```

A lightweight agent is deployed to your cluster that creates a secure outbound tunnel to the platform. No firewall rules required.

### Link Your Cloud Provider (Optional)

Cloud provider access is **optional** and only needed for platform-managed operations like version upgrades, node scaling, and deletion of cloud resources. Connect it any time after registration:

```bash
# AWS — find your account ID: aws sts get-caller-identity --query Account --output text
astroctl cloud aws connect --account-id <AWS_ACCOUNT_ID> --cluster-name my-cluster --region <REGION>

# GCP — find your project ID: gcloud config get-value project
astroctl cloud gcp connect --project-id <GCP_PROJECT_ID> --cluster-name my-cluster --region <REGION>

# Azure — find your subscription ID: az account show --query id --output tsv
astroctl cloud azure connect --subscription-id <SUBSCRIPTION_ID> --resource-group <RESOURCE_GROUP> --cluster-name my-cluster --region <REGION>
```

Each `cloud connect` command generates a setup script and either opens it in your cloud's shell (GCP Cloud Shell) or saves it locally (AWS CloudFormation, Azure CLI script). Run it once — the platform then manages credentials automatically.

See [cluster-template/byok/](cluster-template/byok/) for full details and YAML examples.

## Available Clusters and their State
```
astroctl infra k8s get
```

## Access an existing cluster
```
astroctl infra k8s set-context test-dev
```

## Deploy Application Profiles

> **Note:** Update the `app-profiles/` file to include the `clusterName` of your choice.


To find the cluster names for your organization, run:

```
astroctl infra k8s get
```

Then apply the application profile:
```
astroctl app profile apply -f app-profiles/
```

## Managed Cluster Add-ons

Install the Kubernetes ecosystem apps most clusters need — **ingress, TLS certificates, DNS automation, and node autoscaling** — with one `astroctl` command on any AstroPulse cluster (provisioned or registered). You declare **intent** (which add-on + a few typed options); AstroPulse owns the chart, version, deployment lifecycle, and health. You never handle Helm values, and add-on configuration never contains cloud credentials.

> This is the managed replacement for the manual [External Access Setup](#bring-your-own-external-access-optional) below. Prefer a UI? Manage the same add-ons from the [Astro Console](https://astropulse.io/console). Full details: [`cluster-addons/README.md`](cluster-addons/README.md) · [Cluster Add-ons docs](https://astropulse.io/docs/latest/platform/cluster-pipeline/add-ons).

Ready-to-edit example manifests live in [`cluster-addons/`](cluster-addons/):

| File | Add-on | Namespace |
|---|---|---|
| [`cluster-addons/nginx-ingress.yaml`](cluster-addons/nginx-ingress.yaml) | NGINX Ingress | `ingress-nginx` |
| [`cluster-addons/cert-manager.yaml`](cluster-addons/cert-manager.yaml) | cert-manager | `cert-manager` |
| [`cluster-addons/external-dns.yaml`](cluster-addons/external-dns.yaml) | ExternalDNS | `external-dns` |
| [`cluster-addons/karpenter.yaml`](cluster-addons/karpenter.yaml) | Karpenter (AWS) | `kube-system` |

```bash
# Install or update — declarative + idempotent (edit the file and re-apply to change):
astroctl infra k8s addons apply -f cluster-addons/nginx-ingress.yaml

# Watch it converge, then inspect / debug:
astroctl infra k8s addons get    <cluster>                 # all installed add-ons
astroctl infra k8s addons status <cluster> nginx-ingress
astroctl infra k8s addons logs   <cluster> nginx-ingress   # add-on workload logs
astroctl infra k8s addons events <cluster> nginx-ingress

# Upgrade / roll back / uninstall:
astroctl infra k8s addons versions <cluster> cert-manager
astroctl infra k8s addons rollback <cluster> cert-manager
astroctl infra k8s addons delete   <cluster> cert-manager
```

**Automatic HTTPS:** install NGINX + cert-manager (with a Let's Encrypt ClusterIssuer) + ExternalDNS together and every internet-facing app gets a DNS record and a trusted TLS certificate automatically.

**Cloud identity (ExternalDNS & Karpenter):** these reach cloud APIs with the **cluster's own identity** — IRSA on AWS, Workload Identity on GCP/Azure — set up once with `astroctl cloud <aws|gcp> connect --cluster-name <name>`. Credentials never go in the manifest. Per-provider setup is in the [add-on docs](https://astropulse.io/docs/latest/platform/cluster-pipeline/add-ons#externaldns-dns-automation).

## Bring your own External Access (Optional)

> **Recommended:** use [Managed Cluster Add-ons](#managed-cluster-add-ons) above — NGINX, cert-manager, and ExternalDNS install and stay healthy with one `astroctl` command each. This section is the manual alternative for teams who want to own and manage these components themselves.

`Note: Skip this section if using an Astro-managed cluster, as external access is pre-configured for applications using source type image`

This section is optional and covers setting up external access to your applications.

If you want to make your applications accessible from outside the cluster, follow these steps:

1. Set up the `Kubernetes NGINX Ingress Controller`: This acts as a reverse proxy, routing external traffic to your services.
2. Configure `TLS certificates using cert-manager`: This ensures secure, encrypted connections to your applications.
3. `External DNS:` This will automatically create DNS records for your applications.

By completing this one-time setup, you'll enable external access for all applications in your cluster. This approach uses industry-standard tools to manage external access and secure it with TLS certificates.

Note: If you don't need external access, you can skip this section.

### Kubernetes Nginx Ingress Controller


Note: If you are using AWS Certificate Manager, you can first go to the [AWS Certificate Manager](#aws-certificate-manager)

To deploy Kubernetes nginx controller, run:
```
astroctl app apply -f apps/nginx/aws_nginx.yaml
```

### TLS Certificate 

`Note:` You can skip this section if you are using AWS Certificate Manager

We are using cert-manager to manage the TLS certificate.

```
astroctl app apply -f apps/cert-manager/cert-manager.yaml
```

### AWS

#### AWS Certificate Manager

If you are using AWS Certificate Manager, change the annotation in the apps/nginx/aws_nginx.yaml `service.beta.kubernetes.io/aws-load-balancer-ssl-cert` to your certificate ARN. Follow https://aws.amazon.com/certificate-manager/ to create a certificate. The
existing aws_nginx.yaml is using the global default certificate that will be issued by cert-manager. 

> **Note:** Make sure to disable the `default-ssl-certificate` in the apps/nginx/aws_nginx.yaml if using ACM.

```
extraArgs:
    default-ssl-certificate: "cert-manager/nginx-global-default-tls-cert"
```



#### AWS Route 53

For AWS Route 53, please refer to https://cert-manager.io/docs/providers/aws/ for more information.

### Google Cloud DNS

First, we need to deploy cert-manager that will issue the certificate. For this example, we are using Google Cloud DNS to issue the certificate.


First we need to setup a service account in Google Cloud DNS to allow cert-manager to create the necessary records. This examples assumes you have a GCP project and your google DNS zone is already created.

Let's setup all the variables:
```
export GCP_PROJECT_ID=<your-gcp-project-id>
export GOOGLE_SERVICE_ACCOUNT_NAME=dns01-solver
```


Now we need to run the following command to create the service account and add the necessary permissions:
```
export GCP_PROJECT_ID=<your-gcp-project-id>
export GOOGLE_SERVICE_ACCOUNT_NAME=dns01-solver

gcloud iam service-accounts create $GOOGLE_SERVICE_ACCOUNT_NAME --display-name $GOOGLE_SERVICE_ACCOUNT_NAME

gcloud projects add-iam-policy-binding $GCP_PROJECT_ID --member="serviceAccount:$GOOGLE_SERVICE_ACCOUNT_NAME@$PROJECT_ID.iam.gserviceaccount.com" --role="roles/dns.admin"

gcloud iam service-accounts keys create key.json --iam-account=$GOOGLE_SERVICE_ACCOUNT_NAME@$PROJECT_ID.iam.gserviceaccount.com
```

For more information, please refer to https://cert-manager.io/docs/configuration/acme/dns01/google/

To deploy the Let's Encrypt issuer and certificate, follow these steps:

1. Update the `commonName` and `dnsNames` in `apps/nginx/resources/certificate.yaml` with your domain values.
2. Modify the `project` and `email` fields in `apps/nginx/resources/gcp_cert-manager-issuer.yaml` to match your GCP project and email address.
3. Run the following command to apply the changes:

```
astroctl app apply -f apps/nginx/nginx-certs.yaml
```

This will deploy all the yaml files in resources/nginx folder. Make sure to replace with your own values. 


### External DNS

This section demonstrates how to configure External DNS to automatically create DNS records for your applications. The ingress and service 
must include `external-dns.alpha.kubernetes.io/hostname` annotation with the domain name. Example:

```
external-dns.alpha.kubernetes.io/hostname: "myapp.example.com"
```


#### Google Cloud DNS

Make sure to update the `domainFilters` and `txtOwnerId` in the apps/external-dns/gcp.yaml with your own values.

To deploy External DNS, run:
```
astroctl app apply -f apps/external-dns/gcp.yaml
```


#### AWS Route 53

Make sure to update the `domainFilters` and `txtOwnerId` in the apps/external-dns/aws.yaml with your own values.

To deploy External DNS, run:
```
astroctl app apply -f apps/external-dns/aws.yaml
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
astroctl app events hello-world -k -ojson
```

### Logs
To view logs:
```
astroctl app logs hello-world -ojson
```

### Access
To find the HTTP endpoint (if external access is configured), run:

```
astroctl app get hello-world | grep endpoint
```

If external access is not configured, you can use port forwarding to access the application:

First you need to access the cluster:
```
astroctl infra k8s set-context <cluster-name>
```

Then you can use port forwarding to access the application:

First find the target namespace:
```
astroctl app get hello-world | grep targetNamespace
```

Then run the following command:

```
export TARGET_NAMESPACE=$(astroctl app get hello-world | grep targetNamespace | awk '{print $2}')
kubectl port-forward svc/$TARGET_NAMESPACE-app 8080:80 -n $TARGET_NAMESPACE
```
Open the browser and navigate to `http://localhost:8080`


## Remote Kubernetes Access
1. Find the `clusterName`:

For example, `hello-world` application is deployed on `test-dev` cluster. We can find the cluster name by running:

```
astroctl app get hello-world | grep clusterName
```
2. Set the context with the retrieved `clusterName`:
```
astroctl infra k8s set-context <clusterName>
```
This will generate the kubeconfig and change the context. Now, you can run standard `kubectl` commands.

To delete the kubeconfig for your cluster on your local machine:
```
astroctl infra k8s delete-context <clusterName>
```

## Deploying Services

You can deploy any cloud-native services that work with Kubernetes using either Helm or YAML.

These examples showcase popular cloud-native services that assist in managing the application lifecycle. Please note that the platform does not automatically configure external access for applications when deploying via `helm` (helm repository), `repository` (helm from git repository), or `yaml`.

> **Note:**  Make sure to use the right storage class for the persistent volume claim (PVC) in the resources/ folder. Only storage classes that are created part of the cluster for eks is gp2. There will be no default storage class for the PVC. So make sure to create the storage class before deploying the application.

### Cert-Manager
To deploy Cert-Manager:

Follow the steps in [Cert-Manager](#tls-certificate)

### Grafana
To deploy Grafana:
```
astroctl app apply -f apps/grafana/grafana.yaml
```
#### Access via port-forward

First get the admin password:

```
kubectl -n monitoring get secret -l app.kubernetes.io/name=grafana -o jsonpath="{.items[0].data.admin-password}" | base64 --decode; echo
```

and run the following command:

```
kubectl -n monitoring port-forward $(kubectl -n monitoring get pods -l app.kubernetes.io/name=grafana -o name) 3000:3000
```

Then open the browser and navigate to `http://localhost:3000` with the username `admin` and the password you retrieved earlier.


### Prometheus Operator
To deploy Prometheus:
```
astroctl app apply -f apps/prometheus/prometheus.yaml
```

#### Access via port-forward

```
kubectl port-forward $(kubectl get pods -l app.kubernetes.io/name=prometheus,app.kubernetes.io/component=server -o name) 9090:9090
```

Then open the browser and navigate to `http://localhost:9090`

## Clickhouse
To deploy Clickhouse:

```
astroctl app apply -f apps/clickhouse/clickhouse.yaml
```

## External Secrets

This will allows you to connect to cloud services like AWS Secrets Manager, Google Secret Manager, and Azure Key Vault from your Kubernetes cluster.

To deploy External Secrets:
```
astroctl app apply -f apps/external-secret/external-secret.yaml
```

## Confluent Operator (CFK)

To deploy Confluent Kafka (CFK):
```
astroctl app apply -f apps/confluent-kafka/confluent-operator.yaml
```

### Confluent Kafka

To deploy Confluent Kafka:
```
astroctl app apply -f apps/confluent-kafka/kafka.yaml
```
Check resources/cfk folder for more examples.


## Strimzi Kafka

To deploy Strimzi Operator:
```
astroctl app apply -f apps/strimzi/strimzi.yaml
```

### To deploy Strimzi Kafka (ephemeral storage):

```
astroctl app apply -f apps/strimzi/strimzi-kafka.yaml
```
and a topic `test-topic`.

Check resources/strimzi folder for more examples.


## Otel Collector

To deploy Otel Collector:
```
astroctl app apply -f apps/opentelemetry/otel-collector.yaml
```

## Kube State Metrics

To deploy Kube State Metrics:
```
astroctl app apply -f apps/kube-state-metrics/ksm.yaml
```

## Kubernetes Nginx Ingress Controller

For AWS, Follow the steps in [Kubernetes Nginx Ingress Controller (AWS)](#aws)
For Google Cloud DNS, Follow the steps in [Kubernetes Nginx Ingress Controller (Google Cloud DNS)](#google-cloud-dns)


### Debugging

First check if the CI/CD pipeline is successful.
```
astroctl app events <app-name> -ojson
```

and check the Kubernetes events:
```
astroctl app events <app-name> -k -ojson
```

To get the logs for the application:
```
astroctl app logs <app-name> -ojson
```

**NOTE:** For EKS, if you see unauthorized error, give some time and try again as the platform has to regenerate AWS credentials that probably takes upto 2 minutes.

If you see any other errors, please check the [AstroPulse Support](mailto:contact@astropulse.io)

### Reference

- Any changes on the git will trigger a pipeline and takes upto 3 minutes to get deployed.
- You can always use `kubectl` to get the logs and events for any application. Follow [Remote Kubernetes Access](#remote-kubernetes-access) to access the cluster.
- For `private git repository`, you need to setup the authentication for the git repository on the Astro Platform. Please contact [AstroPulse Support](mailto:contact@astropulse.io) for more information on this. 
   - In future releases, we will have automatic integration with private git repository via `astroctl app config set git.token <token> git.url=<https-url>`.
