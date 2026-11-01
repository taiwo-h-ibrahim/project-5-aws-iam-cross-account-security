# Target Role Trust Policy and Security Controls

## 1. Purpose

The `HTI-P5-CrossAccount-TargetRole` IAM role is the target of the Project 5 cross-account access design.

It resides in the Cloud Security Lab account and allows the designated source AWS account to assume the role through AWS Security Token Service (STS).

## 2. Role Configuration

| Setting | Value |
|---|---|
| Role name | `HTI-P5-CrossAccount-TargetRole` |
| Target account | Cloud Security Lab |
| Target account ID | `875691018505` |
| Source account ID | `802429853711` |
| Trusted principal | `arn:aws:iam::802429853711:root` |
| Allowed action | `sts:AssumeRole` |
| Attached permissions policy | AWS-managed `SecurityAudit` |
| Permissions boundary | AWS-managed `SecurityAudit` |
| Maximum session duration | 1 hour |

## 3. Trust Policy

The target role uses the following trust policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::802429853711:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### Trust Policy Analysis

- **Principal:** The source AWS account, `802429853711`.
- **Action:** `sts:AssumeRole`.
- **Effect:** `Allow`.

The account principal establishes which AWS account is trusted to request role assumption. It does not independently grant every identity in that account permission to assume the role.

The source identity must also have an identity-based policy allowing `sts:AssumeRole` on this specific target role. This is provided by the `HTI-P5-Source-Access` permission set.

## 4. Permissions Policy — SecurityAudit

The target role has the AWS-managed `SecurityAudit` policy attached.

This policy provides permissions for reviewing and inspecting AWS resources and security configurations. It is intended for security auditing rather than unrestricted administration.

## 5. Permissions Boundary — SecurityAudit

The same AWS-managed `SecurityAudit` policy is configured as the role's permissions boundary.

A permissions boundary establishes the maximum permissions that an identity-based policy can grant to the role. It does not independently grant permissions.

In this lab, the boundary was used to demonstrate that an otherwise permitted action can still be denied when it falls outside the boundary.

## 6. Validation

The following checks were performed:

| Validation | Result |
|---|---|
| Target role created in Cloud Security Lab | Verified |
| Source account configured as trusted principal | Verified |
| `sts:AssumeRole` trust action configured | Verified |
| AWS-managed `SecurityAudit` policy attached | Verified |
| `SecurityAudit` permissions boundary configured | Verified |
| Maximum session duration set to one hour | Verified |
| Cross-account role assumption from source account | Successful |
| Target IAM Roles page accessed after assumption | Successful |

The IAM Policy Simulator also showed:

- `iam:ListRoles` — Allowed.
- `iam:CreateRole` — Denied by the permissions boundary.

These simulator results are not represented as live API execution tests.

## 7. Security Significance

The target role demonstrates the use of multiple IAM controls working together:

1. **Cross-account trust:** Defines the AWS account permitted to request access.
2. **Source identity authorization:** Restricts which role the source identity can assume.
3. **Role permissions:** Defines the target role's permitted security-audit activities.
4. **Permissions boundary:** Limits the maximum permissions available to the role.
5. **Temporary credentials:** Uses STS sessions rather than permanent cross-account credentials.

## 8. Outcome

The target role successfully provided controlled access to the Cloud Security Lab account from the Identity Delegated Admin account.

The combination of a restricted source permission set, a specific target trust relationship, SecurityAudit permissions, and a permissions boundary demonstrates a practical least-privilege cross-account access design.
