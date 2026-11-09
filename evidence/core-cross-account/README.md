# Project 5 — Core AWS IAM Cross-Account Evidence

This folder contains the original redacted screenshots supporting the implementation and validation of the core AWS IAM cross-account access lab.

## Evidence coverage

The core evidence demonstrates:

- IAM Identity Center source-account assignment.
- Source-side least-privilege enforcement.
- Successful access to the target account through the designated IAM role.
- Target-role permissions and permissions-boundary configuration.
- The cross-account role trust relationship.
- IAM Policy Simulator results.

## Evidence files

| Evidence | Description |
|---|---|
| E01 | IAM Identity Center source-account assignment |
| E02 | Source-session `iam:ListRoles` denied |
| E03 | Target-role IAM session |
| E04 | Target-role `SecurityAudit` / Access Analyzer limitation |
| E05 | Target-role permissions boundary |
| E06 | Cross-account trust policy |
| E07 | IAM Policy Simulator results |

## Validation distinction

Some screenshots document live AWS console operations, while others show IAM Policy Simulator evaluations.

Simulator results are not evidence of live API execution. They should be interpreted as policy evaluation evidence rather than proof that a live API call was executed.

## Evidence handling

The published screenshots are redacted where necessary to remove AWS account identifiers, personal information and other sensitive data.

Never add credentials, access keys, secret keys, session tokens, recovery codes or other sensitive authentication information to this public repository.

The original core evidence has been preserved and moved into this dedicated folder without renumbering or altering the evidence files.
