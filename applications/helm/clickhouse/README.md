# ClickHouse

| File | Deploys |
|---|---|
| [`clickhouse-operator.yaml`](clickhouse-operator.yaml) | The Altinity ClickHouse operator, from its Helm chart |
| [`clickhouse.yaml`](clickhouse.yaml) | The manifests in [`resources/`](resources/): a one-node ClickHouse cluster named `analytics` |

```bash
astroctl app apply -f clickhouse-operator.yaml
astroctl app apply -f clickhouse.yaml     # once the operator is running
```

Both deploy into the `clickhouse` namespace. Operator documentation:
https://github.com/Altinity/clickhouse-operator
