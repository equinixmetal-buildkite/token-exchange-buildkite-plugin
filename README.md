# Token Exchange Plugin for Buildkite

This Buildkite plugin provides a way to exchange tokens with any OAuth2 token endpoint
that supports [RFC 8693](https://datatracker.ietf.org/doc/html/rfc8693) token exchange.

This is particularly useful for CI pipelines that need to obtain a federated identity
for authentication with external services.

For example, imagine there's a CI
pipeline that needs to access some external resources, such as an AWS S3 bucket or
a Kubernetes cluster, instead of storing a long-lived client secret or access token,
the pipeline can use this plugin to exchange a short-lived OIDC token
(issued by Buildkite) for a federated
identity token (issued by the external service's OIDC provider).
This token can then be used to authenticate with the external service
and perform the necessary actions.

Upon successful exchange, the plugin will set a new environment variable
`FEDERATED_ACCESS_TOKEN` with the value of the exchanged token.

## Requirements

Before using this plugin, you first need to configure your external service
to trust the Buildkite OIDC provider.  Examples of this can be found:

1. [AWS OIDC federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html)
1. [GCP Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation)
1. [Equinix Delivery Identity API (internal link)](https://github.com/equinixmetal/infra9-tools/blob/c3c378077bb2259c988618665325372966b47642/data/prod/delivery.yaml#L128-L143)

In addition, you need to obtain the token endpoint URL for the external service's OIDC provider.
This is typically can be found in the providers' OIDC discovery endpoint, which is usually
located at `https://<provider>/.well-known/openid-configuration`.

## Usage

```yaml
steps:
  - label: "token-exchange"
    # ...
    plugins:
      - equinixmetal-buildkite/token-exchange#main:
          # required, any oauth2 token endpoint that supports rfc8693 token exchange
          token-endpoint: https://my.issuer.com/token
          # optional, audience for the OIDC token issued by Buildkite, defaults to identity-api
          audience: identity-api
          # optional, defaults to false
          debug: true

    # ...
    commands: |
      #!/bin/bash

      # use the federated access token to access a protected external resource
      curl -X GET \
        -H "Authorization: Bearer ${FEDERATED_ACCESS_TOKEN}" \
        https://api.example.com/my-super-secret-resource


    # ...
```
