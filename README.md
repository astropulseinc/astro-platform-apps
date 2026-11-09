# AstroPulse examples

Ready-to-apply manifests for [AstroPulse](https://astropulse.io): clusters,
add-ons, application profiles, applications of every source type (models
included), access policies, and blueprints that put them together.

Everything here can also be done in the [AstroPulse console](https://astropulse.io/console).
The files are for teams that keep their platform in Git or prefer the command line.

## Layout

| Folder | What it holds | Apply with |
|---|---|---|
| [`clusters/`](clusters/) | Create a cluster on AWS, Google Cloud or Azure, or register one you have | `astroctl infra k8s apply` / `register` |
| [`add-ons/`](add-ons/) | One file per managed add-on: ingress, certificates, DNS, node and workload autoscaling, cost, scanning, model serving, gateway, queueing, GPU metrics | `astroctl infra k8s addons apply` |
| [`profiles/`](profiles/) | Application profiles: which cluster an application deploys to | `astroctl app profile apply` |
| [`applications/`](applications/) | Applications by source type: [`image/`](applications/image/), [`helm/`](applications/helm/), [`yaml/`](applications/yaml/), [`repository/`](applications/repository/), [`model/`](applications/model/) | `astroctl app apply` |
| [`access-policies/`](access-policies/) | Who may call a model endpoint: API keys or JWTs | `astroctl access-policy apply` |
| [`blueprints/`](blueprints/) | Complete setups: a [developer platform](blueprints/developer-platform/) and [production AI inference](blueprints/ai-inference/) | the commands above, in the order each README gives |

Every folder has a README that says what its files deploy, what they need
first and how to apply them.

## Quick start

```bash
# Install the CLI and sign in
curl -fsSL https://astropulse.io/install.sh | bash
astroctl auth login

# A cluster: register one you have (uses your current kubectl context) ...
astroctl infra k8s register --cluster-name <YOUR_CLUSTER_NAME>
# ... or create one, after connecting your cloud account (see clusters/)

# A profile that points at it, then an application
astroctl app profile apply -f profiles/specific-cluster.yaml
astroctl app apply -f applications/image/hello-world.yaml
astroctl app status hello-world
```

Every `apply` takes `--dry-run`: AstroPulse checks the file and says what it
would do, without changing anything.

## Use these placeholders

Replace every value in angle brackets before you apply a file.

| Placeholder | Replace with |
|---|---|
| `<YOUR_CLUSTER_NAME>` | Your cluster's name in AstroPulse (`astroctl infra k8s get`) |
| `<YOUR_PROFILE_NAME>` | An application profile's name (`astroctl app profile get`) |
| `<YOUR_REGION>` | A cloud region, such as `us-east-1`, `us-central1` or `eastus` |
| `<YOUR_ZONE_A>`, `<YOUR_ZONE_B>`, `<YOUR_ZONE_C>` | Availability zones in that region |
| `<YOUR_AWS_ACCOUNT_ID>` | Your 12-digit AWS account ID |
| `<YOUR_GCP_PROJECT_ID>` | Your Google Cloud project ID |
| `<YOUR_SUBSCRIPTION_ID>`, `<YOUR_RESOURCE_GROUP>` | Your Azure subscription ID and resource group |
| `<YOUR_VPC_ID>`, `<YOUR_PUBLIC_SUBNET_A>`, `<YOUR_PRIVATE_SUBNET_A>` | An existing network and its subnets |
| `<YOUR_BUCKET_NAME>` | The bucket a self-hosted setup command prints |
| `<YOUR_HOSTED_ZONE_ID>` | A Route 53 hosted zone ID |
| `<YOUR_..._ROLE>` | The name of an IAM role you created for an add-on |
| `<YOUR_GITHUB_ORG>` | Your GitHub organization or user, for a fork of this repository and its container registry |
| `example.com` | A domain you own and have verified in AstroPulse (Integrations > Domains) |
| `platform@example.com` | An email address that receives certificate notices |

## Naming

- Folders and files are lowercase kebab-case.
- A file is named after what it creates or how it differs, for example
  `clusters/aws/eks/existing-vpc.yaml` or `add-ons/karpenter-gpu-pool.yaml`.
  An add-on file is named after the add-on.
- An application's supporting manifests sit beside it, in a `resources/` or
  `manifests/` folder, and its README describes them.

## Documentation

- [AstroPulse documentation](https://astropulse.io/docs/latest/platform/example)
- [Cluster add-ons](https://astropulse.io/docs/latest/platform/cluster-pipeline/add-ons)
- [Access policies](https://astropulse.io/docs/latest/platform/access-policies/overview)

## License

[Apache License 2.0](LICENSE)
