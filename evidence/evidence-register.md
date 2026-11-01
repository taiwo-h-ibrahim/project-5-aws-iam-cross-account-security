# Project 5 — Evidence Register

This register identifies the screenshots captured during the lab and the purpose each can serve in supporting the project documentation. The screenshots were supplied during the lab conversation; they are not included in this repository unless separately uploaded into this folder.

| ID | Evidence description | Supports | Publication guidance |
|---|---|---|---|
| E01 | IAM Identity Center permission-set list | Permission-set inventory | Redact account identifiers and unrelated users |
| E02 | Unused `HTI-P5-CrossAccount-Access` permission-set details | Retirement of unused permission-set definition | Redact tenant/account identifiers |
| E03 | `HTI-P5-Source-Access` permission-set details and inline policy | Source-side `sts:AssumeRole` restriction and one-hour session | Redact account IDs in the role ARN |
| E04 | `HTI-P5-Provisioning` permission-set details and inline policy | Scoped provisioning permissions and SecurityAudit boundary condition | Redact account IDs and unrelated identifiers |
| E05 | AWS access portal account and permission-set tiles | Identity Center access assignment | Redact account IDs, email addresses, and tenant-specific URLs |
| E06 | Target IAM Roles page showing the Project 5 role | Target-role existence | Redact account IDs and unrelated roles where appropriate |
| E07 | Identity Center target-account assignment view | User assignment to SecurityAudit and provisioning permission sets | Redact account and user identifiers as appropriate |
| E08 | Assignment update showing provisioning assignment withdrawn | Post-provisioning access cleanup | Redact account IDs and user identifiers |
| E09 | Source session showing AccessDenied for `iam:ListRoles` | Live source-side least-privilege validation | Redact account IDs, session identifiers, and unrelated details |
| E10 | Target role details showing SecurityAudit policy and permissions boundary | Target-role security configuration | Redact role ARN account ID |
| E11 | Target role session showing IAM Roles page access | Successful cross-account role assumption | Redact account IDs and unrelated role names |
| E12 | Delete confirmation and success notification for unused permission set | Cleanup of unused permission-set definition | Redact account and tenant identifiers |
| E13 | IAM Policy Simulator result allowing `iam:ListRoles` | Simulator-based permission evaluation | Label clearly as Policy Simulator evidence |
| E14 | IAM Policy Simulator result denying `iam:CreateRole` due to boundary | Simulator-based boundary validation | Label clearly as Policy Simulator evidence |
| E15 | Post-cleanup source restriction and successful target role switch | Continued cross-account access after provisioning assignment withdrawal | Redact account IDs, session identifiers, and unrelated details |

## Evidence status

The register is a documentation index. Upload only the original screenshots that remain available and that pass the redaction checks above. Do not recreate or alter evidence in a way that changes the underlying AWS result.
