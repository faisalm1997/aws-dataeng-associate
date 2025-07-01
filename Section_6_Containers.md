# Docker

Docker is a software development platform to deploy apps. Apps are packaged in containers that can be run on any OS and the apps run same regardless of where they're run. A common use case is microservices architecture, lift-and-shift apps from on premises to the cloud. 

An EC2 instance could hold several docker containers which run apps in built in any language. 

Docker images are stored in docker repositories, docker hub is a public repo where users can find images for many technologies. 

Amazon ECR is the 'elastic container registry' which is a private repo and does have a public repo equivalent called the ECR Public gallery. 

## Docker vs VMs

Docker is a sort of virtualisation but not exactly. Resources are shared with the host just like in a VM but containers sit on top of the docker daemon on top of the host OS and infrastructure. 

Process: dockerfile > build > docker image > push & pull to and from docker repository > run docker image inside docker container 

## Managing docker containers on AWS 

AWS has many services to manage docker containers and images 

1. ECS - AWS own container platform 
2. EKS - AWS managed kubernetes which is open source 
3. Fargate - AWS serverless container platform 
4. ECR - Where AWS stores container images 

# ECS

Elastic container service has launch types which launches docker containers on AWS 

## Launch Types
### EC2 Launch type 

This launch type launches ECS tasks on ECS clusters - users can provision and maintain the infra using this launch type with each instance running the ECS agwnt to register a docker images inside the ECS cluster. AWS manages the starting and stopping of the docker containers. 

### Fargate Launch type 

To launch docker containers on AWS - this time we do not need to provision any infra such as EC2 instances to manage as its all serverless. Users create task definitions and AWS runs the ECS tasks based on the CPU/RAM needed. Users can scale this by increasing the number of fargate tasks. 

## ECS - IAM Roles

1. EC2 instance profile: this profile is used by the ECS agent and can make API calls to the ECS service, send container logs to cloudwatch and pull docker images from ECR. It can also store/reference sensitive data in secrets manager or SSM. 
2. ECS task role: Each task has a specific role, different roles are used for different ECS services which are run inside the EC2. A task role is defined in the task definition.

## ECS - Load balancer integration 

1. Application load balancer - is supported and works on ECS 
2. Network load balancer - recommended for high throughput and high performance use cases on ECS or to use with privatelink 
3. Classic load balance - not recommended but supported on ECS (no fargate)

## ECS - Data Volumes 

EFS file systems can be mounted onto ECS tasks with works for both EC2 and fargate launch types. ECS tasks which are running in any AZ can share the same data in EFS. 

Fargate + EFS combination is serverless and can be used for persistent multi AZ shared storage for containers.

Note: S3 cannot be mounted as a file system

# ECR

Elastic container registry which is used to store and manage docker images on AWS. Has a private and public repo - public repo has a public gallery where you can publish docker images. 

ECR is fully integrated with ECS, backed by S3. Access to ECR/ECS is controlled via IAM - an ECS cluster can have EC2 instance which pulls and pushes docker images to the ECR repo all managed by an IAM role. ECR supports image scanning, vulnerabilities, lifecycles and image tags. 

# EKS 

Elastic kubernetes service - launching a managed kubernetes service on AWS. 

K8s is an open source system which is used to deploy, scale and manage containerised apps. An alternative to ECS but different API, EKS supports EC2 if you want to deploy worker nodes or fargate to deploy serverless containers. K8s is cloud agnostic. 

Worker nodes > EKS node > EKS pods 

Users can assign taints and tolerations to ensure pods are schedules and the desired node groups are used based on labels. 

## EKS - Node Types 

1. Managed node - creates and manages nodes which are EC2 instances for the users, nodes are part of the ASG managed by EKS. They support on demand and spot instances
2. Self managed nodes - nodes which are created by the user, and registered to the EKS cluster and managed by an ASG. Users can use a pre built AMI. Supports on demand or spot instances. 
3. Fargate - no maintenance required and nodes are not managed. 

## EKS - Data volumes 

For storage in EKS - the user needs to specify a storageclass manifest on the EKS cluster. EKS uses container storage interface compliant driver. There is support for EBS, EFS (works with fargate), FSx Lustre, FSx for NetApp ONTAP. 