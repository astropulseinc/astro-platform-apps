# External Secrets Operator

[`external-secrets.yaml`](external-secrets.yaml) installs the operator and its
CRDs into the `external-secrets` namespace. It syncs secrets from AWS Secrets
Manager, Google Secret Manager, Azure Key Vault and other stores into
Kubernetes Secrets you declare with `ExternalSecret` resources.

```bash
astroctl app apply -f external-secrets.yaml
```
