# Conditional and strict opt-in AWS Organizations SCPs

This directory contains hardening SCPs that have security value but are not safe
universal defaults. They require a customer prerequisite, architecture decision,
centralized administration model, or documented recovery procedure.

## Conditional controls

- `protect-ebs-encryption-default.yaml`: first enable EBS encryption by default
  in every governed Region.
- `prevent-public-ebs-snapshots.yaml`: confirm that the customer does not
  intentionally publish EBS snapshots and enable snapshot Block Public Access.
- `protect-ami-public-access.yaml`: confirm that public AMI publishing is not
  required and enable AMI Block Public Access in every governed Region.
- `protect-s3-public-access.yaml`: confirm that public S3 access is not required
  and enable the applicable S3 Block Public Access settings.
- `prevent-public-secrets-resource-policies.yaml`: ensure deployment tooling uses
  `BlockPublicPolicy` when changing Secrets Manager resource policies.
- `prevent-external-ram-sharing.yaml`: confirm that AWS RAM sharing outside the
  organization is not required.

## Strict opt-in controls

- `deny-root-user-activity.yaml`: denies all direct use of member-account root
  credentials; validate centralized root access and recovery procedures first.
- `protect-cloudtrail.yaml`: blocks both malicious tampering and legitimate
  member-account CloudTrail reconfiguration.
- `protect-security-monitoring.yaml`: blocks administrative changes to AWS Config,
  GuardDuty, Security Hub, and IAM Access Analyzer.
- `protect-kms-keys.yaml`: blocks disabling or scheduling deletion of every
  customer-managed KMS key without an exception path.

## Combined strict baseline

`default-baseline.yaml` combines universal controls with the conditional controls
above and the strict direct-root deny. It is retained for customers who explicitly
choose the complete bundle, but it must be treated as strict opt-in. Do not use it
as the universal customer baseline.

Do not attach `default-baseline.yaml` together with its equivalent standalone
controls unless duplication is intentional.

## Deployment requirements

1. Confirm all listed prerequisites and required exception paths.
2. Test in a representative non-production account or OU.
3. Validate automation, delegated administration, service-linked roles, incident
   response, key lifecycle, and break-glass procedures.
4. Review denied CloudTrail events before expanding attachment scope.

The universal catalog is maintained in [`../scp`](../scp).
