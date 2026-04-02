# GKE: ACE Exam Study Guide (2026)

[Back to README](README.md)

## 1. GKE Fundamentals

Google Kubernetes Engine (GKE) is a managed environment for deploying, managing, and scaling containerized applications using Google infrastructure.

- Managed Kubernetes: Google manages the Kubernetes Control Plane, while you manage worker nodes in Standard mode.
- Cluster Types:
  - Autopilot: The default and recommended mode for 2026. Fully managed; Google manages nodes, scaling, and security. You pay only for running pods.
  - Standard: You manage the node infrastructure. Full control over nodes, SSH access, and custom machine types.

## 2. Cluster Configurations

- Regional Clusters: Control Plane and nodes replicated across multiple zones. Higher availability (99.95% SLA).
- Zonal Clusters: Control Plane and nodes in a single zone. Less expensive (99.5% SLA).
- Private Clusters: Nodes have internal IP addresses only. Communication with Control Plane via VPC peering. Requires Cloud NAT for outbound internet access.

## 3. Node Management and Scaling

- Node Pools: A group of nodes with the same configuration. Support for N4 and C4 machine types in 2026 for optimized performance.
- Cluster Autoscaler: Automatically adds or removes nodes based on resource demands.
- Horizontal Pod Autoscaler (HPA): Scales pod replicas based on CPU or custom metrics.
- Vertical Pod Autoscaler (VPA): Adjusts CPU and memory reservations for pods.

## 4. GKE Networking

- Services:
  - ClusterIP: Internal IP only.
  - NodePort: Exposes service on each Node's IP at a static port.
  - LoadBalancer: Creates an external Google Cloud Load Balancer.
- Ingress: Manages external access (HTTP/HTTPS) and creates a Google Cloud App Load Balancer.
- Container-Native Load Balancing: Uses Network Endpoint Groups (NEGs) to route traffic directly to pods.

## 5. Storage in GKE

- Persistent Volumes (PV) and Persistent Volume Claims (PVC): Managed storage for stateful applications.
- Storage Classes: Defines storage types (e.g., standard HDD, SSD, or Balanced PD).
- Hyperdisk: Support for Google Cloud Hyperdisk in 2026 for high-performance GKE workloads.

## 6. GKE Security

- Workload Identity: The recommended way for GKE workloads to access Google Cloud services.
- Binary Authorization: Ensures only trusted container images are deployed.
- RBAC: Manages permissions inside the cluster.
- IAM: Manages permissions outside the cluster (e.g., cluster creation).
- Shielded GKE Nodes: Provides node identity and integrity.

## 7. Gemini and Observability

- Gemini in GKE: Use Gemini for AI-powered troubleshooting, log summarization, and cluster health monitoring.
- GKE Enterprise: Advanced fleet management and AI-driven cost optimization powered by Gemini.
- Gemini Code Assist: Use to generate Kubernetes manifests and Helm charts from natural language.

## 8. Essential gcloud and kubectl Commands

- Create a Cluster: `gcloud container clusters create [CLUSTER_NAME] --zone [ZONE] --num-nodes [NUMBER]`
- Get Credentials: `gcloud container clusters get-credentials [CLUSTER_NAME] --zone [ZONE]`
- Resize a Cluster: `gcloud container clusters resize [CLUSTER_NAME] --node-pool [POOL_NAME] --num-nodes [NEW_SIZE]`
- Deploy an Application: `kubectl apply -f [FILENAME.YAML]`
- Check Pod Status: `kubectl get pods`

## 9. Exam Tips and Gotchas

- Control Plane Upgrade: Google automatically upgrades the Control Plane. Define Maintenance Windows and Exclusions.
- Preemptible/Spot VMs: Use for cost savings in fault-tolerant workloads.
- Autopilot vs Standard: Choose Autopilot for reduced operational overhead unless specific node customization is required.

[Back to README](README.md)
