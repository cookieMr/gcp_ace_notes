# Deployment Manager: ACE Exam Study Guide (2026)

## 1. Deployment Manager Overview

Deployment Manager is an Infrastructure as Code (IaC) service that allows you to automate the creation and management of Google Cloud resources.

- **Key Characteristics:**
  - **Declarative:** You specify "what" the infrastructure should look like, and Google Cloud handles the "how" to create it.
  - **Infrastructure as Code:** Allows you to version control and repeatably deploy your infrastructure.
  - **Native to GCP:** Fully integrated with Google Cloud services and IAM.

## 2. Core Components

- **Configuration:** A single YAML file that defines all the resources you want in a deployment.
- **Templates:** Reusable code blocks used to simplify configurations. Written in **Jinja2** or **Python**.
- **Resources:** Individual GCP services (e.g., a VM, a Bucket) defined in the configuration.
- **Types:** The specific kind of resource being created (e.g., `compute.v1.instance`).
- **Properties:** The settings for a resource (e.g., `machineType`, `zone`).

## 3. Configuration File Structure (YAML)

A basic configuration file includes a `resources` list:

```yaml
resources:
  - name: my-vm
    type: compute.v1.instance
    properties:
      zone: us-central1-a
      machineType: zones/us-central1-a/machineTypes/n1-standard-1
      # ... other properties
```

## 4. Templates (Jinja2 and Python)

Templates allow you to abstract logic and make configurations more dynamic.

- **Jinja2:** Simpler, logic-based templating.
- **Python:** More powerful, allows for complex calculations and logic.
- **Importing:** Templates must be explicitly imported into the main YAML configuration file.

## 5. Deployment Lifecycle

- **Create:** Initial deployment of resources.
- **Preview:** Allows you to see what Deployment Manager will do without actually creating resources. (Uses the `--preview` flag).
- **Update:** Modifying an existing deployment. Deployment Manager determines the difference and applies changes.
- **Delete:** Removes all resources associated with a deployment.

## 6. Security and IAM

- **Service Account:** Deployment Manager uses the **Google Cloud APIs Service Agent** by default to create resources on your behalf.
  - _Exam Tip:_ If Deployment Manager fails to create a resource, ensure this service agent has the necessary IAM permissions.
- **IAM Roles:**
  - `roles/deploymentmanager.admin`: Full control.
  - `roles/deploymentmanager.editor`: Create and manage deployments.
  - `roles/deploymentmanager.viewer`: View deployments only.

## 7. Essential gcloud Commands

- **Create a Deployment:**
  `gcloud deployment-manager deployments create [NAME] --config [FILE.YAML]`
- **Update a Deployment:**
  `gcloud deployment-manager deployments update [NAME] --config [NEW_FILE.YAML]`
- **Preview a Deployment:**
  `gcloud deployment-manager deployments create [NAME] --config [FILE.YAML] --preview`
- **List Deployments:**
  `gcloud deployment-manager deployments list`
- **Delete a Deployment:**
  `gcloud deployment-manager deployments delete [NAME]`

## 8. Exam Tips

- **YAML vs. Templates:** Remember that the main config is always **YAML**, but reusable parts are **Jinja2** or **Python**.
- **Declarative Nature:** If a question asks how to ensure a specific state for infrastructure repeatably, the answer is often **Deployment Manager** (or Terraform).
- **Terraform vs. Deployment Manager:** While both are IaC, Deployment Manager is the Google-native tool. If a question specifically mentions "GCP-native templates," it's Deployment Manager.
- **Preview Mode:** Always use the `--preview` flag to validate changes before applying them to production.
- **Resource Types:** Familiarize yourself with the syntax for types like `compute.v1.instance` or `storage.v1.bucket`.
