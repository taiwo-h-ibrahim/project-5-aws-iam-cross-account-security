# Project 5 — AWS IAM Cross-Account Access & Least Privilege

**Author:** Taiwo H Ibrahim  
**Platform:** Amazon Web Services (AWS)  
**Services:** AWS IAM, IAM Identity Center, AWS Security Token Service (STS)  
**Focus:** Cross-account access, identity management, least privilege and access lifecycle management.

---

## 1. Project Overview

This project demonstrates the design, implementation and validation of a restricted cross-account access architecture using AWS IAM Identity Center and AWS IAM.

The objective was to allow an authorized identity in a source AWS account to assume a specifically designated IAM role in a separate target account, without granting unnecessary permissions.

The implementation uses:

- AWS IAM Identity Center for centralized identity and access management.
- Permission sets to assign controlled access to AWS accounts.
- An identity-based policy restricting `sts:AssumeRole` to a specific target role.
- An IAM role trust policy defining the trusted source account.
- AWS STS to establish temporary cross-account role sessions.
- AWS-managed `SecurityAudit` permissions for read-oriented security auditing.
- A permissions boundary to limit the target role's maximum permissions.

The project also demonstrates administrative access withdrawal after deployment and validation.

## 2. Business Problem

In a multi-account AWS environment, administrators and security analysts may need to inspect resources in accounts other than the one through which they initially authenticate.

Granting permanent administrative permissions or creating unnecessary IAM users increases the risk of excessive access and credential compromise.

The challenge was to establish a controlled mechanism through which an authorized identity could access a specific role in another AWS account, while limiting both the scope of access and the permissions available after assumption.

## 3. Security Objectives

1. Implement controlled access between two AWS accounts.
2. Use IAM Identity Center to manage human access.
3. Restrict source-side authorization to one designated target IAM role.
4. Configure a cross-account trust relationship.
5. Apply read-oriented auditing permissions to the target role.
6. Enforce a permissions boundary.
7. Validate successful and denied access scenarios.
8. Withdraw unnecessary provisioning access after deployment.

## 4. AWS Account Architecture

The implementation uses three account responsibilities:

| Account | Responsibility |
|---|---|
| Management account — `ibrahimabeeb-Team` | IAM Identity Center administration |
| Identity Delegated Admin | Source account |
| Cloud Security Lab | Target account |

Account identifiers and other sensitive information are intentionally excluded from this public overview.

### Identity and access components

**Human identity:** `hti-lab-admin`

**Source permission set:** `HTI-P5-Source-Access`

**Target IAM role:** `HTI-P5-CrossAccount-TargetRole`

**Target permissions policy:** AWS-managed `SecurityAudit`

**Target permissions boundary:** AWS-managed `SecurityAudit`

**Maximum target-role session duration:** One hour.

## 5. Architecture and Access Flow

The access process follows this sequence:

1. The administrator authenticates through AWS IAM Identity Center.
2. IAM Identity Center provides access to the Identity Delegated Admin source account through `HTI-P5-Source-Access`.
3. The source permission set authorizes `sts:AssumeRole` only for the designated target role ARN.
4. AWS STS evaluates the request against the source-side permissions and the target role's trust policy.
5. If the request is authorized, STS provides temporary credentials for the target role.
6. The administrator accesses Cloud Security Lab using `HTI-P5-CrossAccount-TargetRole`.
7. The target role's effective permissions are constrained by its attached permissions policy, permissions boundary and other applicable controls.

**Security distinction:**

- The source identity policy controls which target role can be requested.
- The target trust policy controls which principals are trusted to assume the role.
- The attached permissions policy defines the actions available to the assumed role.
- The permissions boundary limits the maximum permissions that can be granted to that role.

## 6. Implementation

### 6.1 IAM Identity Center

Created and configured the human identity and permission sets required for the exercise.

The source permission set, `HTI-P5-Source-Access`, contains a restricted inline policy authorizing `sts:AssumeRole` against the specific target role ARN.

### 6.2 Source-side authorization

The source policy follows the principle of least privilege by limiting role assumption to one designated resource.

