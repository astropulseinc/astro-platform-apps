# Hello world from plain manifests

[`hello-world.yaml`](hello-world.yaml) applies the files in
[`manifests/`](manifests/): a two-replica Deployment and its Service, in the
namespace `hello-world-yaml`. A change pushed to the folder is applied; a file
removed from it is deleted from the cluster.

```bash
astroctl app apply -f hello-world.yaml --dry-run
astroctl app apply -f hello-world.yaml
astroctl app status hello-world-yaml
```

To deploy your own manifests, copy this folder into your repository and point
`repoURL` and `path` at it. `include` and `exclude` take file patterns, and
`recursive: false` stops at the top folder.
