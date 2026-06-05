# Application Examples

Ready-to-use application deployment templates. Each application is deployed using `astroctl app apply`.

## Applications

| Directory | Application | Type | Description |
|-----------|------------|------|-------------|
| [hello-world/](hello-world/) | Hello World | Image | Simple test application to verify your setup |
| [nginx/](nginx/) | NGINX Ingress | Helm | Kubernetes ingress controller for external access |
| [cert-manager/](cert-manager/) | Cert-Manager | Helm | Automatic TLS certificate management |
| [external-dns/](external-dns/) | External DNS | Helm | Automatic DNS record creation (AWS/GCP) |
| [grafana/](grafana/) | Grafana | Helm | Monitoring dashboards and visualization |
| [prometheus/](prometheus/) | Prometheus | Helm | Metrics collection and alerting |
| [clickhouse/](clickhouse/) | ClickHouse | Helm | Column-oriented data warehouse |
| [external-secret/](external-secret/) | External Secrets | Helm | Cloud secret manager integration |
| [confluent-kafka/](confluent-kafka/) | Confluent Kafka | Helm | Kafka streaming platform (Confluent) |
| [strimzi/](strimzi/) | Strimzi Kafka | Helm | Kafka streaming platform (Strimzi) |
| [opentelemetry/](opentelemetry/) | OpenTelemetry | Helm | Distributed tracing and metrics collection |
| [kube-state-metrics/](kube-state-metrics/) | Kube State Metrics | Helm | Kubernetes cluster metrics exporter |

## Quick Start

```bash
# Deploy an application profile first (required)
astroctl app profile apply -f ../app-profiles/profile3.yaml

# Deploy an application
astroctl app apply -f hello-world/demo.yaml

# Check status
astroctl app status hello-world

# View logs
astroctl app logs hello-world

# Access locally via port-forward
astroctl infra k8s set-context <cluster-name>
kubectl -n <namespace> port-forward svc/<app> 8080:80
```

## Debugging

```bash
# Check CI/CD events
astroctl app events <app-name>

# Check Kubernetes events
astroctl app events <app-name> -k

# Check logs
astroctl app logs <app-name>

# Check status
astroctl app status <app-name>
```

## Documentation

- [Application Deployment](https://astropulse.io/docs/latest/platform/application-pipeline/app-deployment)
