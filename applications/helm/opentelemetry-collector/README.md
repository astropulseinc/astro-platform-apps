# OpenTelemetry Collector

[`opentelemetry-collector.yaml`](opentelemetry-collector.yaml) runs the
collector on every node (a DaemonSet in the `opentelemetry` namespace),
gathering host and kubelet metrics, Kubernetes events and Kubernetes
attributes. Add your exporters under `values.object.config`.

```bash
astroctl app apply -f opentelemetry-collector.yaml
```
