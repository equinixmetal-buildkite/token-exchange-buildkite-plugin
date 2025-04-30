# identity-api-buildkite-plugin

The Identity API Buildkite Plugin is a Buildkite plugin that facilitates token
exchange with the Identity API. It enables CI pipelines to obtain their federated
identity tokens and use them for authentication with other services.

> [!NOTE]
> Currently only pipelines under the org `equinix` are allowed to exchange
> tokens. See [infra9-tools configs](
> https://github.com/equinixmetal/infra9-tools/blob/77ea390a7d35086d51965d09fad654aaf5dedccf/data/prod/delivery.yaml#L136
> )

## Usage

```yaml
steps:
  - label: "token-exchange"
    # ...
    plugins:
      - equinixmetal-buildkite/identity-api#main:
          token-endpoint: https://iam.metalctrl.io/token # optional, any oauth2 token endpoint that supports rfc8693 token exchange
          debug: true                                    # optional, defaults to false
    # ...
```

## Claim Mappings

Claim mappings in the Identity API are defined [here](https://github.com/equinixmetal/infra9-tools/blob/77ea390a7d35086d51965d09fad654aaf5dedccf/data/prod/delivery.yaml#L126-L135)

| Identity API Claims | Buildkite Claims               |
|---------------------|---------------------------------|
| `sub`               | `organization_slug/pipeline_slug` |
| `upstream_sub`      | `sub`                          |
| `repository`        | `pipeline_slug`               |
| `ref`               | `pipeline_branch`             |
| `organization`      | `organization_slug`           |
| `job_id`            | `job_id`                      |
| `commit_sha`        | `commit`                      |
| `ci`                | `"buildkite"`                 |
