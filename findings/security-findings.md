# Project 5 — Security Findings and Lessons Learned

## 1. Overview

This document records the security findings, implementation observations, and lessons learned during the AWS IAM Cross-Account Access and Least Privilege lab and its External Google OIDC Federation extension.

The core project focused on restricting source identity permissions, establishing a trusted cross-account role relationship, enforcing permissions boundaries, and separating temporary provisioning privileges from normal access.

The extension applied similar least-privilege principles to an external identity by using OIDC federation, a dedicated IAM role, scoped S3 permissions, and CloudTrail validation.

## 2. Key Security Findings

### Finding 1 — Source Identity Successfully Restricted

**Observation:** The `HTI-P5-Source-Access` permission set allowed only `sts:AssumeRole` on the designated target role.

**Evidence:** An attempt to access IAM Roles directly from the source account returned an `AccessDenied` response for `iam:ListRoles`.

**Security significance:** The source identity could request the intended cross-account access without receiving unrestricted IAM permissions in its own account.

**Assessment:** Control validated.

### Finding 2 — Cross-Account Trust Worked as Intended

**Observation:** The target role trusted the source AWS account, and the source identity had permission to assume that specific role.

**Evidence:** The role-switching workflow successfully established a session in the target account.

**Security significance:** The source identity's permission and the target role's trust policy worked together to enable controlled cross-account access.

**Assessment:** Control validated.

### Finding 3 — Permissions Boundary Restricted Role Capabilities

**Observation:** The target role had the AWS-managed SecurityAudit policy attached and the same policy configured as its permissions boundary.

**Evidence:** IAM Policy Simulator allowed `iam:ListRoles` but denied `iam:CreateRole` because of the permissions boundary.

**Security significance:** The boundary demonstrated how maximum-permission restrictions can prevent an identity-based policy from granting an action outside the permitted scope.

**Assessment:** Validated through IAM Policy Simulator.

### Finding 4 — Temporary Provisioning Access Was Separated

**Observation:** The `HTI-P5-Provisioning` permission-set assignment was withdrawn after the core target role was configured and later reused during the external federation extension only for provisioning tasks.

**Evidence:** Identity Center confirmed the successful assignment withdrawal. Core post-cleanup testing showed that the source identity could still assume the target role. The extension's runtime tests used the dedicated `HTI-P5-ExternalGoogleFederationRole`, not the provisioning permission set.

**Security significance:** Administrative provisioning privileges were separated from the normal runtime access paths demonstrated by both project components.

**Assessment:** Control validated.

### Finding 5 — External Google OIDC Federation Was Restricted

**Observation:** The extension used a dedicated IAM role with an OIDC trust relationship restricted to the intended Google identity and OAuth client audience.

**Evidence:** `AssumeRoleWithWebIdentity` succeeded and returned a federated STS session for `HTI-P5-ExternalGoogleFederationRole`.

**Security significance:** The external identity could obtain temporary AWS credentials without requiring a permanent IAM user or access to the existing core cross-account role.

**Assessment:** Control validated.

### Finding 6 — Federated S3 Access Was Scoped

**Observation:** The external federation role was granted specific S3 read permissions, including access to the permitted prefix and test object.

**Evidence:** S3 prefix listing and `GetObject` succeeded, while `PutObject` and `DeleteObject` tests returned `AccessDenied`.

**Security significance:** The tests demonstrated that authentication through federation did not imply unrestricted resource access.

**Assessment:** Control validated.

### Finding 7 — CloudTrail Captured the Federation Event

**Observation:** CloudTrail Event History recorded the `AssumeRoleWithWebIdentity` event in the Cloud Security Lab account.

**Evidence:** The event identified `sts.amazonaws.com`, the `accounts.google.com` web identity provider, the Google subject, and the dedicated federation role.

**Security significance:** Federated identity activity can be monitored through AWS audit logging.

**Assessment:** Control validated.

