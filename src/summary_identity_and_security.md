# Identity & Security

![Identity & Security](images/SecurityIdentity-512-color.png)

_Image source: Google Cloud Documentation_

### **[IAM](./iam.md)**

Manage access control by defining **who** (identity) has **what access** (role) for which resource. Focuses on the **Principle of Least Privilege** and a hierarchical inheritance model.

### **[Cloud KMS](./cloud_kms.md)**

Managed service to create, import, and manage cryptographic keys (symmetric and asymmetric) for encryption. Supports **Customer-Managed Encryption Keys (CMEK)** for integrated Google Cloud services.

### **[Secret Manager](./secret_manager.md)**

Secure storage for sensitive information like API keys, passwords, and certificates. Features versioning, replication policies, and fine-grained IAM-based access control.

### **[Organization Policies](./organization_policies.md)**

Centralized programmatic control over your organization's resources. Acts as security **guardrails** that can restrict allowed services or locations, regardless of a user's IAM permissions.

### **[VPC Service Controls](./vpc_service_controls.md)**

Create a security perimeter around Google-managed resources to mitigate **data exfiltration** risks. Controls access based on the source network rather than just user identity.

### **[Cloud Armor](./cloud_armor.md)**

Google Cloud's Web Application Firewall (WAF) and DDoS protection service. Protects applications from L7 attacks (SQLi, XSS) and provides IP-based filtering at the network edge.
