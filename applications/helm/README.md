# Helm chart applications

Third-party charts deployed from their public Helm repositories. Where a chart
installs an operator, the resources it runs (a Kafka cluster, a ClickHouse
installation) sit beside it in `resources/` and deploy as a second
application with `source.type: YAML`.

| Folder | Deploys |
|---|---|
| [`prometheus/`](prometheus/) | Prometheus server |
| [`grafana/`](grafana/) | Grafana |
| [`kube-state-metrics/`](kube-state-metrics/) | kube-state-metrics |
| [`opentelemetry-collector/`](opentelemetry-collector/) | OpenTelemetry Collector on every node |
| [`external-secrets/`](external-secrets/) | External Secrets Operator |
| [`clickhouse/`](clickhouse/) | ClickHouse operator and a one-node ClickHouse cluster |
| [`strimzi/`](strimzi/) | Strimzi Kafka operator and a one-node Kafka cluster |
| [`confluent-kafka/`](confluent-kafka/) | Confluent for Kubernetes and a Kafka cluster |

For ingress, certificates and DNS, use the managed add-ons in
[`../../add-ons/`](../../add-ons/) rather than deploying those charts yourself.

## Apply

```bash
astroctl app apply -f prometheus/prometheus.yaml --dry-run
astroctl app apply -f prometheus/prometheus.yaml
astroctl app status prometheus
```

The applications that deploy `resources/` folders read them from this
repository's `main` branch. To change those manifests, copy the folder into
your own repository and point `repoURL` and `path` at it.
