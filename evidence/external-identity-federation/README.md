# Project 5 External Identity Federation Evidence

This folder contains the redacted evidence supporting the External Google OIDC Federation extension to Project 5.

The extension is intentionally separated from the core cross-account evidence so that the original Project 5 evidence remains intact and the additional exercise can be reviewed independently.

## Evidence scope

The evidence demonstrates:

- External Google identity and OAuth/OIDC configuration.
- The dedicated `HTI-P5-ExternalGoogleFederationRole` trust relationship.
- OIDC token claims used for federation validation.
- Successful `AssumeRoleWithWebIdentity` and temporary STS credentials.
- Confirmation of the federated AWS caller identity.
- Scoped S3 permissions.
- Successful S3 read operations within the permitted scope.
- Expected denial of S3 write and delete operations.
- CloudTrail evidence of the external federation event.

## Provisioning context

The `HTI-P5-Provisioning` permission set was originally created for core Project 5 role provisioning. During this extension it was reused and augmented with narrowly scoped permissions required to configure the dedicated external federation role and its policies.

It was **not** used as the runtime access path for the external identity. Runtime access was provided through `HTI-P5-ExternalGoogleFederationRole`.

## Evidence register

| Evidence | Purpose |
|---|---|
| `P5-EXT-E01` | Google OAuth/OIDC client and external identity configuration |
| `P5-EXT-E02` | AWS external federation role trust policy |
| `P5-EXT-E03` | OIDC token claims used for federation validation |
| `P5-EXT-E04` | Successful `AssumeRoleWithWebIdentity` |
| `P5-EXT-E05` | Federated STS caller identity |
| `P5-EXT-E06` | Scoped S3 policy configuration |
| `P5-EXT-E07` | Successful scoped S3 prefix listing |
| `P5-EXT-E08` | Successful S3 object read |
| `P5-EXT-E09` | Denied S3 object write |
| `P5-EXT-E10` | Denied S3 object delete |
| `P5-EXT-E11` | CloudTrail `AssumeRoleWithWebIdentity` event |

## Evidence handling

All published evidence should remain redacted. Do not add OAuth tokens, AWS access keys, secret keys, session tokens, recovery codes, or other sensitive authentication material to the repository.