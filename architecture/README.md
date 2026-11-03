# Project 5 Architecture — AWS IAM Cross-Account Access

## Architecture Overview

This architecture demonstrates controlled cross-account access between two AWS accounts using AWS IAM Identity Center, IAM permission sets, AWS Security Token Service (STS), and a dedicated IAM role.

The design applies least-privilege principles by separating the permissions required to request access from the permissions available within the target account.

## AWS Accounts

| Account | Purpose |
|---|---|
| Identity Delegated Admin | Source account used to initiate cross-account access |
| Cloud Security Lab | Target account containing the protected IAM role |

Numeric AWS account identifiers are intentionally redacted from this public repository.

The AWS IAM Identity Center administration is managed through a separate management account.

## Access Flow

1. The administrator signs in through the AWS IAM Identity Center access portal using the `hti-lab-admin` user.
2. The `HTI-P5-Source-Access` permission set provides narrowly scoped permission to call `sts:AssumeRole` on the designated target role.
3. AWS STS evaluates the source identity's permission and the target role's trust policy.
4. The target account's `HTI-P5-CrossAccount-TargetRole` is assumed.
5. The assumed role provides temporary credentials and access governed by its attached `SecurityAudit` policy and permissions boundary.

## Security Controls

- **Restricted source permissions:** The source permission set allows assumption of the designated target role rather than unrestricted IAM administration.
- **Cross-account trust:** The target role trusts the source AWS account.
- **Role-based access:** Access to the target account is provided through a dedicated IAM role.
- **Audit-oriented permissions:** The target role uses the AWS-managed `SecurityAudit` policy. Its effective permissions are also subject to the configured permissions boundary.
- **Permissions boundary:** The target role has a `SecurityAudit` permissions boundary to limit its maximum permissions.
- **Temporary credentials:** Role assumption uses AWS STS temporary credentials, with a maximum session duration of one hour.
- **Provisioning separation:** The temporary provisioning permission-set assignment was withdrawn after the target role was configured.

## Validation

The implementation was tested by:

- Successfully assuming the target role from the source account.
- Confirming that the source identity could not list IAM roles directly.
- Confirming access to the target account's IAM Roles page after role assumption.
- Using IAM Policy Simulator to verify that `iam:ListRoles` was allowed and `iam:CreateRole` was denied by the permissions boundary.
- Confirming that cross-account role assumption continued to work after withdrawing the temporary provisioning assignment.

Policy Simulator results are distinguished from live console tests; they are not presented as live API execution.

## Architecture Diagram

The accompanying diagram illustrates the account separation, Identity Center access, source-side permission restriction, STS role assumption, target role, and security controls.

## Design Principle

The central security principle is to grant the source identity only the permission needed to assume a specific trusted role, while allowing the target role's own policies and permissions boundary to govern what can be done after access is granted.
