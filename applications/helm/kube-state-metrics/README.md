# kube-state-metrics

[`kube-state-metrics.yaml`](kube-state-metrics.yaml) deploys kube-state-metrics
into the `monitoring` namespace, where Prometheus can scrape it.

```bash
astroctl app apply -f kube-state-metrics.yaml
```
