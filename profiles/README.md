# Application Profiles

Application profiles define how applications are assigned to clusters. Create a profile first, then reference it when deploying applications.

## Templates

| File | Strategy | Description |
|------|----------|-------------|
| [profile1.yaml](profile1.yaml) | Provider + Region | Select cluster by cloud provider and region |
| [profile2.yaml](profile2.yaml) | Proximity | Select cluster closest to your organization |
| [profile3.yaml](profile3.yaml) | Explicit Cluster | Target a specific cluster by name |

## Quick Start

```bash
# 1. Choose a profile strategy and edit the YAML
# 2. Deploy the profile
astroctl app profile apply -f profile3.yaml

# 3. Verify
astroctl app profile get <profile-name>
```

## Usage

When deploying an application, reference the profile by name:

```yaml
name: my-app
profileName: profile3   # matches the profile you created
source:
  type: image
  image:
    registry: docker.io
    repository: myorg/myapp
    tag: v1.0.0
```

## Documentation

- [Application Profiles](https://astropulse.io/docs/latest/platform/application-pipeline/app-profiles)
