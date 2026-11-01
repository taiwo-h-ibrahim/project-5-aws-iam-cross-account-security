# Source Access Policy — HTI-P5-Source-Access

## 1. Purpose

The `HTI-P5-Source-Access` permission set controls what the source identity can do when accessing the Project 5 cross-account environment.

Its purpose is to enforce least privilege by allowing the user to request access to one specific IAM role in the target AWS account, rather than granting broad IAM or administrative permissions.

## 2. Configuration

| Setting | Value |
|---|---|
| Permission set | `HTI-P5-Source-Access` |
| Identity Center user | `hti-lab-admin` |
| Source account | Identity Delegated Admin |
| Source account ID | Redacted in public copy |
| Target account | Cloud Security Lab |
| Target account ID | Redacted in public copy |
| Session duration | 1 hour |
| Allowed action | `sts:AssumeRole` |

## 3. Inline Policy

The following is a redacted illustrative version of the inline policy configured in the permission set. Replace `<TARGET_ACCOUNT_ID>` with the actual target account ID before using an equivalent policy in AWS; this public example is not directly deployable as written.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AssumeOnlyProject5TargetRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::<TARGET_ACCOUNT_ID>:role/HTI-P5-CrossAccount-TargetRole"
    }
  ]
}
```

## 4. Policy Analysis

The policy contains one statement:

- **Effect:** `Allow` — grants the specified action.
- **Action:** `sts:AssumeRole` — permits the identity to request assumption of an IAM role.
- **Resource:** The ARN of the specific Project 5 target role.

The resource restriction is important. The identity is not granted permission to assume arbitrary roles across AWS accounts.

The policy does not grant general IAM administration permissions such as `iam:ListRoles` or `iam:CreateRole`.

## 5. Validation and Test Results

The following tests were performed:

| Test | Result |
|---|---|
| Sign in through AWS IAM Identity Center | Successful |
| Access source account using the permission set | Successful |
| Attempt to list IAM roles directly in the source account | Access denied as expected |
| Assume `HTI-P5-CrossAccount-TargetRole` in the target account | Successful |

The source identity's inability to list IAM roles directly demonstrates that the permission set does not provide unrestricted IAM access.

The successful role assumption demonstrates that the narrowly scoped STS permission works with the target role's trust policy.

## 6. Security Significance

This implementation demonstrates three important IAM security principles:

1. **Least privilege:** Grant only the permissions required for the intended task.
2. **Resource-level restriction:** Limit role assumption to a specific trusted IAM role.
3. **Separation of access:** The source identity's permissions determine whether it can request role assumption; the target role's policies determine what it can do after assumption.

## 7. Outcome

The source access policy successfully enabled controlled cross-account access while preventing direct, unrestricted IAM role listing from the source account.

This provides the first layer of the Project 5 cross-account security design.
