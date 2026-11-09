# Model applications

A model application serves a model from Hugging Face (`hf://`), object storage
or a volume behind an OpenAI-compatible API. AstroPulse picks the serving
runtime, checks the cluster can hold the model, and scales its replicas.

| File | Deploys |
|---|---|
| [`cpu-small.yaml`](cpu-small.yaml) | Qwen2.5 0.5B Instruct on CPU, inside the cluster |
| [`gpu.yaml`](gpu.yaml) | Qwen3 8B on an NVIDIA L4 per replica, 1 to 4 replicas on waiting requests |
| [`api-keys-endpoint.yaml`](api-keys-endpoint.yaml) | Qwen3 8B at `https://qwen-chat.example.com`, callable with an API key |
| [`jwt-endpoint.yaml`](jwt-endpoint.yaml) | Qwen3 8B at `https://chat.example.com`, callable with a JWT from your identity provider |
| [`queued.yaml`](queued.yaml) | Qwen3 8B taking its GPU through a workload queue |

## Before you apply

| Needed for | What |
|---|---|
| every file | A profile ([`../../profiles/`](../../profiles/)); the `cert-manager` and `model-serving` add-ons |
| GPU files | GPU capacity: a GPU node group ([`../../clusters/aws/eks/gpu-node-group.yaml`](../../clusters/aws/eks/gpu-node-group.yaml)) or the Node Autoscaling GPU pool ([`../../add-ons/karpenter-gpu-pool.yaml`](../../add-ons/karpenter-gpu-pool.yaml)) |
| scaling on waiting requests | The `cost-visibility` and `workload-autoscaling` add-ons |
| endpoints | Your domain verified (Integrations > Domains); the `gateway` add-on and cert-manager with a DNS solver ([`../../add-ons/cert-manager-letsencrypt-dns.yaml`](../../add-ons/cert-manager-letsencrypt-dns.yaml)); the access policy ([`../../access-policies/`](../../access-policies/)) |
| `queued.yaml` | The `workload-queueing` add-on with an `inference` queue ([`../../add-ons/workload-queueing.yaml`](../../add-ons/workload-queueing.yaml)) |
| gated models | Hugging Face connected once for your organization: `astroctl integrations huggingface connect --token <YOUR_HF_TOKEN>` or Integrations > Hugging Face |

## Apply

```bash
astroctl app apply -f cpu-small.yaml --dry-run   # says where it would run and on which runtime
astroctl app apply -f cpu-small.yaml
astroctl app get qwen-small
```

Call an endpoint with the key or token its policy accepts:

```bash
curl https://qwen-chat.example.com/v1/models -H "Authorization: Bearer <YOUR_API_KEY>"
```

In the console: Workloads > Models, then **Deploy model**.

Docs: [Model source](https://astropulse.io/docs/latest/platform/application-pipeline/application-resource/source/model)
