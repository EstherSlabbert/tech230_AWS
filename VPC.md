# VPC - Virtual Private Cloud

## Table of Contents
- [What is a VPC?](#what-is-a-vpc)
- [Setting up a VPC](#setting-up-a-vpc)
  - [Create VPC](#create-vpc)
  - [Create Internet Gateway](#create-internet-gateway)
  - [Connect Internet Gateway to VPC](#connect-internet-gateway-to-vpc)
  - [Create public/private Subnet(s)](#create-public-private-subnets)
  - [Create (public) Route Table](#create-public-route-table)
  - [Explicitly connect Subnet to Route Table](#explicitly-connect-subnet-to-route-table)
  - [Link Route Table to Internet Gateway](#link-route-table-to-internet-gateway)
  - [Create EC2 instance and link to VPC](#create-ec2-instance-and-link-to-vpc)

## What is a VPC? <a id="what-is-a-vpc">What is a VPC?</a>

VPC stands for Virtual Private Cloud. It is a **virtual network infrastructure provided by cloud service providers** like Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP). A VPC **allows users to create and manage their own isolated virtual network environments** within a cloud provider's infrastructure.

When you create an AWS account it sets up a default VPC for you in every region.

![Alt text](/images/vpc_overview_pic.png)

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


db VM should have private subnet (e.g. 10.0.3.0/24). SG: 27017. has a route table to allow communication between rooms set up by default, which we do not have permissions to change. 

[AWS - What is CIDR?](https://aws.amazon.com/what-is/cidr/#:~:text=A%20CIDR%20block%20is%20a,regional%20internet%20registries%20(RIR)).

[SSH agent forwarding - SSH into private subnet](https://digitalcloud.training/ssh-into-ec2-in-private-subnet/#:~:text=You%20can%20SSH%20into%20EC2,located%20in%20a%20public%20subnet.)

## Setting Up a VPC <a id="setting-up-a-vpc">Setting Up a VPC</a>

### Create VPC <a id="create-vpc">Create VPC</a>

Navigate to 'Your VPCs' and 'Create VPC'. Select 'VPC only', name it appropriately, manually input the IPv4 CIDR block with the desired values, then 'Create VPC'.

![Alt text](/images/vpc1.png)

![Alt text](/images/vpc2.png)

### Create Internet Gateway <a id="create-internet-gateway">Create Internet Gateway</a>

Navigate to 'Internet Gateways' and 'Create internet gateway'. Name it appropriately then 'Create internet gateway'.

<img src="/images/ig.png"  width="100%" height="100%">

### Connect Internet Gateway to VPC <a id="connect-internet-gateway-to-vpc">Connect Internet Gateway to VPC</a>

After creating the Internet Gateway you will return to the 'Internet Gateways' page where there will be a green bar at the top, which has the button 'Attach to a VPC', click this. Search for the VPC you created and named and select it then click 'Attach internet gateway'.

![Alt text](/images/ig-vpc1.png)

<img src="/images/ig-vpc2.png"  width="100%" height="100%">

### Create public/private Subnet(s) <a id="create-public-private-subnets">Create public/private Subnet(s)</a>

Navigate to 'Subnets' and 'Create subnet'. Name it appropriately. You may wish to select an Availability Zone, or leave it as 'No preference'. Enter an appropriate IPv4 CIDR (ensure it is different for each subnet you create). Create as many subnets as you require. Then 'Create subnet'.

<img src="/images/sub1.png"  width="100%" height="100%">

<img src="/images/sub2.png"  width="100%" height="100%">

### Create (public) Route Table <a id="create-public-route-table">Create (public) Route Table</a>

Navigate to 'Route Tables' and 'Create route table'. Name it appropriately, then select the VPC you created and named, then 'Create route table'.

<img src="/images/rt1.png"  width="100%" height="100%">

<img src="/images/rt2.png"  width="100%" height="100%">

### Explicitly connect Subnet to Route Table <a id="explicitly-connect-subnet-to-route-table">Explicitly connect Subnet to Route Table</a>

Navigate to 'Route Tables' scroll down and select the 'Subnet Associations' tab, then 'Edit Subnet Associations'. Check the subnet you want to create a link to then 'Save associations'.

_Note_: Only link the public subnet (app VM's subnet) with public route table.

<img src="/images/sub-rt1.png"  width="100%" height="100%">

<img src="/images/sub-rt2.png"  width="100%" height="100%">

### Link Route Table to Internet Gateway <a id="link-route-table-to-internet-gateway">Link Route Table to Internet Gateway</a>

Navigate to 'Route Tables' scroll down and select the 'Routes' tab, then 'Edit Routes'. Then 'Add Route' and add your Destination to '0.0.0.0/0' and your Target as your created Internet Gateway that you named appropriately.

<img src="/images/rt-ig1.png"  width="100%" height="100%">

<img src="/images/rt-ig2.png"  width="100%" height="100%">

### Create EC2 instance and link to VPC <a id="create-ec2-instance-and-link-to-vpc">Create EC2 instance and link to VPC</a>

Create EC2 instance(s) - you can use an AMI to do this - and place VM(s) in the subnet(s). (You can use User Data to set up your EC2). See information to create an EC2 instance here: [Create and EC2 instance](https://github.com/EstherSlabbert/tech230_AWS/blob/main/aws_ec2_instances_and_amis.md#create-ec2-instance).

Edit network settings when creating EC2 instance. Enable assigning a public IP for the public subnet VM.

_Note_: Existing Security Groups will not work with a created VPC; you must create a new Security Group with rules for the required ports and a descriptive name.

<img src="/images/ec2-nw1.png"  width="100%" height="100%">

<img src="/images/ec2-nw2.png"  width="100%" height="100%">

<img src="/images/ec2-nw3.png"  width="100%" height="100%">

