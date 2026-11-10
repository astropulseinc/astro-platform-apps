# Kafka with Confluent for Kubernetes

| File | Deploys |
|---|---|
| [`confluent-operator.yaml`](confluent-operator.yaml) | The Confluent for Kubernetes operator, watching every namespace |
| [`kafka.yaml`](kafka.yaml) | The manifests in [`resources/`](resources/): a three-node ZooKeeper ensemble, a three-broker Kafka cluster and a topic |

```bash
astroctl app apply -f confluent-operator.yaml
astroctl app apply -f kafka.yaml          # once the operator is running
```

Both deploy into the `confluent` namespace. Each broker asks for a 100 GiB
volume on the cluster's default StorageClass.
