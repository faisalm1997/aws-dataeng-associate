# AWS Containers Overview

----

# Docker

Docker is a software development platform to deploy apps. Apps are packaged in containers that can be run on any OS and the apps run the same regardless of where they're run.  
A common use case is microservices architecture and lift-and-shift apps from on-premises to the cloud.

An EC2 instance could hold several Docker containers which run apps built in any language.

Docker images are stored in Docker repositories. Docker Hub is a public repo where users can find images for many technologies.

Amazon ECR is the 'Elastic Container Registry', which is a private repo and also has a public repo equivalent called the ECR Public Gallery.

---

## Docker vs VMs

Docker is a sort of virtualisation but not exactly. Resources are shared with the host just like in a VM, but containers sit on top of the Docker daemon on top of the host OS and infrastructure.

**Process:**  
`Dockerfile → build → Docker image → push & pull to/from Docker repository → run Docker image inside Docker container`

---

## Managing Docker Containers on AWS

AWS has many services to manage Docker containers and images:

1. **ECS:** AWS's own container platform  
2. **EKS:** AWS managed Kubernetes (open source)  
3. **Fargate:** AWS serverless container platform  
4. **ECR:** Where AWS stores container images  

---

# ECS (Elastic Container Service)

Elastic Container Service has launch types which launch Docker containers on AWS.

### Launch Types

#### EC2 Launch Type

- Launches ECS tasks on ECS clusters.
- Users provision and maintain the infrastructure.
- Each instance runs the ECS agent to register Docker images inside the ECS cluster.
- AWS manages starting and stopping of Docker containers.

#### Fargate Launch Type

- Launch Docker containers on AWS without provisioning infrastructure (serverless).
- Users create task definitions and AWS runs ECS tasks based on CPU/RAM needed.
- Scale by increasing the number of Fargate tasks.

---

## ECS - IAM Roles

1. **EC2 Instance Profile:**  
   Used by the ECS agent to make API calls to ECS, send container logs to CloudWatch, pull Docker images from ECR, and store/reference sensitive data in Secrets Manager or SSM.
2. **ECS Task Role:**  
   Each task has a specific role. Different roles are used for different ECS services running inside EC2. Defined in the task definition.

---

## ECS - Load Balancer Integration

1. **Application Load Balancer:** Supported and works on ECS.
2. **Network Load Balancer:** Recommended for high throughput and high performance use cases on ECS or with PrivateLink.
3. **Classic Load Balancer:** Not recommended but supported on ECS (not supported on Fargate).

---

## ECS - Data Volumes

- EFS file systems can be mounted onto ECS tasks (works for both EC2 and Fargate launch types).
- ECS tasks running in any AZ can share the same data in EFS.
- Fargate + EFS is serverless and can be used for persistent, multi-AZ shared storage for containers.

> **Note:** S3 cannot be mounted as a file system.

---

# ECR (Elastic Container Registry)

Elastic Container Registry is used to store and manage Docker images on AWS.  
- Has private and public repos (public repo has a public gallery for publishing Docker images).
- Fully integrated with ECS, backed by S3.
- Access to ECR/ECS is controlled via IAM.
- An ECS cluster can have EC2 instances which pull and push Docker images to the ECR repo, all managed by an IAM role.
- ECR supports image scanning, vulnerability detection, lifecycles, and image tags.

---

# EKS (Elastic Kubernetes Service)

Elastic Kubernetes Service is a managed Kubernetes service on AWS.

- Kubernetes (K8s) is an open source system to deploy, scale, and manage containerised apps.
- Alternative to ECS but with a different API.
- EKS supports EC2 (for worker nodes) or Fargate (for serverless containers).
- Kubernetes is cloud agnostic.

**Hierarchy:**  
Worker nodes → EKS node → EKS pods

- Users can assign taints and tolerations to ensure pods are scheduled on the desired node groups based on labels.

---

## EKS - Node Types

1. **Managed Node:**  
   EKS creates and manages EC2 nodes for users. Nodes are part of an Auto Scaling Group managed by EKS. Supports on-demand and spot instances.
2. **Self-Managed Nodes:**  
   Nodes are created by the user, registered to the EKS cluster, and managed by an Auto Scaling Group. Users can use a pre-built AMI. Supports on-demand or spot instances.
3. **Fargate:**  
   No maintenance required; nodes are not managed.

---

## EKS - Data Volumes

- For storage in EKS, specify a storage class manifest on the EKS cluster.
- EKS uses a Container Storage Interface (CSI) compliant driver.
- Supports EBS, EFS (works with Fargate), FSx Lustre, FSx for NetApp ONTAP.

---