# Kafka with Strimzi

| File | Deploys |
|---|---|
| [`strimzi.yaml`](strimzi.yaml) | The Strimzi Kafka operator 0.43.0, watching every namespace |
| [`kafka.yaml`](kafka.yaml) | The manifests in [`resources/`](resources/): a one-node Kafka 3.8 cluster in KRaft mode with ephemeral storage, and the topic `test-topic` |

```bash
astroctl app apply -f strimzi.yaml
astroctl app apply -f kafka.yaml          # once the operator is running
```

Both deploy into the `strimzi-kafka` namespace. More Kafka examples for this
operator version:
https://github.com/strimzi/strimzi-kafka-operator/tree/0.43.0/examples/kafka
