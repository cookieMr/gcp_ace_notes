# Organization Policies: ACE Exam Study Guide (2026)

![Dilbert helps when no image for Organization Policies can be found](images/00_image_is_needed.png)

_Image source: Dilbert.com_

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

- **Resource Location Restriction:** Restricts the physical locations where resources (VMs, buckets, etc.) can be created.
  - Constraint: `constraints/gcp.resourceLocations`
  - Use case: Data residency compliance (e.g., EU-only data).
- **Disable Service Account Key Creation:** Prevents users from downloading JSON keys for service accounts (improves security).
  - Constraint: `constraints/iam.disableServiceAccountKeyCreation`
- **Disable External IP Addresses:** Prevents VMs from having public IP addresses.
  - Constraint: `constraints/compute.disableExternalIPAccess`
- **Restrict Shared VPC Host Projects:** Limits which projects can beShared VPC hosts.
  - Constraint: `constraints/compute.restrictShared VPCHostProjects`
- **Enforce Shielded VM:** Requires all new VMs to use Shielded VM features.
  - Constraint: `constraints/compute.requireShieldedVm`
- **Allow Cloud NAT:** Forces all VMs to use Cloud NAT (no direct egress).
  - Constraint: `constraints/compute.requireNatConfig`
- **Disable VPC Auto Mode:** Prevents automatic VPC network creation.
  - Constraint: `constraints/compute.skipDefaultNetworkCreation`
- **Restrict CMEK:** Requires CMEK for specific services.
  - Constraint: `constraints/storage.require CMEK`
- **Disable Serial Port Access:** Blocks serial port access on VMs.
  - Constraint: `constraints/compute.disableSerialPortAccess`
- **Allowed SSH Key Sources:** Restricts which users can add SSH keys to metadata.
  - Constraint: `constraints/compute.trustedImageProjects`
- **Public Access Prevention:** Blocks public access to Cloud Storage buckets.
  - Constraint: `storage.publicAccessPrevention`

> **Exam Tip:** Organization Policy constraints are evaluated before IAM. If a policy denies, IAM cannot override it.

## 5. Advanced Features (2026 Focus)

- **Dry-run Mode:** Test a policy's impact without enforcing. Audit logs show what _would_ be blocked.
- **Tags-based Policies:** Apply policies conditionally based on resource tags (e.g., stricter policy for `environment:prod` resources).
- **List Policy Evaluation:** For list constraints, use `whitelist` (allow list) or `blacklist` (deny list) modes.
- **Condition Support:** Use IAM-style conditions in org policies for advanced scenarios.

## 5a. Common Constraint Reference

| Constraint | Description | Type |
|------------|-------------|------|
| `gcp.resourceLocations` | Allowed resource locations | List |
| `iam.disableServiceAccountKeyCreation` | Block SA key downloads | Boolean |
| `compute.disableExternalIPAccess` | No public IPs on VMs | Boolean |
| `compute.requireShieldedVm` | Require Shielded VM | Boolean |
| `storage.publicAccessPrevention` | Block public access | Boolean |
| `compute.skipDefaultNetworkCreation` | Block auto VPC creation | Boolean |

## 6. Essential `gcloud` Commands

- **List Available Constraints:** `gcloud resource-manager org-policies list --organization=[ORG_ID]`
- **Describe Current Policy:** `gcloud resource-manager org-policies describe [CONSTRAINT_NAME] --project=[PROJECT_ID]`
- **Set a Policy (from YAML):** `gcloud resource-manager org-policies set-policy [POLICY_FILE].yaml --project=[PROJECT_ID]`
- **Delete a Policy:** `gcloud resource-manager org-policies delete [CONSTRAINT_NAME] --project=[PROJECT_ID]`
- **Set Boolean Policy:** `gcloud resource-manager org-policies set-policy [CONSTRAINT_NAME] --project=[PROJECT_ID] --policy-file=[FILE].yaml`
- **List Effective Policy:** `gcloud resource-manager org-policies describe [CONSTRAINT_NAME] --effective --project=[PROJECT_ID]`

## 6a. Policy YAML Example

```yaml
spec:
  rules:
    - allowAll: false
  updateTime: '2024-01-01T00:00:00Z'
```

## 7. Troubleshooting Tip

If a user reports they have Owner permissions but cannot create a VM with an external IP or create a bucket in a specific region, always check Organization Policies first. The error message will typically indicate that a constraint has been violated.
