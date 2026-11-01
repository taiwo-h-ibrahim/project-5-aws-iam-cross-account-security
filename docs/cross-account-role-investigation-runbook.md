# AWS Cross-Account Role Assumption — Investigation Runbook

## 1. Purpose

This runbook provides a structured procedure for investigating suspicious or unexpected cross-account IAM role-assumption activity in AWS.

It uses the Project 5 architecture as its reference environment:

- Source account: Identity Delegated Admin (`802429853711`)
- Target account: Cloud Security Lab (`875691018505`)
- Target role: `HTI-P5-CrossAccount-TargetRole`

The primary investigation source is AWS CloudTrail.

**Scenario:** An alert or security report indicates an unexpected `AssumeRole` event involving the Project 5 target role.

This is a simulated investigation scenario for portfolio and analyst training purposes. It is not a report of an actual security incident.

## 2. Investigation Objectives

The analyst should establish:

1. Which identity requested the role assumption?
2. Which AWS account initiated the request?
3. Which target role was requested?
4. When did the event occur?
5. Was the role assumption successful or unsuccessful?
6. Was the activity expected and authorized?
7. Were any subsequent actions suspicious?
8. What containment or remediation actions are appropriate?

## 3. Relevant AWS Services and Evidence

| Service or evidence | Investigation purpose |
|---|---|
| AWS CloudTrail | Review API activity and STS role-assumption events |
| IAM Identity Center | Verify user and permission-set assignments |
| AWS IAM | Inspect the target role, trust policy, and permissions |
| CloudTrail Event History | Search recent management events |
| STS event details | Identify role-assumption activity and session information |

CloudTrail Event History is useful for initial investigation. A production monitoring design should also consider centralized, retained CloudTrail logs.

## 4. Investigation Procedure

### Step 1 — Locate the AssumeRole Event

1. Sign in to the AWS Management Console with an appropriately authorized security-audit identity.
2. Open AWS CloudTrail.
3. Select **Event history**.
4. Confirm the AWS Region being investigated.
5. Filter by event name:

`AssumeRole`

6. Review matching events and identify the relevant target role.

**Evidence to record:**
- Event time
- Event name
- AWS Region
- Event ID
- Event source

**Expected event source:** `sts.amazonaws.com`

### Step 2 — Inspect the Event Details

Open the relevant event and inspect its JSON details.

Pay particular attention to:

| Field | Investigation purpose |
|---|---|
| `eventTime` | Establish when the request occurred |
| `eventName` | Confirm the API operation |
| `eventSource` | Identify the AWS service |
| `awsRegion` | Identify the Region |
| `userIdentity.type` | Identify the identity type |
| `userIdentity.accountId` | Identify the initiating account |
| `userIdentity.arn` | Identify the principal |
| `userIdentity.invokedBy` | Identify an AWS service when present |
| `requestParameters.roleArn` | Identify the requested target role |
| `requestParameters.roleSessionName` | Identify the requested session name |
| `sourceIPAddress` | Identify the recorded request source |
| `userAgent` | Identify the recorded client or service |
| `responseElements` | Inspect available successful response details |
| `errorCode` and `errorMessage` | Identify failures when present |

Not every field will be present in every CloudTrail event. Interpret fields in the context of the event rather than assuming they are always populated.

### Step 3 — Verify the Initiating Identity

Compare the event details with the expected Project 5 access design.

Expected source account:

`802429853711`

Expected target role:

`arn:aws:iam::875691018505:role/HTI-P5-CrossAccount-TargetRole`

Check whether the initiating principal and session information are consistent with the authorized Identity Center user and expected access workflow.

Where necessary, inspect IAM Identity Center assignments and the source permission set.

### Step 4 — Determine Whether the Role Assumption Succeeded

Inspect the event response and error fields.

- A successful `AssumeRole` event should contain successful response details, including temporary credential metadata where available.
- An unsuccessful request may contain an `errorCode` and `errorMessage`.

Do not treat the presence of an `AssumeRole` event alone as proof that access was granted.

### Step 5 — Review Subsequent Activity

If the role assumption succeeded, investigate subsequent actions associated with the assumed-role session.

Look for:

- Unexpected IAM policy changes.
- Attempts to create or modify IAM roles.
- Changes to trust policies.
- Unusual access to sensitive resources.
- Unexpected activity outside the intended security-audit purpose.

Correlate relevant CloudTrail events using the assumed-role session identity and available session context.

### Step 6 — Assess the Activity

Classify the activity based on available evidence.

| Classification | Indicators |
|---|---|
| Expected | Authorized identity, expected source account, designated target role, and legitimate purpose |
| Suspicious | Unexpected identity, unusual source, unexpected timing, or activity inconsistent with the approved workflow |
| Unauthorized or confirmed malicious | Strong evidence of unauthorized access or malicious activity |

An unusual event is not automatically malicious. Validate the identity, business context, and subsequent actions before reaching a conclusion.

## 5. Response and Containment

If the activity is confirmed as unauthorized or malicious:

1. Preserve the relevant CloudTrail event details and associated evidence.
2. Notify the appropriate security or cloud operations team.
3. Review and, where justified, revoke the affected user's access through IAM Identity Center.
4. Review the target role's trust policy and source-side permission set.
5. Remove unauthorized permissions or trust relationships.
6. Investigate other activity associated with the affected identity or session.
7. Rotate or revoke affected credentials where applicable, using the appropriate AWS response procedure.
8. Verify that unauthorized access is no longer possible.

Containment decisions should consider the operational impact of disabling legitimate access.

## 6. Recovery and Verification

After remediation:

- Verify that unauthorized permission assignments have been removed.
- Reinspect the target role's trust policy.
- Confirm that only approved identities can assume the role.
- Review subsequent CloudTrail events for further suspicious activity.
- Document the remediation and verification results.

## 7. Investigation Record Template

Use the following template when recording an investigation:

| Field | Analyst entry |
|---|---|
| Case or alert reference | |
| Investigation date | |
| Analyst | |
| Event time | |
| Event ID | |
| Source account | |
| Initiating identity | |
| Target role ARN | |
| Source IP address | |
| User agent | |
| AssumeRole outcome | |
| Subsequent activity reviewed | |
| Findings | |
| Severity assessment | |
| Containment actions | |
| Verification outcome | |
| Final disposition | |

## 8. Lessons for Cloud SOC Analysts

This investigation demonstrates why monitoring STS activity is important in AWS environments.

Cross-account role assumption is a legitimate administrative capability, but it can also be abused if source permissions or target trust relationships are misconfigured or compromised.

A cloud SOC analyst should therefore:

- Monitor `AssumeRole` activity.
- Validate source identities and target roles.
- Correlate role assumptions with subsequent API activity.
- Distinguish successful access from denied requests.
- Preserve evidence before taking containment actions.
- Verify remediation rather than assuming that a configuration change resolved the issue.

## 9. Scope and Limitations

This runbook describes a proposed investigation workflow using the Project 5 architecture and AWS CloudTrail.

The Project 5 lab validated cross-account role assumption and inspected CloudTrail event history. It did not execute a complete incident-response exercise involving a confirmed malicious role assumption.

The containment and recovery steps are operational recommendations, not actions claimed to have been performed during the lab.

## 10. Conclusion

The ability to identify, investigate, and respond to unexpected cross-account role assumptions is an important cloud security and SOC capability.

This runbook extends the Project 5 IAM implementation into a practical investigation workflow that can be developed further in a future cloud detection and monitoring project.
