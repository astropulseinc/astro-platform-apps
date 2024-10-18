
# Astro Platform Application Deployment Guide


## Table of Contents
1. [Download astroctl CLI](#download-astroctl-cli)
2. [Generate API Key](#generate-api-key-to-interact-with-astro-platform)
3. [Deploy an EKS Cluster](#deploy-an-eks-cluster)
4. [Manage Clusters](#available-clusters-and-their-state)
5. [Deploy Application Profiles](#deploy-application-profiles)
6. [External Access Setup (Optional)](#bring-your-own-external-access-optional)
   - [Kubernetes NGINX Ingress Controller](#kubernetes-nginx-ingress-controller)
   - [TLS Certificate](#tls-certificate)
   - [AWS Specific Setup](#aws)
   - [Google Cloud DNS Setup](#google-cloud-dns)
   - [External DNS](#external-dns)
7. [Deploy Hello-world Application](#deploy-hello-world-application)
8. [Remote Kubernetes Access](#remote-kubernetes-access)
9. [Deploying Services](#deploying-services)
   - [Cert-Manager](#cert-manager)
   - [Grafana](#grafana)
   - [Prometheus](#prometheus-operator)
   - [Clickhouse](#clickhouse)
   - [External Secrets](#external-secrets)
   - [Confluent Operator (CFK)](#confluent-operator-cfk)
   - [Strimzi Kafka](#strimzi-kafka)
   - [Otel Collector](#otel-collector)
   - [Kube State Metrics](#kube-state-metrics)
10. [Kubernetes Nginx Ingress Controller](#kubernetes-nginx-ingress-controller-1)
11. [Reference](#reference)

## Download astroctl CLI
```
curl -L https://storage.googleapis.com/astroctl-cli/astroctl-$(go env GOOS).$(go env GOARCH).tar.gz | tar -xz
chmod +x astroctl
sudo mv astroctl /usr/local/bin
```

## Generate API Key to Interact with Astro Platform
To generate an API key:
```
astroctl auth login
```

## Deploy an EKS Cluster

On this example, we will deploy a cluster on AWS EKS using a BYOA (Bring Your Own Account) method.

Note: Don't forget to update the `aws_eks_byoa.yaml` with your own `accountId` and `region`

To deploy a cluster, run:
```
 astroctl clusters apply -f cluster-template/aws/eks/byoa.yaml
```

> **Note:** Admin access to the EKS cluster is required to deploy the services. If you need access, please contact [AstroPulse support](mailto:contact@astropulse.io). After obtaining access, run `astroctl auth login` to log in to the Astro Platform. To verify admin access, run `astroctl whoami` and check the roles section for the `admin` role.

For different type of clusters, check the [documentations](https://astropulse.io/docs/latest/platform/cluster-pipeline/cluster-mgmt)

## Available Clusters and their State
```
astroctl clusters get
```

## Access an existing cluster
```
astroctl clusters set-context test-dev
```

## Deploy Application Profiles

> **Note:** Update the `app-profiles/` file to include the `clusterName` of your choice.


To find the cluster names for your organization, run:

```
astroctl clusters get 
```

Then apply the application profile:
```
astroctl app profile apply -f app-profiles/
```

## Bring your own External Access (Optional)

`Note: Skip this section if using an Astro-managed cluster, as external access is pre-configured for appliction which are using source type image`

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
astroctl app events hello-world -k ojson
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
astroctl clusters set-context set-context
```

Then you can use port forwarding to access the application:
```
kubectl port-forward svc/hello-world 8080:80 -n <namespace>
```

You can find the <namespace> in the `astroctl app get <app-name>` command.


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

These examples showcase popular cloud-native services that assist in managing the application lifecycle. Please note that the platform does not automatically configure external access for applications when deploying via `helm` (helm repository), `repository` (helm from git repository), or `yaml`.


### Cert-Manager
To deploy Cert-Manager:

Follow the steps in [Cert-Manager](#tls-certificate)

### Grafana
To deploy Grafana:
```
astroctl app apply -f apps/grafana/grafana.yaml
```

### Prometheus Operator
To deploy Prometheus:
```
astroctl app apply -f apps/prometheus/prometheus.yaml
```

## Clickhouse
To deploy Clickhouse:
```
astroctl app apply -f apps/clickhouse/clickhouse.yaml
```

## External Secrets

This will allows you to connect to cloud services like AWS Secrets Manager, Google Secret Manager, and Azure Key Vault from your Kubernetes cluster.

To deploy External Secrets:
```
astroctl app apply -f apps/external-secrets/external-secrets.yaml
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

### To deploy Strimzi Kafka:

```
astroctl app apply -f apps/strimzi/strimzi-kafka.yaml
```

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

### Reference

- Any changes on the git will trigger a pipeline and takes upto 3 minutes to get deployed.
- Application with source type yaml doesn't have logs access via astroctl. You can use `kubectl` to get the logs.
- You can always use `kubectl` to get the logs and events for any application.
