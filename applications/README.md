# Applications

An application is one thing AstroPulse deploys and keeps in sync: a container
image, a Helm chart, plain manifests, a chart in Git, or a model. The folders
follow the source types.

| Folder | `source.type` | Examples |
|---|---|---|
| [`image/`](image/) | `Image` | A container image, inside the cluster or on your domain |
| [`helm/`](helm/) | `Helm` | Third-party charts: Prometheus, Grafana, Kafka, ClickHouse and more |
| [`yaml/`](yaml/) | `YAML` | Plain Kubernetes manifests from a Git folder |
| [`repository/`](repository/) | `Repository` | A Helm chart kept in Git |
| [`model/`](model/) | `Model` | Hugging Face models on CPU or GPU, on your domain behind an access policy |

Every application names a profile in `profileName`; create one first from
[`../profiles/`](../profiles/).

## Apply

```bash
astroctl app apply -f image/hello-world.yaml --dry-run
astroctl app apply -f image/hello-world.yaml
astroctl app status hello-world
astroctl app events hello-world
astroctl app logs hello-world
```

In the console: Workloads > Applications, or Workloads > Models for models.

AstroPulse sets up outside access only for image and model applications
(`externalAccess`). Charts and manifests bring their own Services and Ingresses.

Docs: [Applications](https://astropulse.io/docs/latest/platform/application-pipeline/application-resource/overview)
