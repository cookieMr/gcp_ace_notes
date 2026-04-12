# Organization Policies: ACE Exam Study Guide (2026)

## 1. Organization Policies Overview

Organization Policies provide centralized and programmatic control over your organization's cloud resources. They act as guardrails to ensure compliance and security across the entire resource hierarchy.

- Purpose: Restrict what can be done with resources, regardless of a user's IAM permissions.
- Scope: Can be applied at the Organization, Folder, or Project level.
- Organization Policy vs. IAM:
  - IAM: Focuses on who can do what (identity-based).
  - Organization Policy: Focuses on what can be done to a resource (resource-based constraints).
  - Crucial Exam Tip: If an Organization Policy denies an action, it overrides all IAM permissions. Even a Project Owner cannot bypass an Organization Policy constraint.

## 2. Constraints and Policies

- Constraint: A blueprint that defines a specific restriction (e.g., constraints/compute.disableExternalIPs).
- Policy: The actual configuration of a constraint applied to a specific resource (Organization, Folder, or Project).
- Types of Constraints:
  - List Constraints: Allow or deny a specific list of values (e.g., Allowed locations for Cloud Storage or Allowed shared VPC host projects).
  - Boolean Constraints: Enforce or do not enforce a specific behavior (e.g., Disable serial port access or Skip default network creation).

## 3. Resource Hierarchy and Inheritance

Policies follow the Google Cloud resource hierarchy.

- Inheritance: By default, a policy applied at a higher level (e.g., Organization) is inherited by all child resources (Folders, Projects).
- Policy Evaluation: The effective policy is the result of the policy applied at the current level plus any inherited settings.
- Overriding: You can choose to Override a parent's policy at a lower level to make it more or less restrictive (if allowed).
- Resetting: You can choose to Restore to parent to remove local modifications and inherit from the parent again.

## 4. Key Exam Scenarios and Constraints

You should recognize these common constraints for the exam:

- Resource Location Restriction: Restricts the physical locations where resources (VMs, buckets, etc.) can be created.
  > The `constraints/gcp.resourceLocations` constraint allows you to specify which geographical locations resources can be created in. You can define a whitelist of regions (e.g., europe-west1, europe-west2) to ensure data residency requirements are met.
- Disable Service Account Key Creation: Prevents users from creating external JSON keys for service accounts.
- Disable External IP Addresses: Prevents Compute Engine instances from having external (public) IP addresses.
- Restrict Shared VPC Host Projects: Limits which projects can be used as Shared VPC hosts.
- Enforce Shielded VM: Requires all new VMs to use Shielded VM features for improved security.

## 5. Advanced Features (2026 Focus)

- Dry-run Mode: Allows you to test the impact of a policy change before enforcing it. This generates audit logs showing what would have been blocked without actually blocking it.
- Tags-based Policies: Allows you to conditionally apply organization policies based on Tags attached to resources (e.g., apply a strict policy only to resources tagged with environment: production).
- Gemini for Policy Analysis: Use Gemini in the Cloud Console to explain the impact of an Organization Policy on your existing resources and identify potential violations before they occur.

## 6. Essential `gcloud` Commands

- List Available Constraints: `gcloud resource-manager org-policies list --organization=[ORG_ID]`
- Describe Current Policy: `gcloud resource-manager org-policies describe [CONSTRAINT_NAME] --project=[PROJECT_ID]`
- Set a Policy (from YAML): `gcloud resource-manager org-policies set-policy [POLICY_FILE].yaml --project=[PROJECT_ID]`
- Delete a Policy: `gcloud resource-manager org-policies delete [CONSTRAINT_NAME] --project=[PROJECT_ID]`

## 7. Troubleshooting Tip

If a user reports they have Owner permissions but cannot create a VM with an external IP or create a bucket in a specific region, always check Organization Policies first. The error message will typically indicate that a constraint has been violated.
