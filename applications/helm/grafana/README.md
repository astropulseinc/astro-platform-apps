# Grafana

[`grafana.yaml`](grafana.yaml) deploys Grafana into the `monitoring` namespace
with a 10 GiB volume on the cluster's default StorageClass and the ClickHouse
data source plugin. Set `persistence.storageClassName` if the cluster has no
default StorageClass.

```bash
astroctl app apply -f grafana.yaml
astroctl infra k8s set-context <YOUR_CLUSTER_NAME>
kubectl -n monitoring get secret -l app.kubernetes.io/name=grafana -o jsonpath='{.items[0].data.admin-password}' | base64 -d; echo
kubectl -n monitoring port-forward $(kubectl -n monitoring get svc -l app.kubernetes.io/name=grafana -o name) 3000:80
# then open http://localhost:3000 and sign in as admin
```
