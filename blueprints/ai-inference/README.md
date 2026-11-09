# Production AI inference

`qwen-chat` as a model API at `https://qwen-chat.apps.example.com` in your own
AWS account, callable only with a token from your identity provider. GPU nodes
appear for pending replicas and leave when empty, replicas scale with waiting
requests, and cost and GPU metrics are reported per model.

| Path | What it is |
|---|---|
| [`cluster.yaml`](cluster.yaml) | An EKS cluster: three small platform nodes, no always-on GPU |
| [`add-ons/`](add-ons/) | The ten add-ons, numbered in install order in each file's first line |
| [`profile.yaml`](profile.yaml) | The profile `inference` |
| [`access-policy.yaml`](access-policy.yaml) | The JWT policy `qwen-callers` |
| [`model.yaml`](model.yaml) | `qwen-chat`: Qwen3 8B on NVIDIA L4, 1 to 2 replicas |

## Before you start

- An AWS account with GPU quota above zero in your region:
  `astroctl cloud aws accelerators list -r <YOUR_REGION>`.
- A subdomain you own (`apps.example.com` here) with a Route 53 hosted zone,
  and the domain verified under Integrations > Domains.
- An identity provider with an issuer URL that serves an OpenID discovery
  document; put it in `access-policy.yaml`. AstroPulse reads its signing keys
  before saving the policy.
- For a gated model, a Hugging Face token: `echo "$HF_TOKEN" | astroctl integrations huggingface connect --token-stdin`.

## Apply, in order

```bash
astroctl cloud aws connect --account-id <YOUR_AWS_ACCOUNT_ID> --cluster-name <YOUR_CLUSTER_NAME> --region <YOUR_REGION>
astroctl infra k8s apply -f cluster.yaml --dry-run
astroctl infra k8s apply -f cluster.yaml

for f in cert-manager karpenter model-serving gateway cost-visibility \
         workload-autoscaling accelerator-metrics vulnerability-scanning \
         external-dns workload-queueing; do
  astroctl infra k8s addons apply -f add-ons/$f.yaml
done

astroctl app profile apply -f profile.yaml
astroctl access-policy apply -f access-policy.yaml
astroctl app apply -f model.yaml --dry-run
astroctl app apply -f model.yaml
```

External DNS and Workload Queueing are optional. Node Autoscaling goes before
Model Serving, because the weight cache lives on its GPU node pool.

cert-manager starts on the Let's Encrypt staging issuer, which avoids its rate
limits while you set up but is not trusted by clients. Once the model's
certificate is issued, switch to production and apply again:

```bash
sed -i.bak 's/issuerType: LetsEncryptStaging/issuerType: LetsEncrypt/' add-ons/cert-manager.yaml
astroctl infra k8s addons apply -f add-ons/cert-manager.yaml
```

## Check it

With the production issuer in place:

```bash
astroctl app get qwen-chat
curl -i https://qwen-chat.apps.example.com/v1/models                                  # HTTP 401
curl https://qwen-chat.apps.example.com/v1/models -H "Authorization: Bearer <YOUR_TOKEN>"
```

In the console: Workloads > Models > `qwen-chat`.

Docs: [Run production AI inference](https://astropulse.io/docs/examples/ai-infrastructure/production-ai-inference)
