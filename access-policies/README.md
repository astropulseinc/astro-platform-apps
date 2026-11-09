# Access policies

An access policy decides who may call a model endpoint. The gateway add-on
checks it before a request reaches the model; a request it refuses gets HTTP
401. A model names its policy in `externalAccess.accessPolicyRef`.

| File | Admits |
|---|---|
| [`api-keys.yaml`](api-keys.yaml) | Callers with a key AstroPulse issued for the policy |
| [`jwt.yaml`](jwt.yaml) | Callers with a token from your identity provider that names the right audience and claims |

## Apply

```bash
astroctl access-policy apply -f api-keys.yaml --dry-run
astroctl access-policy apply -f api-keys.yaml
astroctl access-policy key create partner-keys --label partner-a   # shown once; store it
astroctl access-policy get
```

For `jwt.yaml`, AstroPulse reads the issuer's discovery document to find its
signing keys. Set `jwksUri` if the issuer publishes none.

In the console: Integrations > Access Policies.

Then attach the policy to a model: [`../applications/model/api-keys-endpoint.yaml`](../applications/model/api-keys-endpoint.yaml)
or [`../applications/model/jwt-endpoint.yaml`](../applications/model/jwt-endpoint.yaml).

Docs: [Access policies](https://astropulse.io/docs/latest/platform/access-policies/overview)
