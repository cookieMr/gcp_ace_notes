# GCP Google Kubernetes Engine (GKE): ACE Exam Study Guide

[Back to root](./README.md)

## 1. GKE Fundamentals

Google Kubernetes Engine (GKE) is a managed environment for deploying, managing, and scaling containerized applications using Google infrastructure.

- **Managed Kubernetes:** Google manages the Kubernetes Control Plane (Master nodes), while you manage the worker nodes (in Standard mode).
- **Cluster Types (The most important exam distinction):**
  - **Autopilot:** Fully managed. Google manages the nodes, scaling, and security. You pay only for the pods you run. Best for most use cases where you don't need low-level node customization.
  - **Standard:** You manage the node infrastructure. You have full control over the nodes, including SSH access and custom machine types. You pay for the underlying Compute Engine VMs.

## 2. Cluster Configurations

- **Regional Clusters:** The Control Plane and nodes are replicated across multiple zones within a region. Provides higher availability (99.95% SLA).
- **Zonal Clusters:** The Control Plane and nodes are in a single zone. Less expensive but vulnerable to a single zone failure (99.5% SLA).
- **Private Clusters:** Nodes have only internal IP addresses. Communication with the Control Plane happens via VPC peering. Requires a NAT Gateway (Cloud NAT) for outbound internet access.

## 3. Node Management & Scaling

- **Node Pools:** A group of nodes within a cluster that all have the same configuration (machine type, labels, etc.). A cluster can have multiple node pools (e.g., one with GPUs, one with high-memory).
- **Cluster Autoscaler:** Automatically adds or removes nodes from node pools based on the resource demands (CPU/Memory) of the pods.
- **Horizontal Pod Autoscaler (HPA):** Scales the number of pod replicas based on CPU utilization or custom metrics.
- **Vertical Pod Autoscaler (VPA):** Recommends or automatically adjusts the CPU and memory reservations for your pods.

## 4. GKE Networking

- **Services:**
  - **ClusterIP:** Default. Exposes the service on an internal IP only.
  - **NodePort:** Exposes the service on each Node's IP at a static port.
  - **LoadBalancer:** Creates an external Google Cloud Load Balancer (TCP/UDP) to route traffic to the pods.
- **Ingress:** A Kubernetes resource that manages external access to services, typically HTTP/HTTPS. It creates a Google Cloud HTTP(S) Load Balancer and provides features like SSL termination and path-based routing.
- **Container-Native Load Balancing:** Uses Network Endpoint Groups (NEGs) to route traffic directly to pods rather than nodes, reducing latency.

## 5. Storage in GKE

- **Persistent Volumes (PV):** A piece of storage in the cluster (e.g., a Persistent Disk).
- **Persistent Volume Claims (PVC):** A request for storage by a user/pod.
- **Storage Classes:** Used to define the type of storage (e.g., standard HDD vs. SSD). GKE provides a default storage class that automatically provisions Persistent Disks.

## 6. GKE Security

- **Workload Identity:** _Critical Exam Topic._ The recommended way for GKE workloads to access Google Cloud services (GCS, BigQuery, etc.). It links a Kubernetes Service Account (KSA) to a Google Cloud Service Account (GSA).
- **Binary Authorization:** Ensures only trusted container images are deployed to your cluster.
- **RBAC (Role-Based Access Control):** Manages permissions _inside_ the Kubernetes cluster (e.g., who can create pods).
- **IAM:** Manages permissions _outside_ the cluster (e.g., who can create or delete the cluster).
- **Shielded GKE Nodes:** Provides verifiable node identity and integrity to protect against rootkits and boot-level malware.

## 7. Essential gcloud & kubectl Commands

- **Create a Cluster:** `gcloud container clusters create [CLUSTER_NAME] --zone [ZONE] --num-nodes [NUMBER]`
- **Get Credentials:** _High Probability._ Necessary to configure `kubectl` to talk to a specific cluster.
  `gcloud container clusters get-credentials [CLUSTER_NAME] --zone [ZONE]`
- **Resize a Cluster:** `gcloud container clusters resize [CLUSTER_NAME] --node-pool [POOL_NAME] --num-nodes [NEW_SIZE]`
- **Deploy an Application:** `kubectl apply -f [FILENAME.YAML]`
- **Check Pod Status:** `kubectl get pods`
- **Expose a Deployment:** `kubectl expose deployment [NAME] --type=LoadBalancer --port=80`

## 8. Exam Tips & Gotchas

- **Control Plane Upgrade:** Google automatically upgrades the Control Plane. You can define **Maintenance Windows** and **Exclusions** to control when upgrades happen.
- **Preemptible/Spot VMs in GKE:** You can use Spot VMs for node pools to save costs, but ensure your workloads are fault-tolerant.
- **Logging:** GKE integrates with Cloud Logging and Cloud Monitoring automatically. You can view container logs in the Google Cloud Console.

[Back to root](./README.md)
