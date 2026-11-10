# YAML applications

An application with `source.type: YAML` applies the Kubernetes manifests in a
Git folder and keeps the cluster in sync with it.

| Folder | Deploys |
|---|---|
| [`hello-world/`](hello-world/) | A Deployment and a Service from [`hello-world/manifests/`](hello-world/manifests/) |
