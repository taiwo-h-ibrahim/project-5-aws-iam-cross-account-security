# Project 5 — Evidence Register

This register identifies the redacted screenshots published in this folder and explains what each one demonstrates.

| ID | Screenshot | Evidence description | Supports | Validation type |
|---|---|---|---|---|
| E01 | `E01-identity-center-source-assignment-redacted.png` | IAM Identity Center source permission-set assignment | Assignment of `HTI-P5-Source-Access` | Live AWS console evidence |
| E02 | `E02-source-session-iam-listroles-denied-redacted.png` | Source session receives AccessDenied when attempting `iam:ListRoles` | Source-side least-privilege restriction | Live AWS console evidence |
| E03 | `E03-target-role-session-iam-roles-redacted.png` | Target IAM Roles page accessed through the assumed target role | Successful cross-account role assumption | Live AWS console evidence |
| E04 | `E04-target-role-securityaudit-accessanalyzer-denial-redacted.png` | Target role configuration and observed Access Analyzer permission denial | SecurityAudit attachment and observed permission limitation | Live AWS console evidence |
| E05 | `E05-target-role-permissions-boundary-redacted.png` | Target role permissions-boundary configuration | Use of the SecurityAudit permissions boundary | Live AWS console evidence |
| E06 | `E06-cross-account-trust-policy-redacted.png` | Target role trust policy | Cross-account trust relationship with the source account | Live AWS console evidence |
| E07 | `E07-iam-policy-simulator-results-redacted.png` | Policy Simulator allows `iam:ListRoles` and denies `iam:CreateRole` | Simulator-based permissions and boundary evaluation | IAM Policy Simulator evidence |

## Evidence interpretation

- E01–E06 document AWS console configuration or observed operations.
- E07 documents IAM Policy Simulator results only. It must not be interpreted as proof that either action was executed against the live AWS environment.
- The Access Analyzer denial shown in E04 is an observed permission limitation. It remained unresolved during the lab and did not prevent successful cross-account role assumption.

## Publication and security

The published screenshots were redacted to remove AWS account identifiers and personal information where visible.

No passwords, access keys, secret keys, session tokens, or recovery codes should be published in this repository.
