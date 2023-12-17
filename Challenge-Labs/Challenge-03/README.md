# Set Up and Configure a Cloud Environment in Google Cloud

### Tasks:
1.
2.
3. Multiple VPC Networks
4. Managing Deployments Using Kubernetes Engine 


### 1.

### 2.

### 3. Multiple VPC Networks
#### Overview
In this lab you create several VPC networks and VM instances and test connectivity across networks. Specifically, you create two custom mode networks (managementnet and privatenet) with firewall rules and VM instances as shown in this network diagram:
![Alt text](./image.png)

The **mynetwork** network with its firewall rules and two VM instances (**mynet-{Region}-vm** and **mynet-{Region}-vm**) have already been created for you for this lab.

#### Objectives
In this lab, you will learn how to perform the following tasks:

 - Create custom mode VPC networks with firewall rules
 - Create VM instances using Compute Engine
 - Explore the connectivity for VM instances across VPC networks
 - Create a VM instance with multiple network interfaces

#### Task 1. Create custom mode VPC networks with firewall rules
Create two custom networks **managementnet** and **privatenet**, along with firewall rules to allow **SSH**, **ICMP**, and **RDP** ingress traffic.

**Create the managementnet network**

Create the managementnet network using the Cloud Console.

1. In the Cloud Console, navigate to Navigation menu (**Navigation menu** ) > **VPC network** > **VPC networks**.

2. Notice the **default** and **mynetwork** networks with their subnets.

   Each Google Cloud project starts with the **default** network. In addition, the **mynetwork** network has been premade as part of your network diagram.

3. Click **Create VPC Network**.

4. Set the Name to ``managementnet``.

5. For **Subnet creation mode**, click **Custom**.

6. Set the following values, leave all other values at their defaults:

**Create the privatenet network**
**Create the firewall rules for managementnet**
**Create the firewall rules for privatenet**

#### Task 2. Create VM instances
Create two VM instances:
-
-

**Create the managementnet--vm instance**
**Create the privatenet--vm instance**

#### Task 3. Explore the connectivity between VM instances

Explore the connectivity between the VM instances. Specifically, determine the effect of having VM instances in the same zone versus having instances in the same VPC network.

**Ping the external IP addresses**

Ping the external IP addresses of the VM instances to determine if you can reach the instances from the public internet.

**Ping the external IP addresses**

#### Task 4. Create a VM instance with multiple network interfaces

Every instance in a VPC network has a default network interface. You can create additional network interfaces attached to your VMs. Multiple network interfaces enable you to create configurations in which an instance connects directly to several VPC networks (up to 8 interfaces, depending on the instance's type).

**Create the VM instance with multiple network interfaces**
**Explore the network interface detail**
**Explore the network interface connectivity**

### 4. Managing Deployments Using Kubernetes Engine 
#### Overview

Dev Ops practices will regularly make use of multiple deployments to manage application deployment scenarios such as "Continuous deployment", "Blue-Green deployments", "Canary deployments" and more. This lab provides practice in scaling and managing containers so you can accomplish these common scenarios where multiple heterogeneous deployments are being used.
Objectives

- Practice with kubectl tool
- Create deployment yaml files
- Launch, update, and scale deployments
- Practice with updating deployments and deployment styles

##### Introduction to deployments

Heterogeneous deployments typically involve connecting two or more distinct infrastructure environments or regions to address a specific technical or operational need. Heterogeneous deployments are called "hybrid", "multi-cloud", or "public-private", depending upon the specifics of the deployment.

For the purposes of this lab, heterogeneous deployments include those that span regions within a single cloud environment, multiple public cloud environments (multi-cloud), or a combination of on-premises and public cloud environments (hybrid or public-private).

Various business and technical challenges can arise in deployments that are limited to a single environment or region:

- **Maxed out resources**: In any single environment, particularly in on-premises environments, you might not have the compute, networking, and storage resources to meet your production needs.
- **Limited geographic reach**: Deployments in a single environment require people who are geographically distant from one another to access one deployment. Their traffic might travel around the world to a central location.
- **Limited availability**: Web-scale traffic patterns challenge applications to remain fault-tolerant and resilient.
- **Vendor lock-in**: Vendor-level platform and infrastructure abstractions can prevent you from porting applications.
- **Inflexible resources**: Your resources might be limited to a particular set of compute, storage, or networking offerings.

Heterogeneous deployments can help address these challenges, but they must be architected using programmatic and deterministic processes and procedures. One-off or ad-hoc deployment procedures can cause deployments or processes to be brittle and intolerant of failures. Ad-hoc processes can lose data or drop traffic. Good deployment processes must be repeatable and use proven approaches for managing provisioning, configuration, and maintenance.

Three common scenarios for heterogeneous deployment are multi-cloud deployments, fronting on-premises data, and continuous integration/continuous delivery (CI/CD) processes.

The following exercises practice some common use cases for heterogeneous deployments, along with well-architected approaches using Kubernetes and other infrastructure resources to accomplish them.

#### Get sample code for this lab

1. Get the sample code for creating and running containers and deployments:

```
gsutil -m cp -r gs://spls/gsp053/orchestrate-with-kubernetes .
cd orchestrate-with-kubernetes/kubernetes
```

2. Create a cluster with 3 nodes (this will take a few minutes to complete):

```
gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
```

#### Task 1. Learn about the deployment object
#### Task 2. Create a deployment
#### Task 3. Rolling update
#### Task 4. Canary deployments
#### Task 5. Blue-green deployments