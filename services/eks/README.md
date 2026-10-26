# Amazon EKS

[Back to index](../../README.md)

## Overview

- Managed service runs **Kubernetes on AWS** without needing to manage the control plane itself

- Integration: **ECR** (container images), **ELB** (load balancing), **IAM** (auth), **VPC** (isolation)

---

## Architecture

### Control Plane

- Runs on private EC2 instances, **single-tenant**, spanning **multiple AZs**

- Fronted by **ELB Network Load Balancer**

- Encrypt etcd data using **AWS KMS**

- Default log is **not sent** to CloudWatch → must enable each type manually

- Authentication: **IAM/OIDC** \| Authorization: **Kubernetes RBAC**

### Nodes

- Must be in the same VPC as the subnets selected when creating the cluster

- API server endpoint **public by default**, private access can be enabled

### Autoscaling

| Type | Mechanism |
|----|----|
| **Cluster Autoscaler** | Use AWS Auto Scaling Groups |
| **Karpenter** | Work directly with EC2 Fleet |
| **Vertical Pod Autoscaler** | Adjust pods CPU/memory |
| **Horizontal Pod Autoscaler** | Adjust the number of pods according to CPU utilization |

---

## Node types

### Self-managed Nodes

- Node group = 1 or more EC2s in **Auto Scaling Group**

- In a node group: same **instance type, AMI, IAM role**

- Cluster can have many node groups with different instance types

- Update by: migrating to a new node group or updating in-place

### Managed Node Groups

- AWS automates provisioning and lifecycle management

- Automatically tagged to be detected by **Cluster Autoscaler**

- Supports **On-Demand or Spot instances**

- Node updates/terminations automatically **drain nodes** to ensure availability

### AWS Fargate

- Must define **Fargate profile** before scheduling pods

- Fargate profile is **immutable**, including: Pod execution role, Subnets, Selectors, Namespace, Labels

- Run **1 pod/node**

- Storage ephemeral, encrypted with **Fargate managed keys**

- If a pod matches multiple profiles, profile selection rules determine placement; explicitly select a matching profile when deterministic placement is required.

---

## EKS Auto Mode

- For clusters running **Kubernetes 1.29+**

- AWS fully automates: provisioning, scaling, add-ons, cost optimization

- Reduces infrastructure management for new clusters; suitability depends on workload requirements.

---

## EKS Connector

- Register and connect **any Kubernetes cluster** to AWS

- View status, config, workloads on EKS console

---

## Storage and CSI drivers

| Driver | Notes |
|----|----|
| **EBS CSI** | Needs IAM permissions; Cannot mount to Fargate pods |
| **EFS CSI** | Fargate only supports **static provisioning**; Windows containers are not supported |
| **FSx for Lustre** | Fargate **not supported** |
| **FSx for NetApp ONTAP** | Fully managed ONTAP file systems |
| **FSx for OpenZFS** | FSx OpenZFS lifecycle management |
| **Amazon File Cache** | High-speed cache for S3 or other file systems |

---

## Networking

### VPC Configurations

| Type | Features |
|----|----|
| **Private subnets** | Nodes use NAT or service endpoints for required outbound access; API endpoint access is configured separately |
| **Public subnets** | Nodes can use public IPv4 addresses where configured; API endpoint exposure is configured separately |
| **Public + Private** | Nodes are private, Load Balancer is public (**recommended**) |

### Pod Networking (VPC CNI Plugin)

- Assign **private IPv4/IPv6** from VPC to each pod

- Runs as **Daemonset** named `aws-node`

- Includes two components: **IPAM daemon** (IP management) + **CNI plugin** (network configuration)

- **Does not support dual-stack** (IPv4 + IPv6 simultaneously) for pods/services

- **Multus CNI**: attach multiple network interfaces to 1 pod

- **Security groups for pods**: control inbound/outbound traffic

### Add-ons Network

| Add-ons | Role |
|----|----|
| **CoreDNS** | DNS service in cluster (default **2 replicas**) |
| **Kube-proxy** | Maintain network rules on EC2 nodes |
| **Calico** | Network policy engine, network segmentation |
| **AWS Load Balancer Controller** | Create **ALB** (Ingress) or **NLB** (LoadBalancer service) |

---

## Security

### IAM Policies required

