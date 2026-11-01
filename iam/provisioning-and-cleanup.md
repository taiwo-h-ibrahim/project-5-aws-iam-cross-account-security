# Temporary Provisioning and Cleanup — Project 5

## 1. Purpose

The `HTI-P5-Provisioning` permission set was created to provide the administrative permissions required to configure the Project 5 target IAM role.

The design separates temporary role-provisioning activities from the normal cross-account access workflow.

The provisioning permission set was assigned to the `hti-lab-admin` user in the Cloud Security Lab account during configuration.

## 2. Provisioning Permission Set

| Setting | Value |
|---|---|
| Permission set | `HTI-P5-Provisioning` |
| Identity Center user | `hti-lab-admin` |
| Target account | Cloud Security Lab |
| Target account ID | Redacted in public copy |
| Session duration | 1 hour |
| Purpose | Configure the Project 5 target IAM role |

## 3. Provisioning Permissions

The inline policy contained permissions grouped around the following tasks:

| Permission group | Purpose |
|---|---|
| `CreateOnlyProject5TargetRole` | Create the designated target role, with a condition requiring the SecurityAudit permissions boundary |
| `ManageOnlyProject5TargetRole` | Manage the designated role's inline policies and trust policy, and retrieve or delete the role |
| `ListRolesForConsoleNavigation` | Allow IAM role listing for console navigation |
| `ValidateIAMPolicies` | Validate IAM policies using IAM Access Analyzer |
| `ListManagedPoliciesForConsoleNavigation` | List managed policies for console navigation |
| `AttachSecurityAuditToProject5TargetRole` | Attach the SecurityAudit policy to the designated target role |
| `ListAttachedPoliciesForProject5TargetRole` | List attached policies for the target role |

The permissions were intended to limit provisioning activity to the Project 5 target role wherever the relevant IAM actions supported resource-level restrictions.

## 4. Configuration Challenges

During provisioning, two permission errors were encountered:

- `iam:AttachRolePolicy` was initially denied.
- `access-analyzer:ListPolicyGenerations` was denied.

The provisioning policy was updated to include `iam:AttachRolePolicy` restricted to the target role ARN, together with `iam:ListAttachedRolePolicies` for policy inspection.

The SecurityAudit policy was subsequently attached successfully, and the SecurityAudit permissions boundary was verified.

The `access-analyzer:ListPolicyGenerations` denial remained. It was not required for the core cross-account role-assumption workflow.

## 5. Cleanup Procedure

After configuring the target role, the assignment of `HTI-P5-Provisioning` to `hti-lab-admin` in the Cloud Security Lab account was withdrawn.

The Identity Center assignment update completed successfully.

The resulting assignment state showed `HTI-Lab-Security-Audit` remaining assigned to the user in the target account.

The provisioning permission-set definition itself was retained in Identity Center. Its assignment was withdrawn; the permission-set definition was not deleted.

## 6. Post-Cleanup Validation

The following checks were performed after withdrawing the provisioning assignment:

| Test | Result |
|---|---|
| Provisioning assignment withdrawn | Successful |
| SecurityAudit assignment retained in target account | Verified |
| Source identity direct IAM role listing | Access denied as expected |
| Source identity assumption of target role | Successful |
| Target IAM Roles page accessed through assumed role | Successful |

This confirmed that the normal cross-account access workflow continued to operate after the temporary provisioning assignment was withdrawn.

## 7. Security Significance

This approach demonstrates the value of separating administrative provisioning from ongoing operational access.

- Temporary provisioning permissions are used for configuration rather than retained as the normal access path.
- The source permission set remains narrowly scoped to the designated STS role-assumption action.
- The target role retains its SecurityAudit policy and permissions boundary.
- Removing the provisioning assignment reduces the administrative permissions available to the user in the target account.

## 8. Lessons Learned

1. IAM provisioning often requires careful attention to both identity policies and permissions boundaries.
2. Console navigation can require additional permissions that are separate from the core security operation.
3. Permission errors should be investigated individually rather than addressed by granting broad administrative access.
4. Withdrawing a permission-set assignment is different from deleting its definition.
5. Post-cleanup testing is necessary to confirm that the intended operational access path still works.

## 9. Outcome

The target role was configured successfully, the temporary provisioning assignment was withdrawn, and cross-account role assumption continued to work.

This demonstrates a separation between role configuration privileges and the normal least-privilege access workflow.
