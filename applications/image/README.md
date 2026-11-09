# Image applications

| File | Deploys |
|---|---|
| [`hello-world.yaml`](hello-world.yaml) | A web container, reachable inside the cluster |
| [`hello-world-domain.yaml`](hello-world-domain.yaml) | The same container at `https://hello-world.example.com` |

## Before you apply

- A profile ([`../../profiles/`](../../profiles/)).
- For `hello-world-domain.yaml`: your domain verified in AstroPulse
  (`astroctl domain add example.com`, then `astroctl domain verify example.com`,
  or Integrations > Domains), and the `nginx-ingress` and `cert-manager`
  add-ons on the cluster; add `external-dns` to publish the DNS record
  ([`../../add-ons/`](../../add-ons/)).

## Apply

```bash
astroctl app apply -f hello-world.yaml --dry-run
astroctl app apply -f hello-world.yaml
astroctl app get hello-world
```

Without a domain, reach it with a port-forward:

```bash
astroctl infra k8s set-context <YOUR_CLUSTER_NAME>
kubectl get svc -A | grep hello-world
kubectl port-forward -n <namespace> svc/<service> 8080:80
```
