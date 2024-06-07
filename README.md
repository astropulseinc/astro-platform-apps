# Create Organization (needs admin role)
`astroctl org create test-org`

# Get the Organization Id (needs the admin role)
`astroctl org list`

# Generate API Key to interact with Astro Platform
`astroctl auth login --org-id <org_id>`

# Check if there are any cluster
`astroctl provider clusters list -p aws` (aws)
`astroctl provider clusters list -p gcp` (gcp)

# Ask the administrator to deploy the cluster
If  there are no cluster, ask administrator to deploy the cluster (requires `platform-admin or admin` role)

# Check if any cluster is available
`astroctl provider clusters list -p aws`
`astroctl provider clusters list -p gcp`

Wait till, there are any cluster availabile

# Deploy Application Profiles
Note: Make sure to update the app-profiles/selected-cluster.yaml to update the clusterName of your choice. To find the cluster names (for your organization) run 
`astroctl provider clusters list -p aws` (for aws)
`astroctl provider clusters list -p gcp` (for gcp)
`astroctl app profile apply -f app-profiles/`

# Deploy Hello-world Application
`astroctl app apply -f apps/hello-world/demo.yaml`

## status check
`astroctl app status hello-world`

## events
`astroctl app events hello-world -ojson` (ci/cd events)
`astroctl app events hello-world -k ojson` (k8s events)

## logs
`astroctl app logs hello-world -ojson` 

## Access
To find the http endpoint run the command 
`astroctl app get hello-world | grep endpoint`

## Remote K8s Access
- First find the clusterName
-  `astroctl app status  hello-world | grep clusterName`

Once clusterName is retrieved, replace <clusterName> as required

`astroctl provider clusters set-context <clusterName>`

Once you run the above command it will generate the kubeconfig and change the context.
Now, you can run the standard `kubectl` command

For `kubeconfig` for your clsuter deletion run the command
`astroctl provider clusters delete-context <clusterName>`


# Deploying Services

These are some examples that provides popular cloud native services that helps to manage
application lifecycle. For platform doesn't create any external access configuration for application
when deploying through source type `helm (helm repo)` `repository (helm from git code)` or `yaml`

## Cert-Manager
`astroctl app apply -f apps/cert-manager/cert-manager.yaml`

## Grafana
`astroctl app apply -f apps/grafana/grafana.yaml`

## Prometheus
`astroctl app apply -f apps/prometheus/prometheus.yaml`

### Debug
use the astroctl app logs/events and remote access to debug

## Deploy CFK Operator (for Confluent Data Streaming)

`astroctl app apply -f apps/operators/cfk/cfk.yaml`

### Debug
use the astroctl app logs/events and remote access to debug

## Deploy Kafka Cluster (CFK)
`astroctl app apply -f apps/confluent-kafka/cfk.yaml`

`Note:` for this logs subcommand is not available from astroctl






