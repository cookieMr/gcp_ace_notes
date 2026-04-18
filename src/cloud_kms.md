# Cloud KMS: ACE Exam Study Guide (2026)

![Cloud KMS](images/key_management_service.png)

_Image source: Google Cloud Documentation_

## 1. Cloud KMS Overview

Cloud KMS is a managed service that allows you to create, import, and manage cryptographic keys and perform cryptographic operations in a single centralized cloud service.

- **Purpose:** Securely manage symmetric and asymmetric encryption keys for use with other Google Cloud services.
- **KMS Hierarchy:**
  - **Project:** The top-level container for all KMS resources.
  - **Location:** Key rings are regional or multi-regional (e.g., `us-east1` or `us`).
  - **Key Ring:** A logical grouping of keys for easier management and permissioning.
  - **CryptoKey:** A named resource that contains one or more key versions.
  - **CryptoKeyVersion:** The actual material used for cryptographic operations.
- **KMS Autokey (2026 Update):** A newer feature that simplifies CMEK by allowing services to request keys on-demand, with KMS automatically handling the creation and assignment of keys according to best practices.

## 2. Key Management Operations

The ACE exam expects you to know how to perform basic lifecycle operations on keys.

- **Creating Key Rings and Keys:**
  - Key rings are permanent and cannot be deleted.
  - Keys can be created within a key ring with specific purposes (e.g., `symmetric-encryption`).
- **Key Rotation:**
  - **Automatic Rotation:** You can set a rotation schedule (e.g., every 90 days).
  - **Manual Rotation:** You can manually create a new key version and set it as the primary version.
  - **Note:** Older versions remain available to decrypt data encrypted with them, but the primary version is used for new encryption.
- **Key State Management:**
  - **Enabled:** Key version can be used.
  - **Disabled:** Key version cannot be used but is not deleted.
  - **Scheduled for Destruction:** Marks a key for deletion after a 24-hour waiting period (can be cancelled within that window).
- **Envelope Encryption:**
  - Cloud KMS uses envelope encryption: generate a **datakey** (DEK) to encrypt the actual data, then use the KMS key to encrypt the datakey (KEK).
  - The encrypted datakey is stored alongside the encrypted data.
  - **Benefit:** Only the small datakey needs to be encrypted by KMS, not the entire dataset.
- **HSM-Backed Keys:**
  - Keys can be software-backed (default) or HSM-backed for higher security.
  - HSM keys use hardware security modules to store key material.
  - Use `--protection-level hsm` when creating keys for HSM protection.
- **Key Import:**
  - Import your own key material into Cloud KMS (for compliance/regulatory requirements).
  - Supported formats: RSA wrap key, asymmetric key, symmetric key.
  - Requires the key to be created with `--importable` flag.

## 2.1. Asymmetric Keys

Cloud KMS supports asymmetric encryption for signing and verification.

- **Key Purposes:**
  - `asymmetric-sign`: For digital signatures (e.g., RSA, ECDSA).
  - `asymmetric-encryption`: For asymmetric encryption (e.g., RSA).
- **Supported Algorithms:**
  - RSA-OAEP (2048, 3072, 4096-bit)
  - ECdsa (P-256, P-384 curves)
- **Use Case:** Digitally sign documents or verify signatures without exposing private keys.
- **Exam Tip:** Asymmetric keys are used for signing/verification, symmetric keys for encryption/decryption.

## 3. Customer-Managed Encryption Keys (CMEK)

CMEK is a major exam topic. It allows you to use your own Cloud KMS keys to encrypt data at rest within Google Cloud services.

- **Default Encryption:** By default, Google Cloud encrypts all data at rest using Google-managed keys.
- **CMEK Integration:**
  - **Cloud Storage:** Set a default KMS key for a bucket.
  - **Compute Engine:** Encrypt Persistent Disks (PDs) on standard, N4, and C4 machine types.
  - **BigQuery:** Encrypt datasets and tables using a KMS key.
  - **Cloud SQL:** Encrypt PostgreSQL and SQL Server instances.
  - **Dataproc:** Encrypt clusters and data at rest.
  - **Spanner:** Encrypt databases using customer-managed keys.
  - **GKE (GKE CSI Driver):** Use CMEK for cluster secrets and PVCs.
  - **Secret Manager:** Encrypt secrets using CMEK.
  - **Service Agent Permissions:**
  - To use CMEK, the **Service Agent** for the specific service must be granted the `roles/cloudkms.cryptoKeyEncrypterDecrypter` role on the KMS key.
  - **Common Service Agent Format:** `service-[PROJECT_NUM]@gcp-sa-[SERVICE].iam.gserviceaccount.com` (e.g., for Cloud Storage: `service-[NUM]@gcp-sa-storage.iam.gserviceaccount.com`)

## 4. IAM Roles for Cloud KMS

The ACE exam focuses on the Principle of Least Privilege and Separation of Duties.

- **Cloud KMS Admin (`roles/cloudkms.admin`):** Allows managing key rings and keys. It does **not** allow using keys for encryption/decryption.
- **Cloud KMS CryptoKey Encrypter/Decrypter (`roles/cloudkms.cryptoKeyEncrypterDecrypter`):** Allows using keys to encrypt and decrypt data.
- **Cloud KMS Viewer (`roles/cloudkms.viewer`):** Allows viewing key rings and keys without the ability to use or manage them.
- **Best Practice:** Grant the `EncrypterDecrypter` role to the specific **Service Agent** that needs it.

## 5. KMS vs. Secret Manager vs. CSEK

You must distinguish between these three concepts for the exam.

- **Cloud KMS:** Used for managing encryption keys (to encrypt large files, disks, or database entries).
- **Secret Manager:** Used for managing sensitive strings like API keys, passwords, and database credentials.
- **Customer-Supplied Encryption Keys (CSEK):** You provide the raw key material. Google does not store the key.

## 6. Essential `gcloud` Commands

- **Create a Key Ring:** `gcloud kms keyrings create [NAME] --location [LOCATION]`
- **Create a Key:** `gcloud kms keys create [NAME] --keyring [RING] --location [LOCATION] --purpose encryption`
- **Create HSM Key:** `gcloud kms keys create [NAME] --keyring [RING] --location [LOCATION] --purpose encryption --protection-level hsm`
- **Add IAM Policy Binding:** `gcloud kms keys add-iam-policy-binding [KEY] --location [LOCATION] --keyring [RING] --member [MEMBER] --role roles/cloudkms.cryptoKeyEncrypterDecrypter`
- **Rotate a Key:** `gcloud kms keys versions rotate [KEY] --keyring [RING] --location [LOCATION]`
- **Enable a Key Version:** `gcloud kms keys versions enable [VERSION] --key [KEY] --keyring [RING] --location [LOCATION]`
- **Disable a Key Version:** `gcloud kms keys versions disable [VERSION] --key [KEY] --keyring [RING] --location [LOCATION]`
- **Destroy a Key Version:** `gcloud kms keys versions destroy [VERSION] --key [KEY] --keyring [RING] --location [LOCATION]`

## 7. External Key Manager (EKM)

- **Purpose:** Use your own on-premises key management infrastructure with Google Cloud services.
- **Use Case:** BYOK (Bring Your Own Key) with external key vendors (Thales, AWS CloudHSM, etc.).
- **How it works:** Google Cloud makes encryption/decryption requests to your external key server via EKM API.
- **Configuration:**
  - Create an EKM connection in Cloud KMS.
  - Define the external key URL and credentials.
  - Map Cloud KMS key names to external key IDs.
- **Exam Tip:** EKM provides control over key lifecycle but may have higher latency than native Cloud KMS.
