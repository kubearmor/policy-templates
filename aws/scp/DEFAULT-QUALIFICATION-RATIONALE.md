# Default SCP qualification rationale

## Purpose

This document explains why some security-oriented SCPs are suitable for the
default customer baseline while others should be offered only after customer
configuration and testing. It is intended to support product and policy-catalog
decisions rather than claim that an excluded policy has no security value.

## Qualification standard

A policy qualifies for the default baseline when it:

1. Prevents a clear, high-impact security or governance failure.
2. Applies consistently across ordinary AWS Organizations member accounts.
3. Requires no customer-specific account IDs, role names, Regions, tags, VPCs,
   VPC endpoints, KMS keys, or other environment values.
4. Does not assume a particular identity, network, deployment, or operating model.
5. Has a low likelihood of blocking normal application and administrative work.
6. Can be understood and tested without collecting workload behavioral data.

Passing this standard does not eliminate the need for staged deployment and
testing. An SCP is an explicit permission boundary and its denies cannot be
overridden by an administrator policy in an affected member account.

## Qualifying example: Prevent member-account closure

Source workbook policy: `SCP-A004: Prevent Account Closure and Contact
Manipulation`.

The complete workbook policy combines account closure, contact administration,
and Region administration. Only its member-account closure control qualifies for
the universal baseline, so the implementation is deliberately narrower:

```yaml
Version: "2012-10-17"
Statement:
  - Sid: DenyMemberAccountClosure
    Effect: Deny
    Action:
      - account:CloseAccount
    Resource: "*"
```

### Why it qualifies

- **Clear high-impact risk:** Closing an AWS account makes its workloads and data
  unavailable and begins a process after which its remaining resources are
  permanently deleted.
- **Universal scope:** Every governed member account is subject to the same risk;
  the policy does not depend on which AWS services the customer runs.
- **No environment parameters:** It contains no role names, account IDs, Regions,
  or resource names.
- **Low routine impact:** Application teams do not need `account:CloseAccount` for
  ordinary deployment or operations.
- **Central lifecycle remains possible:** SCPs do not restrict principals in the
  organization management account. Account retirement can therefore remain a
  deliberate, centrally governed process.
- **Direct AWS recommendation:** AWS recommends denying both member-account
  departure and self-closure at the organization root.

### Product decision

Include `prevent-account-closure.yaml` in the default baseline. Do not include the
workbook's contact and Region restrictions in the same default policy: customers
have different ownership models for account contacts and Region activation.

References:

- [AWS Organizations management-account best practices](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices_mgmt-acct.html)
- [Closing member accounts in AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_close.html)

## Non-qualifying example: Require VPC endpoints for S3

Source workbook policy: `SCP-C002: Require VPC Endpoints for S3`.

This policy denies S3 object operations unless requests arrive through a list of
approved VPC endpoints. It is a valid data-perimeter hardening pattern, but it is
not a safe universal default.

### Why it does not qualify

- **Customer-specific configuration:** `aws:SourceVpce` must be compared with real
  endpoint IDs such as `vpce-...`. Those identifiers differ by customer, account,
  Region, and network architecture.
- **Assumes a network model:** Some customers access S3 through gateway endpoints,
  interface endpoints, corporate networks, public AWS endpoints, or combinations
  of these. The SCP assumes the required endpoints already exist everywhere they
  are needed.
- **Can block normal access:** The `aws:SourceVpce` key exists only when a request
  uses a VPC endpoint. Requests from the standard AWS Management Console or public
  S3 endpoint do not satisfy that network condition. AWS explicitly notes that a
  policy restricted to a VPC endpoint can block console access.
- **Managed-service integration risk:** Network-origin context can be removed when
  an AWS service calls another service on the customer's behalf. A safe design must
  account for service principals and forward-access-session keys such as
  `aws:ViaAWSService` or `aws:CalledVia`.
- **Migration dependency:** Applying the deny before endpoints, DNS, routing,
  endpoint policies, and exception paths are verified can cause an immediate S3
  outage for applications, backup tools, analytics jobs, and administrators.
- **Behavior and architecture discovery is required:** The approved endpoint set
  and necessary service integrations cannot be inferred universally. They must be
  established from the customer's intended architecture and validated access paths.

### Product decision

Do not include this policy in the default baseline or general metadata catalog of
universally recommended SCPs. Offer it as an opt-in data-perimeter control after:

1. The customer supplies the approved VPC endpoint or endpoint-owner scope.
2. Required S3 access paths and AWS managed-service integrations are inventoried.
3. Service-principal and forward-access-session behavior is handled safely.
4. The control is tested in a representative non-production OU.
5. A recovery path is documented before broader attachment.

References:

- [IAM global condition key: `aws:SourceVpce`](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)
- [Amazon S3 gateway endpoints and endpoint-restricted access](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-s3.html)

## Summary for product decisions

| Policy | Security hardening | Universal default | Decision |
| --- | --- | --- | --- |
| Prevent member-account closure | Yes | Yes | Include in default baseline |
| Require VPC endpoints for S3 | Yes | No | Offer as configured opt-in control |

The distinction is portability and operational safety, not whether the control has
security value. Default SCPs should prevent broadly undesirable outcomes without
encoding a customer's operating model. Architecture-dependent controls belong in
a configurable, opt-in policy tier.
