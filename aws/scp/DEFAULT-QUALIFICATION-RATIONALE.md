# Default SCP qualification rationale


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

