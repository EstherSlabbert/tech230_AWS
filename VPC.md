# VPC - Virtual Private Cloud

## Table of Contents
- [What is a VPC?]()
- [Setting up a VPC]()

## What is a VPC?

VPC stands for Virtual Private Cloud. It is a **virtual network infrastructure provided by cloud service providers** like Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP). A VPC **allows users to create and manage their own isolated virtual network environments** within a cloud provider's infrastructure.

When you create an AWS account it sets up a default VPC for you in every region.

![Alt text](/images/vpc_overview.png)

Here are some key characteristics and components of an AWS VPC:

**IP Address Range**: When creating a VPC, users define an IP address range in the form of a CIDR block (Classless Inter-Domain Routing). This range specifies the set of IP addresses that can be used within the VPC.

**Subnets**: VPCs are divided into one or more subnets, each associated with a specific availability zone (AZ) within a region. Subnets act as logical divisions within the VPC and allow users to isolate resources based on their needs.

**Route Tables**: Each subnet in a VPC is associated with a route table, which determines how traffic is routed within the VPC. Route tables define the rules for forwarding traffic between subnets or to external networks.

**Security Groups and Network Access Control Lists (ACLs)**: Security groups and ACLs provide network security for resources within the VPC. They control inbound and outbound traffic, allowing users to define fine-grained access rules.

**Internet Gateway**: An internet gateway enables communication between resources within the VPC and the internet. It serves as a bridge between the VPC and the public internet.

**Virtual Private Gateway**: A virtual private gateway allows for secure communication between a VPC and an on-premises network or other VPCs using VPN (Virtual Private Network) connections.

**Network Address Translation (NAT) Gateway**: A NAT gateway allows resources within private subnets to initiate outbound internet connections while keeping them hidden behind a public IP address.

**Peering Connections**: VPC peering allows the connection and communication between multiple VPCs, enabling resource sharing and network connectivity between them.

vpc = house

Private IP address range: 10.0.0.0
10.0.0.0/16 = CIDR block; works with bytes, gives space limits in CIDR block. /16 dictates that 10.0 = fixed; 0.0 ranges from 0-255.

rooms = public subnets (still uses a private ip range, but it is public as it will be accessed by a public ip at some point) 10.0.0.0/24 or 10.0.2.0/24 or any no.from 0-255 in the second last place.

Access:
you = ssh (port 22)
others -> access ports (HTTP (port 80)), (3000), (HTTPS = port 443)
with a public ip for app vm
outer door = internet gateway
path to access rooms = (public) route table -> directs traffic from the internet gateway to the subnet of the VM
door to room = security group (allows ports 80,3000,22)


db VM should have private subnet. 10.0.3.0/24. SG: 27017. has a route table to allow communication between rooms set up by default, which we do not have permissions to change. 

[AWS - What is CIDR?](https://aws.amazon.com/what-is/cidr/#:~:text=A%20CIDR%20block%20is%20a,regional%20internet%20registries%20(RIR)).

[SSH agent forwarding - SSH into private subnet](https://digitalcloud.training/ssh-into-ec2-in-private-subnet/#:~:text=You%20can%20SSH%20into%20EC2,located%20in%20a%20public%20subnet.)

## Setting Up a VPC

### Create VPC

### Create Internet Gateway

### Connect Internet Gateway to VPC

### Create public/private Subnet(s)

### Create (public) Route Table

### Explicitly connect Subnet to Route Table

### Link Route Table to Internet Gateway

### Create EC2 instance and link to VPC
