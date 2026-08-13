# AWS universal hardening SCPs

## Purpose

This document explains why the four policies in `aws/scp` are classified as
universal AWS Organizations hardening controls. In this catalog, *universal*
means that a policy can be recommended to customers without first collecting
workload behavioral data or defining customer-specific exceptions.

Universal does not mean risk-free or exempt from testing. Every SCP is a maximum
permissions boundary whose explicit denies cannot be overridden by an IAM
administrator in an affected member account. Each policy should still be tested
in a representative non-production account or OU before broad attachment.

## Qualification standard

A policy is considered universal when it:

1. Prevents a clear, high-impact security or governance failure.
2. Addresses a risk shared by ordinary AWS Organizations member accounts.
3. Requires no customer-specific account IDs, role names, Regions, resource
   names, network identifiers, tags, or behavioral history.
4. Does not assume a particular workload, identity, or network architecture.
5. Has a low likelihood of blocking legitimate daily application and
   administrative operations.
6. Preserves a practical management or remediation path.

## Summary

| Policy | Prevented risk | Why it is universal |
| --- | --- | --- |
| `deny-leaving-organization.yaml` | A member account escaping central governance | Workloads do not need to leave an organization during routine operation |
| `prevent-account-closure.yaml` | Accidental or unauthorized member-account closure | Applications and account operators do not need account closure privileges |
| `prevent-root-access-key-creation.yaml` | Creation of long-lived root credentials | Root access keys are unnecessary and dangerous; credential remediation remains possible |
| `require-secure-transport.yaml` | AWS API requests sent over insecure transport | TLS is the normal AWS API access method, with direct AWS service calls exempted |

## 1. Prevent member accounts from leaving the organization

Policy: `aws/scp/deny-leaving-organization.yaml`

The policy denies only `organizations:LeaveOrganization`. A member account that
leaves the organization can escape centrally attached SCPs and other organization
governance. Preventing self-departure ensures that account removal remains an
intentional central governance decision.

Why it qualifies:

- The risk applies to every governed member account.
- No workload needs this action for deployment or normal operation.
- The policy contains no customer-specific values or exception roles.
- It does not interfere with ordinary AWS service usage.
- The organization management account can still perform the centrally governed
  account-removal workflow because SCPs do not restrict management-account
  principals.
- AWS recommends denying member-account departure at the organization root.

This control does not prevent the management account from removing a member
account from the organization.

## 2. Prevent member-account closure

Policy: `aws/scp/prevent-account-closure.yaml`

The policy denies only `account:CloseAccount`. Account closure can make workloads
and data unavailable and begins the account-deletion lifecycle. It should be a
deliberate centrally governed event rather than a capability available inside
every member account.

Why it qualifies:

- Accidental or malicious closure is a high-impact risk for every member account.
- Applications and normal administrators do not need this action.
- It is independent of the services, Regions, and workloads in the account.
- It requires no role names, tags, account IDs, or resource identifiers.
- Central account lifecycle management remains available through the organization
  management account, which is outside SCP enforcement.
- AWS recommends denying member-account self-closure at the organization root.

This policy does not stop the organization management account from centrally
closing an eligible member account.

## 3. Prevent creation of root-user access keys

Policy: `aws/scp/prevent-root-access-key-creation.yaml`

The policy denies `iam:CreateAccessKey` only when the caller is the direct root
user of a member account. Long-lived root access keys provide unrestricted
programmatic credentials and create exceptional compromise risk.

Why it qualifies:

- AWS workloads and ordinary administration do not require root access keys.
- The policy targets only creation of a new root access key; it does not deny all
  root-user operations.
- Existing access keys can still be updated or deleted during remediation.
- IAM users and roles can still create access keys when otherwise authorized, so
  this policy does not impose a universal ban on IAM-user operating models.
- The `aws:AssumedRoot` check keeps AWS Organizations centrally managed short-term
  root sessions outside this deny.
- It requires no customer-specific identity names or account identifiers.

## 4. Require secure transport

Policy: `aws/scp/require-secure-transport.yaml`

The policy denies requests when AWS reports `aws:SecureTransport` as `false`. It
therefore prevents calls made over an insecure transport channel.

Why it qualifies:

- Protecting credentials and request data in transit is relevant to every AWS
  account and workload.
- Normal AWS SDK, CLI, console, and service endpoints use TLS.
- The policy requires no customer network ranges, endpoints, Regions, or roles.
- The `aws:PrincipalIsAWSService` condition exempts direct AWS service-principal
  calls, avoiding disruption when network context is redacted during
  service-to-service authorization.
- It does not prescribe a VPC, proxy, private-endpoint, or egress architecture.

This policy checks the AWS authorization context. It does not configure TLS for
customer applications, load balancers, or application endpoints.

## Scope and limitations

- SCPs apply to affected member accounts, including delegated administrator
  accounts. They do not restrict users, roles, or the root user in the AWS
  Organizations management account.
- SCPs do not grant permissions. An IAM or resource policy must still allow an
  action before it can be performed.
- These controls prevent future actions; they do not remediate existing root
  access keys or reverse an account state that already exists.
- Universal classification is a product deployment tier, not a claim that testing
  can be skipped.

## References

- [AWS Organizations SCP documentation](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)
- [AWS Organizations management-account best practices](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_best-practices_mgmt-acct.html)
- [AWS root-user best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/root-user-best-practices.html)
- [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)
