# Developer platform

The platform side of a path from `git push` to a running service, set up once
per cluster:

- [kpack](https://github.com/buildpacks-community/kpack) builds container images
  from source with Cloud Native Buildpacks and pushes them to your registry.
- [Kyverno](https://kyverno.io) policies report pods that use a `latest` tag or
  an image from outside your registry.

The application side, the build and deploy files a service carries and the
GitHub workflow that ships it, lives in
[astropulseinc/latency-app](https://github.com/astropulseinc/latency-app).

| Path | What it is |
|---|---|
| [`platform-profile.yaml`](platform-profile.yaml) | The profile `platform`, pointing at your cluster |
| [`applications/kpack.yaml`](applications/kpack.yaml) | kpack, from its unmodified v0.18.0 release manifest in [`resources/kpack-release/`](resources/kpack-release/) |
| [`applications/kpack-builder.yaml`](applications/kpack-builder.yaml) | The builder in [`resources/kpack/`](resources/kpack/): stack, store, ClusterBuilder and the service account builds push with |
| [`applications/kyverno.yaml`](applications/kyverno.yaml) | Kyverno, from its Helm chart |
| [`applications/kyverno-policies.yaml`](applications/kyverno-policies.yaml) | The policies in [`resources/kyverno/`](resources/kyverno/), in Audit mode for the `latency-app` namespace |

## Before you apply

1. Fork this repository into your GitHub organization. `kpack-builder.yaml` and
   `kyverno-policies.yaml` read from your fork, because you edit those files.
2. In your fork, replace `<YOUR_GITHUB_ORG>` in
   `applications/kpack-builder.yaml`, `applications/kyverno-policies.yaml`,
   `resources/kpack/cluster-builder.yaml` and
   `resources/kyverno/require-trusted-registry.yaml`, and `<YOUR_CLUSTER_NAME>`
   in `platform-profile.yaml`. Commit and push.
3. Create the registry Secret builds push with:

   ```bash
   astroctl infra k8s set-context <YOUR_CLUSTER_NAME>
   kubectl create namespace kpack
   kubectl create secret docker-registry registry-credentials -n kpack \
     --docker-server=ghcr.io --docker-username=<YOUR_GITHUB_USER> --docker-password=<YOUR_REGISTRY_TOKEN>
   ```

## Apply

```bash
astroctl app profile apply -f platform-profile.yaml
astroctl app apply -f applications/kpack.yaml
astroctl app apply -f applications/kyverno.yaml
astroctl app apply -f applications/kpack-builder.yaml      # once kpack is running
astroctl app apply -f applications/kyverno-policies.yaml   # once Kyverno is running
```

The policies only report (`validationActions: [Audit]`). Switch them to
`[Deny]` once the reports are clean, and list the namespaces your services run
in under `namespaceSelector`.

Walkthrough: [From Git push to production](https://astropulse.io/docs/blog/building-modern-paas-platform-with-astropulse)
