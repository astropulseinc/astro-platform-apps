# Hello world from a Helm chart in Git

[`hello-chart.yaml`](hello-chart.yaml) renders the chart in [`chart/`](chart/)
with `replicaCount: 2` and deploys it into the namespace `hello-chart`.

```bash
astroctl app apply -f hello-chart.yaml --dry-run
astroctl app apply -f hello-chart.yaml
astroctl app status hello-chart
```

Values come from `values.object`, or from files in the chart folder:

```yaml
values:
  files: [values-production.yaml]
```

To deploy your own chart, copy this folder into your repository and point
`repoURL` and `path` at the chart.
