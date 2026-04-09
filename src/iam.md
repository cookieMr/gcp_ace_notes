# Identity and Access Management (IAM): ACE Exam Study Guide (2026)

## 1. IAM Overview

Identity and Access Management (IAM) allows you to manage access control by defining **who** (identity) has **what access** (role) to **which resource**.

- **The IAM Policy:** A collection of statements that define who has what type of access. A policy is attached to a resource and used to enforce access control.
- **The "Who" (Principals):**
  - Google Account (individual user).
  - Service Account (for applications and VMs).
  - Google Group (best practice for managing multiple users).
  - Google Workspace domain or Cloud Identity domain.
  - authenticatedUser (any signed-in Google account).
  - allUsers (anyone on the internet).

## 2. IAM Roles

A role is a collection of permissions. Permissions are typically in the format `service.resource.verb` (e.g., `compute.instances.list`).

- **Basic Roles (Primitive):**
  - **Owner:** Full control, including managing roles/permissions and billing.
  - **Editor:** Can modify resources but cannot manage roles or billing.
  - **Viewer:** Read-only access.
  - **Exam Tip:** Basic roles are generally too broad for production and violate the Principle of Least Privilege. They should only be used in very small projects or development environments.
- **Predefined Roles:**
  - Google-managed roles that provide granular access to specific services (e.g., `roles/storage.objectViewer`, `roles/compute.networkAdmin`).
  - **Exam Tip:** These are the preferred choice for most scenarios. You must be able to identify the correct predefined role for a given job function.
- **Custom Roles:**
  - User-defined roles created when predefined roles do not meet specific needs.
  - **Constraints:**
    - Cannot be applied at the Folder or Organization level; they are project-specific or organization-specific.
    - Require manual maintenance as new permissions are added to GCP services.
    - Cannot be used if the underlying permissions are not supported for custom roles.

> Primitive roles (Owner, Editor, Viewer) apply across all services in a project and are too broad for most use cases. Predefined roles are service-specific (e.g., compute.instanceAdmin) with granular permissions following the principle of least privilege.

## 3. Service Accounts

Service accounts are special identities used by applications and virtual machines, rather than people.

- **Types of Service Accounts:**
  - **User-managed:** Created by the user (e.g., `my-app-sa@project-id.iam.gserviceaccount.com`).
  - **Default Service Accounts:** Created automatically by GCP (e.g., Compute Engine default service account). These often have the "Editor" role by default, which is not recommended for production.
  - **Google-managed:** Used by GCP services to perform actions on your behalf.
- **Key Concepts:**
  - **Service Account User Role (`roles/iam.serviceAccountUser`):** To allow one service account to use another (e.g., attach it to a resource), grant the Service Account User role (`roles/iam.serviceAccountUser`) on the target service account to the acting service account or user.
  - **Service Account Keys:** Avoid downloading JSON keys for production. Use Identity Federation or attached service accounts instead.
  - **Workload Identity:** The recommended way for GKE workloads to access GCP services securely.
- **Exam Tip:** When a VM needs to access a Cloud Storage bucket, do not use user credentials or hardcoded keys. Attach a service account with the `roles/storage.objectViewer` role to the VM.

## 4. Principle of Least Privilege (PoLP)

The Principle of Least Privilege states that a principal should have only the minimum permissions necessary to perform their job.

- **Implementation:**
  - Use **Predefined Roles** instead of Basic Roles.
  - Apply roles at the **lowest possible level** in the resource hierarchy.
  - Use **IAM Conditions** to restrict access based on attributes like time, resource name, or IP address.
  - **IAM Recommender:** Regularly audit permissions using the AI-powered IAM Recommender to identify and remove unused roles.
  - **Policy Troubleshooter:** Use the Policy Troubleshooter to understand why a user has or doesn't have a specific permission.

## 5. Resource Hierarchy and Inheritance

IAM policies are hierarchical and permissions are inherited.

- **Hierarchy Level:** Organization > Folder > Project > Resource.
  - The maximum depth of the folder hierarchy in Google Cloud is `10` levels, where:
    - Organization = level 0
    - Folders = levels 1–9
    - Projects = always at the bottom
- **Inheritance:** A role granted at the Organization level is inherited by all Folders, Projects, and Resources within that Organization.
- **Additive Nature:** Permissions are additive. You cannot "deny" a permission at a lower level if it was granted at a higher level.
- **Exam Tip:** If a user is an "Editor" at the Project level, they are an "Editor" for every bucket in that project, regardless of any restrictive policies set on individual buckets.

## 6. IAM Best Practices for 2026

- **Gemini for IAM:** Leverage Gemini in the Google Cloud Console to explain complex IAM policies and suggest the most restrictive roles for a given set of permissions.
- **Use Groups:** Always assign roles to Google Groups rather than individual users to simplify management.
- **Audit Logs:** Use Cloud Audit Logs to track "Who did what, where, and when."
- **Avoid Default Service Accounts:** Create custom service accounts with specific roles instead of using the broad default accounts.
- **IAP (Identity-Aware Proxy):** Use IAP to control access to applications and VMs without relying on VPNs or external IP addresses.

## 7. Essential gcloud Commands

- **View Project Policy:** `gcloud projects get-iam-policy [PROJECT_ID]`
- **Add Role Binding:** `gcloud projects add-iam-policy-binding [PROJECT_ID] --member='user:[EMAIL]' --role='roles/viewer'`
- **Remove Role Binding:** `gcloud projects remove-iam-policy-binding [PROJECT_ID] --member='user:[EMAIL]' --role='roles/viewer'`
- **Create Service Account:** `gcloud iam service-accounts create [SA_NAME] --display-name="[DISPLAY_NAME]"`
- **List Service Accounts:** `gcloud iam service-accounts list`
