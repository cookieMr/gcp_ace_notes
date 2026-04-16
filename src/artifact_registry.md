# Artifact Registry: ACE Exam Study Guide (2026)

![Artifact Registry](images/artifact_registry.png)

_Image source: Google Cloud Documentation_

## 1. Artifact Registry Overview

Artifact Registry is the evolution of Container Registry (GCR), providing a single, secure, and managed place to store and manage build artifacts (container images, language packages, and OS packages).

- **Key Characteristics:**
  - **Unified:** Stores Docker images, Maven (Java), npm (Node.js), PyPI (Python), and OS packages (Debian, RPM).
  - **Secure:** Supports vulnerability scanning, Binary Authorization, and fine-grained IAM controls.
  - **Regional:** Repositories are regional or multi-regional, unlike the project-wide nature of GCR.
  - **Replacement for GCR:** Google recommends using Artifact Registry for all new projects.

| Feature                  | Artifact Registry (AR)                                              | Google Container Registry (GCR)          |
| ------------------------ | ------------------------------------------------------------------- | ---------------------------------------- |
| Status                   | Current, fully supported; recommended for all new workloads Legacy; | in maintenance mode                      |
| Supported Artifact Types | Containers, Maven, npm, Python, generic artifacts                   | Containers only                          |
| Repository Structure     | Regional or multi‑regional repositories                             | Multi‑regional buckets (us, eu, asia)    |
| IAM & Permissions        | Fine‑grained, repo‑level IAM                                        | Bucket‑level IAM (coarse)                |
| VPC‑SC Support           | Full support                                                        | Limited                                  |
| Security Scanning        | Built‑in scanning + deeper integration with Security Command Center | Basic container scanning                 |
| Performance              | Faster pulls, optimized caching, regional isolation                 | Older architecture, slower under load    |
| Tag & Version Management | More flexible; supports immutability policies                       | Basic tagging                            |
| Pricing Model            | Storage + network egress (per repo)                                 | Storage + network egress (per bucket)    |
| Recommended Use          | All new container and artifact storage                              | Only for legacy workloads; migrate to AR |

## 2. Core Concepts

- **Repository:** A container for artifacts of a specific type (e.g., a "Docker" repository).
- **Package:** A group of artifacts that share the same name (e.g., `my-app`).
- **Version:** A specific instance of a package (e.g., `v1.0.0` or a Docker tag).
- **Registry Format:** The format of the repository (e.g., `Docker`, `npm`, `Maven`).

## 3. Repository Types

- **Standard:** Stores your private artifacts.
- **Remote:** Acts as a proxy for external repositories (e.g., Docker Hub, npmjs.org) and caches them locally for faster, more reliable builds.
- **Virtual:** Combines multiple standard and remote repositories into a single endpoint.

## 4. Security and Compliance

- **Vulnerability Scanning:** Automatically scans container images for known security vulnerabilities.
- **Binary Authorization:** Integration with GKE ensures only trusted, scanned images are deployed.
- **CMEK (Customer-Managed Encryption Keys):** Allows you to encrypt your artifacts with your own keys from Cloud KMS.
- **Fine-grained IAM:** Permissions can be granted at the _repository level_, whereas GCR permissions were tied to the underlying Cloud Storage bucket.

## 5. Repository Structure (Naming)

For Docker images, the format is:
`[LOCATION]-docker.pkg.dev/[PROJECT_ID]/[REPOSITORY_NAME]/[IMAGE_NAME]:[TAG]`

- **Example:** `us-central1-docker.pkg.dev/my-project/my-repo/my-app:v1`

## 6. Access Control (IAM)

- `roles/artifactregistry.admin`: Full control over repositories and artifacts.
- `roles/artifactregistry.repoAdmin`: Manage repositories only.
- `roles/artifactregistry.writer`: Upload and delete artifacts.
- `roles/artifactregistry.reader`: View and pull artifacts.

## 7. Essential `gcloud` Commands

- **Create a Docker Repository:**
  `gcloud artifacts repositories create [NAME] --repository-format=docker --location=[LOCATION] --description="My Docker repo"`
- **Configure Docker for Artifact Registry:**
  `gcloud auth configure-docker [LOCATION]-docker.pkg.dev`
- **List Repositories:** `gcloud artifacts repositories list`
- **List Artifacts in a Repository:** `gcloud artifacts docker images list [LOCATION]-docker.pkg.dev/[PROJECT]/[REPO]`

## 8. Exam Tips

- **GCR vs. Artifact Registry:** Remember that Artifact Registry is the modern, regional, and multi-format successor to Container Registry (GCR).
- **Vulnerability Scanning:** Know that this is a core feature for secure container-based workflows.
- **Repository Naming:** Be prepared to identify the correct format for an Artifact Registry Docker image URL.
- **Multi-format:** If a question mentions storing npm or Python packages in Google Cloud, the answer is always **Artifact Registry**.
- **Cleanup Policies:** You can define policies to automatically delete old versions of packages to save costs.
