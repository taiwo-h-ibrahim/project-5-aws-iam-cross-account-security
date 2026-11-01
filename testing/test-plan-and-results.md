# Project 5 — Test Plan and Results

## 1. Testing Objective

The objective of this test plan is to validate that the AWS IAM cross-account access design enforces least privilege while allowing the designated source identity to assume the target IAM role.

Testing focused on:

- Identity Center access and permission-set assignments.
- Source-side IAM restrictions.
- Target-role trust configuration.
- Cross-account role assumption through AWS STS.
- Target-role permissions and permissions boundary.
- Continued access after withdrawal of the temporary provisioning assignment.

## 2. Test Environment

| Component | Configuration |
|---|---|
| Identity Center user | `hti-lab-admin` |
| Source account | Identity Delegated Admin |
| Source account ID | Redacted in public copy |
| Target account | Cloud Security Lab |
| Target account ID | Redacted in public copy |
| Source permission set | `HTI-P5-Source-Access` |
| Target IAM role | `HTI-P5-CrossAccount-TargetRole` |
| Target permissions policy | AWS-managed `SecurityAudit` |
| Permissions boundary | AWS-managed `SecurityAudit` |
| Maximum role session | 1 hour |

## 3. Test Cases and Results

### Test 1 — Identity Center Access

**Objective:** Verify that the designated user can access the configured AWS accounts through IAM Identity Center.

**Procedure:** Sign in to the AWS access portal and inspect the account and permission-set assignments.

**Expected result:** The user can access the assigned accounts and permission sets.

**Observed result:** Successful. The source and target account assignments were available through the access portal.

**Status:** PASS

### Test 2 — Source Identity IAM Restriction

**Objective:** Verify that the source identity cannot directly list IAM roles.

**Procedure:** From the source account session using `HTI-P5-Source-Access`, attempt to access the IAM Roles page.

**Expected result:** The request is denied because the source permission set does not grant `iam:ListRoles`.

**Observed result:** AWS returned an AccessDenied response for `iam:ListRoles`.

**Status:** PASS

### Test 3 — Cross-Account Role Assumption

**Objective:** Verify that the source identity can assume the designated target role.

**Procedure:** Use the AWS console role-switching workflow to access `HTI-P5-CrossAccount-TargetRole` in the target account.

**Expected result:** The source identity successfully assumes the target role.

**Observed result:** Successful. The target account session was established and the IAM Roles page loaded.

**Status:** PASS

### Test 4 — Target Role Access

**Objective:** Verify that the assumed target role provides the intended security-audit access.

**Procedure:** Navigate to IAM Roles while operating under the assumed target-role session.

**Expected result:** The IAM Roles page is accessible.

**Observed result:** Successful. The target account's IAM Roles page displayed the available roles.

**Status:** PASS

### Test 5 — SecurityAudit Policy Attachment

**Objective:** Verify that the target role has the AWS-managed SecurityAudit policy attached.

**Procedure:** Inspect the target IAM role's permissions configuration.

**Expected result:** The SecurityAudit policy is attached.

**Observed result:** Verified in the target role configuration.

**Status:** PASS

### Test 6 — Permissions Boundary

**Objective:** Verify that the target role has the SecurityAudit permissions boundary configured.

**Procedure:** Inspect the target IAM role's permissions boundary.

**Expected result:** The AWS-managed SecurityAudit policy is configured as the permissions boundary.

**Observed result:** Verified in the target role configuration.

**Status:** PASS

### Test 7 — IAM Policy Simulator: iam:ListRoles

**Objective:** Check whether the target role's effective permissions allow IAM role listing.

**Procedure:** Evaluate `iam:ListRoles` using the IAM Policy Simulator.

**Expected result:** The action is allowed under the target role's SecurityAudit permissions.

**Observed result:** Allowed in IAM Policy Simulator.

**Status:** PASS

### Test 8 — IAM Policy Simulator: iam:CreateRole

**Objective:** Verify that the permissions boundary prevents the target role from creating IAM roles.

**Procedure:** Evaluate `iam:CreateRole` using the IAM Policy Simulator.

**Expected result:** The action is denied by the permissions boundary.

**Observed result:** Denied by the permissions boundary in IAM Policy Simulator.

**Status:** PASS

### Test 9 — Post-Provisioning Assignment Withdrawal

**Objective:** Verify that normal cross-account access continues after removing the temporary provisioning assignment.

**Procedure:** Withdraw the `HTI-P5-Provisioning` assignment from the user in the Cloud Security Lab account. Then test source-side access restrictions and attempt cross-account role assumption again.

**Expected result:** The temporary provisioning assignment is no longer available, but the source permission set can still assume the target role.

**Observed result:** The provisioning assignment was successfully withdrawn. Direct IAM role listing from the source session remained denied, while cross-account role assumption continued to work.

**Status:** PASS

## 4. Test Summary

| Test category | Result |
|---|---|
| Identity Center access | PASS |
| Source-side least privilege | PASS |
| Cross-account role assumption | PASS |
| Target-role access | PASS |
| SecurityAudit attachment | PASS |
| Permissions boundary | PASS |
| Policy Simulator — `iam:ListRoles` | PASS |
| Policy Simulator — `iam:CreateRole` | PASS |
| Post-provisioning cleanup validation | PASS |

**Overall result:** All documented test cases produced their expected outcomes.

## 5. Testing Limitations

The test results distinguish between live AWS console validation and policy simulation.

- Cross-account role assumption and console access were tested in the live AWS environment.
- Source-side `iam:ListRoles` denial was observed in the live console.
- The `iam:ListRoles` Allow and `iam:CreateRole` Deny results were obtained through IAM Policy Simulator. They were not live API execution tests.
- The Access Analyzer `ListPolicyGenerations` permission error encountered during the lab was not part of the core role-assumption validation.

## 6. Conclusion

The tests demonstrate that the Project 5 design successfully supports controlled cross-account access.

The source identity is restricted to assuming a designated target role, while the target role's SecurityAudit permissions and permissions boundary govern its effective access.

The successful post-cleanup test also demonstrates that the normal access workflow remains functional after withdrawing the temporary provisioning assignment.