### Finding 8 — Access Analyzer Permission Limitation

**Observation:** An `access-analyzer:ListPolicyGenerations` AccessDenied error occurred during target-role testing.

**Impact:** This limited that particular Access Analyzer console operation but did not prevent the core cross-account role-assumption workflow.

**Assessment:** Observed limitation. It was not resolved as part of the core project because it was not required for the tested access workflow.

## 3. Implementation Challenges

### IAM Policy Attachment

The initial provisioning attempt to attach the SecurityAudit policy failed because `iam:AttachRolePolicy` was denied.

The provisioning permission set was updated to allow the attachment action on the designated target role ARN. The SecurityAudit policy was then attached successfully.

**Lesson:** IAM permissions should be added deliberately and scoped to the required resource wherever supported.

### External Federation Provisioning

The External Google OIDC Federation extension reused `HTI-P5-Provisioning` rather than creating a second broad administrative permission set.

Additional IAM permissions were added incrementally as specific configuration requirements were encountered, including management of the dedicated federation role and creation or management of its scoped policies.

**Lesson:** Reusing a provisioning role can reduce duplication when its additional permissions remain deliberately scoped and its assignment is not used as the runtime access path.

### Console Navigation Permissions

Some AWS console operations required additional permissions beyond the core provisioning actions.

This highlighted the difference between permissions required to perform a security operation and permissions required to navigate or inspect resources through the console.

**Lesson:** Console usability requirements should be considered without unnecessarily expanding the underlying security permissions.

## 4. Security Design Principles Demonstrated

The lab demonstrated the following principles:

1. **Least privilege:** Grant only the permissions needed for the intended operation.
2. **Separation of duties:** Separate temporary administrative provisioning from normal operational access.
3. **Explicit trust:** Establish defined trust relationships for both AWS account access and external OIDC federation.
4. **Temporary access:** Use STS role sessions rather than permanent cross-account or external IAM credentials.
5. **Permissions boundaries:** Limit the maximum permissions that can be granted to the core IAM role.
6. **Scoped authorization:** Restrict the external federation role to the required S3 resources and actions.
7. **Validation:** Test both successful access and expected access-denial scenarios.
8. **Auditability:** Validate external federation through CloudTrail.
9. **Cleanup:** Withdraw temporary access assignments after configuration and verify that runtime access still works.

## 5. Limitations and Scope

This project validates specific IAM and federation designs in a controlled AWS lab.

The results should not be interpreted as a full production AWS security assessment.

In particular:

- The core target role's SecurityAudit policy and permissions boundary were validated, but the project did not implement a complete organization-wide IAM governance model.
- The `ListPolicyGenerations` Access Analyzer permission issue remained unresolved.
- IAM Policy Simulator results were not represented as live API execution.
- The external federation tests covered the documented S3 read, write-denial and delete-denial scenarios; they did not establish that every possible unauthorized access path had been tested.
- The CloudTrail validation was performed in the target Cloud Security Lab account's Event History.

## 6. Recommendations

For a production implementation, consider the following improvements:

- Review and periodically validate cross-account trust relationships.
- Monitor STS `AssumeRole` and `AssumeRoleWithWebIdentity` activity through CloudTrail.
- Alert on unexpected role assumptions and unusual federated access patterns.
- Review permission sets, OIDC trust policies and role policies regularly.
- Use short session durations appropriate to operational requirements.
- Remove unused permission sets and temporary access assignments.
- Apply additional organizational guardrails where appropriate.

## 7. Overall Conclusion

Project 5 successfully demonstrated controlled AWS identity access through two complementary patterns: cross-account role assumption for an internal identity and OIDC federation for an external Google identity.

The core and extension both applied least-privilege principles, explicit trust relationships, temporary credentials, scoped permissions, access-denial validation, and access lifecycle controls.

The project provides practical evidence of AWS IAM architecture, federation, authorization evaluation, security-focused troubleshooting, and auditable identity access.