The policy does not grant general IAM read access or administrative permissions in the source account.

### 6.3 Target IAM role

Created `HTI-P5-CrossAccount-TargetRole` in Cloud Security Lab.

Its trust policy trusts the Identity Delegated Admin source account.

The target role has:

- AWS-managed `SecurityAudit` attached as its permissions policy.
- AWS-managed `SecurityAudit` configured as its permissions boundary.
- A maximum session duration of one hour.

### 6.4 Provisioning controls

A separate permission set, `HTI-P5-Provisioning`, was used to create and manage the target role during implementation.

Its administrative permissions were scoped to the designated Project 5 target role where configured.

After provisioning and testing were completed, its assignment to `hti-lab-admin` in Cloud Security Lab was withdrawn. The permission-set definition was retained but no longer assigned to the user.

The unused `HTI-P5-CrossAccount-Access` permission-set definition was retired.

## 7. Testing and Validation

Testing included both live AWS console operations and IAM Policy Simulator checks.

| Test | Result |
|---|---|
| Source identity access through Identity Center | Successful |
| Source identity attempts `iam:ListRoles` | Access denied as expected |
| Cross-account target-role assumption | Successful |
| Target IAM Roles page access | Successful |
| IAM Policy Simulator — `iam:ListRoles` | Allowed |
| IAM Policy Simulator — `iam:CreateRole` | Denied by permissions boundary |
| Target role `SecurityAudit` attachment | Verified |
| Target role permissions boundary | Verified |
| Cross-account access after provisioning assignment withdrawal | Successful |

**Testing note:** Policy Simulator results are identified separately from live AWS console tests. Simulator outcomes are not presented as live API execution results.

## 8. Access Lifecycle Management

Following successful provisioning and testing:

- The `HTI-P5-Provisioning` assignment was removed from `hti-lab-admin`.
- Its permission-set definition was retained for possible future controlled provisioning.
- The unused `HTI-P5-CrossAccount-Access` permission-set definition was deleted.
- `HTI-P5-Source-Access` was retained.
- `HTI-Lab-Security-Audit` was retained.
- The target IAM role and its security configuration remained intact.

Cross-account role assumption was successfully tested again after the provisioning assignment was withdrawn.

This demonstrates separation of provisioning privileges from ongoing operational access.

## 9. Security Findings and Limitations

### Findings

- Source-side role assumption was restricted to a specific target role ARN.
- The target role used a separate trust policy to establish the cross-account trust relationship.
- The target role had an auditing-oriented permissions policy and a permissions boundary.
- Provisioning access was withdrawn after implementation.
- Cross-account access remained functional after cleanup.

### Observed limitation

The target role could not perform the `access-analyzer:ListPolicyGenerations` console operation because that action was not permitted by its identity-based permissions.

This was an expected limitation of the restricted access design, not a failure of cross-account role assumption.

### Scope limitation

This was a practical cloud security lab implementation, not a production AWS security assessment. The findings and conclusions are limited to the configurations and tests performed.

## 10. Project Outcome

The project successfully demonstrated:

- AWS IAM Identity Center permission-set administration.
- Restricted cross-account role assumption using AWS STS.
- IAM trust relationships.
- Resource-scoped authorization.
- Permissions boundaries and least privilege.
- Live access validation and policy simulation.
- Withdrawal of unnecessary provisioning access.
- Verification of continued access after administrative cleanup.

The exercise strengthened practical understanding of AWS identity architecture, authorization evaluation and access lifecycle management.

## 11. Repository Contents

Project documentation and supporting evidence are organized into:

- `architecture/` — architecture diagrams and design explanation.
- `iam/` — relevant IAM policy documents and configuration notes.
- `evidence/` — redacted screenshots and implementation evidence.
- `testing/` — test plan and validation results.
- `findings/` — security review and observations.
- `docs/` — operational runbook and supporting documentation.

---

**Author:** Taiwo H Ibrahim  
**GitHub:** [taiwo-h-ibrahim](https://github.com/taiwo-h-ibrahim)
