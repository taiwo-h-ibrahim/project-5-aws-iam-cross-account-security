# Project 5 — Security Findings and Lessons Learned

## 1. Overview

This document records the security findings, implementation observations, and lessons learned during the AWS IAM Cross-Account Access and Least Privilege lab.

The project focused on restricting source identity permissions, establishing a trusted cross-account role relationship, enforcing permissions boundaries, and separating temporary provisioning privileges from normal access.

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

**Observation:** The `HTI-P5-Provisioning` permission-set assignment was withdrawn after the target role was configured.

**Evidence:** Identity Center confirmed the successful assignment withdrawal. Subsequent testing showed that the source identity could still assume the target role.

**Security significance:** The administrative permissions used for provisioning were separated from the normal cross-account access path.

**Assessment:** Control validated.

### Finding 5 — Access Analyzer Permission Limitation

**Observation:** An `access-analyzer:ListPolicyGenerations` AccessDenied error occurred during target-role testing.

**Impact:** This limited that particular Access Analyzer console operation but did not prevent the core cross-account role-assumption workflow.

**Assessment:** Observed limitation. It was not resolved as part of the core project because it was not required for the tested access workflow.

## 3. Implementation Challenges

### IAM Policy Attachment

The initial provisioning attempt to attach the SecurityAudit policy failed because `iam:AttachRolePolicy` was denied.

The provisioning permission set was updated to allow the attachment action on the designated target role ARN. The SecurityAudit policy was then attached successfully.

**Lesson:** IAM permissions should be added deliberately and scoped to the required resource wherever supported.

### Console Navigation Permissions

Some AWS console operations required additional permissions beyond the core provisioning actions.

This highlighted the difference between permissions required to perform a security operation and permissions required to navigate or inspect resources through the console.

**Lesson:** Console usability requirements should be considered without unnecessarily expanding the underlying security permissions.

## 4. Security Design Principles Demonstrated

The lab demonstrated the following principles:

1. **Least privilege:** Grant only the permissions needed for the intended operation.
2. **Separation of duties:** Separate temporary administrative provisioning from normal operational access.
3. **Explicit trust:** Establish a defined trust relationship between AWS accounts.
4. **Temporary access:** Use STS role sessions rather than permanent cross-account credentials.
5. **Permissions boundaries:** Limit the maximum permissions that can be granted to an IAM role.
6. **Validation:** Test both successful access and expected access-denial scenarios.
7. **Cleanup:** Withdraw temporary access assignments after configuration and verify that normal access still works.

## 5. Limitations and Scope

This project validates a specific cross-account IAM access design in a controlled AWS lab.

The results should not be interpreted as a full production AWS security assessment.

In particular:

- The target role's SecurityAudit policy and permissions boundary were validated, but this project did not implement a complete organization-wide IAM governance model.
- The `ListPolicyGenerations` Access Analyzer permission issue remained unresolved.
- IAM Policy Simulator results were not represented as live API execution.
- The lab did not establish that every possible unauthorized access path had been tested.

## 6. Recommendations

For a production implementation, consider the following improvements:

- Review and periodically validate cross-account trust relationships.
- Monitor STS `AssumeRole` activity through CloudTrail.
- Alert on unexpected role assumptions and unusual access patterns.
- Review permission sets and role policies regularly.
- Use short session durations appropriate to operational requirements.
- Remove unused permission sets and temporary access assignments.
- Apply additional organizational guardrails where appropriate.

## 7. Overall Conclusion

Project 5 successfully demonstrated controlled AWS cross-account access using IAM Identity Center, IAM permission sets, STS role assumption, a dedicated target role, and a permissions boundary.

The tests confirmed that the source identity was restricted, the target role could be assumed successfully, and the normal access workflow continued after the temporary provisioning assignment was withdrawn.

The project provides practical evidence of IAM least-privilege implementation, access separation, policy validation, and security-focused troubleshooting.
