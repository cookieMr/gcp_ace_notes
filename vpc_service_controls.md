# GCP VPC Service Controls: ACE Exam Study Guide

[Back to README](README.md)

## 1. VPC Service Controls Overview

VPC Service Controls (VPC SC) is a security feature that allows you to define a security perimeter around Google-managed resources (like Cloud Storage, BigQuery, and Cloud SQL) to mitigate data exfiltration risks.

- **Primary Goal:** Prevent data exfiltration from Google Cloud services.
- **Key Functionality:** It limits access to protected services to only those requests originating from within a defined **Service Perimeter**.
- **VPC SC vs. IAM:**
  - **IAM:** Determines **who** can access a resource.
  - **VPC SC:** Determines **where** the request can come from.
  - **Exam Tip:** Even if a user has the "Owner" IAM role, they will be blocked if their request originates from outside the allowed perimeter.

## 2. Core Components

- **Service Perimeter:** A logical boundary that isolates Google Cloud resources. Projects within a perimeter can communicate freely, but communication across the boundary is restricted.
- **Access Levels:** Defined using **Access Context Manager**. They allow access to a perimeter based on attributes like:
  - Source IP address (e.g., corporate office range).
  - User identity.
  - Device type (e.g., encrypted, company-owned).
- **Service Perimeter Bridge:** Allows projects in different perimeters to communicate with each other. Use this when you have two distinct perimeters that need to share data.
- **Egress and Ingress Rules:** Granular rules that allow specific communication into or out of a perimeter without using a full bridge.

## 3. Key Concepts & Scenarios

- **Data Exfiltration Mitigation:** VPC SC prevents scenarios where a malicious insider copies data from a production BigQuery dataset to a personal dataset outside the organization.
- **Private Google Access:** Often used in conjunction with VPC SC. It allows VMs with only internal IP addresses to reach Google APIs.
- **Dry-Run Mode:** Allows you to test a perimeter configuration without enforcing it. It generates audit logs showing what _would_ have been blocked. Always use this before moving to enforcement in production.
- **VPC Service Controls Troubleshooter:** A tool in the Cloud Console used to diagnose why a request was blocked (e.g., finding the missing access level or perimeter project).

## 4. Protected Services

Not all services are supported, but the most common exam-relevant services include:

- Cloud Storage (GCS)
- BigQuery
- Cloud SQL
- Pub/Sub
- Cloud Spanner
- Cloud Functions
- Artifact Registry

## 5. Implementation Steps

1. **Create an Access Policy:** The container for all access levels and perimeters (usually at the Organization level).
2. **Define Access Levels:** Specify the conditions (IPs, devices) for allowed access.
3. **Create a Service Perimeter:**
   - Add projects to the perimeter.
   - Select the services to protect (e.g., Storage, BigQuery).
   - Attach Access Levels (optional).
4. **Test in Dry-Run Mode:** Monitor audit logs for potential breakages.
5. **Enforce the Perimeter.**

## 6. Essential gcloud Commands

- **List Perimeters:** `gcloud access-context-manager perimeters list --policy=[POLICY_ID]`
- **Describe a Perimeter:** `gcloud access-context-manager perimeters describe [PERIMETER_NAME] --policy=[POLICY_ID]`
- **List Access Levels:** `gcloud access-context-manager levels list --policy=[POLICY_ID]`

## 7. Troubleshooting Tip

If you see a `403 Forbidden` error with a reason like `RESOURCES_NOT_IN_PERIMETER` or `ACCESS_DENIED_BY_VPC_SERVICE_CONTROLS`, it means VPC SC is blocking the request. Check if:

- The project is included in the perimeter.
- The service is being protected by the perimeter.
- The user's request meets the criteria of the attached Access Level (e.g., correct IP address).

[Back to README](README.md)
