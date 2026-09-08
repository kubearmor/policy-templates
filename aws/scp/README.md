# Universal AWS Organizations hardening SCPs

This directory contains the small set of deny-list service control policies
(SCPs) that can be recommended to AWS Organizations customers without workload
behavioral data or customer-specific account IDs, Regions, role names, tags, or
resource identifiers.

## Universal controls

- `deny-leaving-organization.yaml` prevents member accounts from leaving the
  organization.
- `prevent-account-closure.yaml` prevents member accounts from closing
  themselves.
- `prevent-root-access-key-creation.yaml` prevents direct member-account root
  users from creating long-lived access keys while retaining remediation paths.
- `require-secure-transport.yaml` denies requests made over insecure transport
  and exempts direct AWS service-principal calls.

These policies are standalone controls. Select all four for the universal
baseline; there is intentionally no combined file, which keeps each control
independently visible and manageable.

SCPs restrict maximum permissions and do not grant permissions. They affect
member accounts, including delegated administrator accounts, but do not restrict
users or roles in the AWS Organizations management account.

Even universal controls should be tested first in a representative
non-production OU before organization-wide attachment.

For the qualification criteria and product rationale, see
[`DEFAULT-QUALIFICATION-RATIONALE.md`](DEFAULT-QUALIFICATION-RATIONALE.md).
