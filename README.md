# AWS EKS Cluster Provisioning with eksctl

---
This repository contains the Infrastructure as Code (IaC) configuration and manifests required to provision a production-grade Amazon EKS cluster on AWS using eksctl and CloudFormation.

---

## 📋 Prerequisites & Local Setup
Before deploying the cluster, ensure your local workspace meets the following requirements:
* **AWS CLI (v2):** Installed and configured with appropriate IAM permissions (AdministratorAccess or equivalent EKS/VPC/IAM rights).

```bash
aws configure
aws sts get-caller-identity
```
* **eksctl:** Installed on your local machine to parse the manifest and deploy CloudFormation stacks.

* **kubectl:** Installed to manage Kubernetes resources after the control plane is live.

* **SSH Key Pair:** An RSA key pair generated on your local machine (`~/.ssh/id_rsa.pub` or Windows equivalent) to enable SSH access to worker nodes.

```bash
ssh-keygen -t rsa -b 4096
```
---

## 🛠️ Pre-Deployment Steps
### 1. Clone/Fork the Repository:

```bash
git clone https://github.com/edrex-cloud/prod-eks-cluster.git
cd prod-eks-cluster
```
### 2. Review Cluster Manifest:
Verify the parameters inside `eks-cluster.yaml` including region, Kubernetes version, node sizes, and auto-scaling bounds.

---
## 🚀 Deployment Instructions
Run the following command in your terminal from the directory containing your manifest:

```bash
eksctl create cluster -f .\eks-cluster.yaml
```
> Note: The cluster creation process takes approximately 15–20 minutes as AWS provisions the control plane, VPC subnets, NAT Gateways, IAM OIDC provider, and Managed Node Groups.

---

## 📊 Cluster Architecture & Key Features
* **Control Plane:** AWS EKS managed Kubernetes control plane running version 1.34 in `us-east-1`.

* **Managed Node Groups:**
  * **Standard Workers:** On-Demand `t3a.medium` instances for primary workloads (desired: 3, min: 2, max: 5).
  * **Spot Workers:** Mixed `t3.medium` / `t3.large` spot instances for cost-optimized processing (desired: 2, min: 0, max: 10).
* **Storage Drivers (CSI):** Pre-configured with AWS EBS CSI Driver (block storage) and AWS EFS CSI Driver (shared file storage).
* **IAM Security:** `withOIDC: true` enabled to allow fine-grained IAM Roles for Service Accounts (IRSA).
* **Networking:** AWS VPC CNI for native pod networking with dual public and private cluster endpoints.

---

## 🔐 Post-Deployment Verification
### 1. Verify Node Status:

```bash
kubectl get nodes -o wide
```
### 2. Check Cluster Health & Pods:

```bash
kubectl get pods --all-namespaces
```
### 3. Verify Installed Add-ons:

```bash
eksctl get addon --cluster drex-prod-cluster --region us-east-1
```
---

## 🧹 Teardown / Cleanup
To avoid unnecessary AWS charges, destroy all provisioned infrastructure when finished:

```bash
eksctl delete cluster -f .\eks-cluster.yaml
```
---

## Connect with me
* 💼 **LinkedIn:** [Divine Eric](https://www.linkedin.com/in/divine-eric-a06733373/)
* 📧 **Email:** [divineeric.service@gmail.com](divineeric.service@gmail.com)

