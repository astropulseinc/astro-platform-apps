# Application profiles

A profile says which cluster an application deploys to. Every application names
one in `profileName`. Create the profile before the applications that use it.

| File | Picks |
|---|---|
| [`specific-cluster.yaml`](specific-cluster.yaml) | The cluster you name |
| [`cloud-region.yaml`](cloud-region.yaml) | A cluster in the cloud and region you name |
| [`nearest-cluster.yaml`](nearest-cluster.yaml) | The cluster nearest to your organization |

## Apply

```bash
astroctl infra k8s get                       # your cluster names
astroctl app profile apply -f specific-cluster.yaml --dry-run
astroctl app profile apply -f specific-cluster.yaml
astroctl app profile get
```

In the console: the **Profile** step of Workloads > Applications > **Deploy application**
picks or creates one.

Docs: [Application profiles](https://astropulse.io/docs/latest/platform/application-pipeline/application-profile)
