# Universal Azure hardening policies

This directory contains Azure Policy definitions that can be recommended without
collecting customer workload behavior or supplying customer-specific resource
names, locations, identities, networks, or exception values.

## Universal control

- `require-storage-secure-transfer.yaml` denies Azure Storage accounts that do
  not require secure transport.

Azure Policy definitions should be created at a management group when they need
to be assigned across multiple subscriptions. A definition does nothing until it
is assigned to an applicable scope.

Universal does not mean untested. Validate the policy in a non-production
management group or subscription before broad assignment. Existing noncompliant
resources are reported but aren't automatically changed by the `deny` effect.

Conditional and strict opt-in controls are maintained in [`../policy2`](../policy2).
