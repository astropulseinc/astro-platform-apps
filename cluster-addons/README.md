# Managed Cluster Add-ons

Platform-curated versions of the Kubernetes ecosystem apps most clusters need —
ingress, DNS automation, TLS certificates, and node autoscaling — installed,
upgraded, and monitored with **one command** on any AstroPulse cluster
(provisioned or registered).

You declare **intent** (which add-on + a few typed options). AstroPulse owns the
chart, the version, the deployment lifecycle, and health. You never handle Helm
values, and the add-on configuration never contains cloud credentials.

> Prefer a UI? Manage the same add-ons from the [Astro Console](https://astropulse.io/console)
> cluster page. Full docs:
> [Cluster Add-ons](https://astropulse.io/docs/latest/platform/cluster-pipeline/add-ons).

## Examples

| File | Add-on | Namespace | Notes |
|---|---|---|---|
| [`nginx-ingress.yaml`](nginx-ingress.yaml) | NGINX Ingress | `ingress-nginx` | TLS terminated at the controller; pair with cert-manager |
| [`external-dns.yaml`](external-dns.yaml) | ExternalDNS | `external-dns` | **Requires cluster-side DNS identity** (IRSA / Workload Identity) |
| [`cert-manager.yaml`](cert-manager.yaml) | cert-manager | `cert-manager` | Installs CRDs; optional Let's Encrypt ClusterIssuer |
| [`karpenter.yaml`](karpenter.yaml) | Karpenter | `kube-system` | AWS only; cluster-managed on EKS |

Each add-on installs into its **own dedicated namespace** (created automatically).

## Apply

```bash
# Install or update (declarative + idempotent — edit and re-apply to change):
astroctl infra k8s addons apply -f nginx-ingress.yaml

# Watch it converge, then observe:
astroctl infra k8s addons status  <cluster> nginx-ingress
astroctl infra k8s addons get     <cluster>
astroctl infra k8s addons logs    <cluster> nginx-ingress
astroctl infra k8s addons events  <cluster> nginx-ingress
astroctl infra k8s addons history <cluster> nginx-ingress

# Upgrade / roll back / uninstall:
astroctl infra k8s addons versions <cluster> cert-manager
astroctl infra k8s addons rollback <cluster> cert-manager
astroctl infra k8s addons delete   <cluster> cert-manager
```

Edit `spec.version` or the `configuration` in any file and re-apply to upgrade or
reconfigure in place.

## Cloud identity (ExternalDNS and Karpenter)

These add-ons reach cloud APIs with the **cluster's own identity** — IRSA on AWS,
Workload Identity on GCP/Azure — which you set up out of band. AstroPulse never
receives or stores cloud credentials. If you need to authenticate with a static
key/Secret instead, deploy the app yourself (see [`../apps/external-dns/`](../apps/external-dns))
rather than using the managed add-on. Per-provider setup is in the
[add-on docs](https://astropulse.io/docs/latest/platform/cluster-pipeline/add-ons#externaldns-dns-automation).
