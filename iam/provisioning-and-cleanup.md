# Temporary Provisioning and Cleanup — Project 5

## 1. Purpose

The `HTI-P5-Provisioning` permission set was originally created to provide the temporary administrative permissions required to configure the Project 5 target IAM role.

The design separates temporary provisioning activities from the normal operational access workflow.

During the later External Google OIDC Federation extension, the same provisioning permission set was reused and augmented with additional narrowly scoped permissions required to configure the extension's dedicated federation role and associated policies.

The provisioning permission set was assigned to the `hti-lab-admin` user in the Cloud Security Lab account during configuration activities.

## 2. Provisioning Permission Set

| Setting | Value |
|---|---|
| Permission set | `HTI-P5-Provisioning` |
| Identity Center user | `hti-lab-admin` |
| Target account | Cloud Security Lab |
| Target account ID | Redacted in public copy |
| Session duration | 1 hour |
| Initial purpose | Configure the Project 5 target IAM role |
| Extension use | Configure the dedicated External Google OIDC Federation role and associated policies |

The permission set was not originally designed as a general-purpose administrative role. Its scope was expanded only as required by the documented lab extensions.

## 3. Provisioning Permissions

### Core Project 5

The original inline policy contained permissions grouped around the following tasks:

| Permission group | Purpose |
|---|---|
| `CreateOnlyProject5TargetRole` | Create the designated target role, with a condition requiring the SecurityAudit permissions boundary |
| `ManageOnlyProject5TargetRole` | Manage the designated role's inline policies and trust policy, and retrieve or delete the role |
| `ListRolesForConsoleNavigation` | Allow IAM role listing for console navigation |
| `ValidateIAMPolicies` | Validate IAM policies using IAM Access Analyzer |
| `ListManagedPoliciesForConsoleNavigation` | List managed policies for console navigation |
| `AttachSecurityAuditToProject5TargetRole` | Attach the SecurityAudit policy to the designated target role |
| `ListAttachedPoliciesForProject5TargetRole` | List attached policies for the target role |

These permissions were intended to limit provisioning activity to the Project 5 target role wherever the relevant IAM actions supported resource-level restrictions.

### External Google OIDC Federation Extension

For the extension, the same provisioning permission set was augmented with additional permissions needed to configure:

- The `HTI-P5-ExternalGoogleFederationRole`.
- Its restricted `sts:AssumeRoleWithWebIdentity` trust policy.
- The scoped S3 policies used by the extension.
- The required IAM policy objects and policy attachments.
- Validation and configuration of the external federation workflow.

The extension retained the same security principle: administrative permissions were added for specific configuration tasks rather than replacing the provisioning model with broad account administration.

## 4. Configuration Challenges

During core Project 5 provisioning:

- `iam:AttachRolePolicy` was initially denied.
- `access-analyzer:ListPolicyGenerations` was denied.

The provisioning policy was updated to include `iam:AttachRolePolicy` restricted to the target role ARN, together with `iam:ListAttachedRolePolicies` for policy inspection.

During the External Google OIDC Federation extension, additional IAM permissions were added as individual configuration requirements were encountered. These included permissions to manage the dedicated federation role and create or manage its scoped policies.

The `access-analyzer:ListPolicyGenerations` denial remained. It was not required for either the core cross-account role-assumption workflow or the external federation validation.

## 5. Cleanup and Access Lifecycle

### Core Project 5

After configuring the target role, the assignment of `HTI-P5-Provisioning` to `hti-lab-admin` in the Cloud Security Lab account was withdrawn.

The resulting assignment state showed `HTI-Lab-Security-Audit` remaining assigned to the user in the target account.

### Extension

The external federation configuration used the provisioning permission set during implementation. The dedicated federation role and its associated policies were then used by the external identity workflow itself; the provisioning permission set was not part of the runtime federation access path.

The provisioning permission-set definition was retained in Identity Center. Its assignment was withdrawn after the relevant provisioning work was completed.

## 6. Post-Cleanup Validation

The following core checks were performed after withdrawing the provisioning assignment:

| Test | Result |
|---|---|
| Provisioning assignment withdrawn | Successful |
| SecurityAudit assignment retained in target account | Verified |
| Source identity direct IAM role listing | Access denied as expected |
| Source identity assumption of target role | Successful |
| Target IAM Roles page accessed through assumed role | Successful |

For the External Google OIDC Federation extension, runtime access was separately validated through `AssumeRoleWithWebIdentity` using the dedicated federation role and scoped S3 permissions.

This confirms the distinction between **provisioning access** and the **runtime access paths** demonstrated by the two Project 5 components.

## 7. Security Significance

This approach demonstrates the value of separating administrative provisioning from ongoing operational access.

- Temporary provisioning permissions are used for configuration rather than retained as the normal access path.
- Core source access remains narrowly scoped to the designated STS role-assumption action.
- The core target role retains its SecurityAudit policy and permissions boundary.
- The extension uses a dedicated federation role rather than reusing the core cross-account target role.
- The external identity receives only the scoped permissions assigned to the federation role.
- Removing the provisioning assignment reduces the administrative permissions available to the human Identity Center user.

## 8. Lessons Learned

1. IAM provisioning often requires careful attention to both identity policies and permissions boundaries.
2. A provisioning permission set can be reused for related lab extensions when additional permissions are added deliberately and remain scoped to the configuration task.
3. Console navigation can require additional permissions that are separate from the core security operation.
4. Permission errors should be investigated individually rather than addressed by granting broad administrative access.
5. Withdrawing a permission-set assignment is different from deleting its definition.
6. Post-provisioning testing is necessary to confirm that intended runtime access paths still work.

## 9. Outcome

The core target role was configured successfully, the temporary provisioning assignment was withdrawn, and cross-account role assumption continued to work.

The same provisioning framework was subsequently reused and augmented for the External Google OIDC Federation extension, which established a separate external-identity runtime access path.

This demonstrates a controlled distinction between **administrative provisioning**, **core cross-account access**, and **external OIDC-federated access**.