| Role | Policies |
|----|----|
| **EKS Cluster** | `AmazonEKSClusterPolicy` |
| **Worker Node** | `AmazonEKSWorkerNodePolicy` + `AmazonEC2ContainerRegistryReadOnly` + `AmazonEKS_CNI_Policy` |
| **Fargate Pod** | `AmazonEKSFargatePodExecutionRole` |

### Authentication & Authorization

- In the legacy authentication model, the cluster creator receives administrative Kubernetes access.

- Legacy clusters map IAM identities through **aws-auth ConfigMap** and Kubernetes role bindings; **EKS access entries** provide another supported access-management model.

- **EKS Pod Identity**: assign IAM role to Kubernetes service account, **no need for OIDC provider**

- Encrypt Kubernetes secrets with **AWS KMS (envelope encryption)**

- **ASCP**: displays secrets from Secrets Manager / SSM Parameter Store as files mounted into pods

- **AWS PrivateLink**: access EKS management APIs in VPC

---

## Monitoring & Logging

### Control plane log types

- API server (`kube-apiserver`)

- Audit (`kube-apiserver-audit`)

- Authenticator

- Controller manager (`kube-controller-manager`)

- Scheduler (`kube-scheduler`)

> Logs sent to **CloudWatch Logs** according to each log stream per cluster

- **CloudTrail**: records all API calls (IAM user, role, federated user, AWS service)

- **EKS Dashboard**: centralized view of all clusters/node groups/add-ons cross-account and cross-region

---

## Deployment Options

| Options | Description |
|----|----|
| **EKS on AWS** | Managed/self-managed EC2 or Fargate, completely in the cloud |
| **EKS on Outposts** | Data plane on-premises on Outpost rack, control plane on AWS |
| **EKS Anywhere** | Everything (hardware, control plane, data plane) is controlled by you, with AWS support |
| **EKS Distro** | Open-source Kubernetes distribution like EKS, **without** AWS support |
| **EKS Hybrid Nodes** | On-premises/edge nodes connect to managed EKS control plane on AWS |

---

## Pricing

- Charge **hourly** for each EKS cluster

- **EC2 nodes**: charged according to EC2 resources used

- **Fargate**: charged by **vCPU + memory**

- **Outposts**: the source describes an hourly cluster fee with no separate EKS worker-node fee; Outposts infrastructure charges still apply.

- **Standard support**: **14 months** per Kubernetes version

- **Extended support**: add up to **12 months** (total **26 months**) at a higher fee


## Notes

### Scaling pods and nodes together

During traffic spikes such as a Black Friday sale, scale at two levels:

1. **Pod capacity:** Horizontal Pod Autoscaler uses metrics, commonly provided by Metrics Server, to adjust replica count based on CPU or memory targets.
2. **Node capacity:** A node autoscaler provisions capacity when pods cannot be scheduled. For example, creating 100 pods in a cluster with only two full nodes leaves pods `Pending` until more capacity is available.

Cluster Autoscaler adjusts configured Auto Scaling Groups. Karpenter provisions suitable EC2 capacity directly based on pod resource requests, such as 2 vCPUs and 4 GiB of memory, reducing the need to maintain many fixed node groups. Actual launch time and cost depend on available capacity and configuration.

### Carrier-edge workloads

AWS Wavelength places AWS compute within supported telecommunications networks for latency-sensitive mobile applications. An EKS workload targeting the carrier's 5G edge uses worker nodes in a **Wavelength Zone**. This shortens the network path compared with routing mobile traffic through the public internet to a Region; single-digit millisecond latency is a design goal rather than a universal guarantee.

Outposts supports on-premises deployment, while Local Zones place AWS infrastructure in selected metropolitan areas. The source's Wavelength example uses the legacy `aws-auth ConfigMap` to map IAM users or roles to Kubernetes identities and RBAC groups. Grant only the required permissions rather than mapping every user to `system:masters`.

### Pod subnets and network controls

The Amazon VPC CNI plugin gives pods addresses from VPC subnets, enabling private communication through VPC routing. Configure custom networking when pods must use designated subnets. Security groups, network ACLs, and route tables provide VPC controls. Kubernetes NetworkPolicy controls allowed pod traffic; it does not select a pod's subnet.

### Managed nodes with Spot capacity

For **stateless end-of-day processing** that can be retriggered after failure, managed node groups can use Spot Instances while automating node provisioning and lifecycle operations. Running and managing containers directly on an EC2 Auto Scaling Group requires more container-orchestration work.
