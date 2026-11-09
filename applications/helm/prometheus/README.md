# Prometheus

[`prometheus.yaml`](prometheus.yaml) deploys the Prometheus server into the
`monitoring` namespace, with Alertmanager and the Pushgateway turned off.

```bash
astroctl app apply -f prometheus.yaml
astroctl infra k8s set-context <YOUR_CLUSTER_NAME>
kubectl -n monitoring port-forward $(kubectl -n monitoring get svc -l app.kubernetes.io/name=prometheus,app.kubernetes.io/component=server -o name) 9090:80
# then open http://localhost:9090
```
