# VPC - Virtual Private Cloud

## Table of Contents
- [What is a VPC?](#what-is-a-vpc)
  - [Analogy](#analogy)
  - [CIDR](#cidr)
- [Setting up a VPC](#setting-up-a-vpc)
  - [1. Create VPC](#create-vpc)
  - [2. Create Internet Gateway](#create-internet-gateway)
  - [3. Connect Internet Gateway to VPC](#connect-internet-gateway-to-vpc)
  - [4. Create public/private Subnet(s)](#create-public-private-subnets)
  - [5. Create (public) Route Table](#create-public-route-table)
  - [6. Explicitly connect Subnet to Route Table](#explicitly-connect-subnet-to-route-table)
  - [7. Link Route Table to Internet Gateway](#link-route-table-to-internet-gateway)
  - [8. Create EC2 instance and link to VPC](#create-ec2-instance-and-link-to-vpc)

## <a id="what-is-a-vpc">What is a VPC?</a>

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

#### <a id="analogy">Analogy</a>

A VPC is a network that is like a secure building. It has a Private IP address, which is indicated by a CIDR block (e.g. 10.0.0.0/16), which specifies the size of the network in variable-sized blocks and allows for flexible allocation of IP addresses.

The front door or gate is like the Internet Gateway, which provides a controlled point of entry and exit for traffic to and from the VPC through the internet.

From the front door there is a path to certain rooms, which is like a (public) Route Table. The Route Table directs traffic from the Internet Gateway to Subnets containing different resources (i.e. VMs, containers, servers etc.).

Subnets are like the rooms with in the building. They are also indicated by a CIDR block, e.g. 10.0.2.0/24. (Note: public subnets still uses a private IP range, but it is classified as public because it will be accessed by a public IP at some point).
Subnets can be divided into 2 different kinds. There are more secure rooms, which are like vaults which can be compared to private subnets and less secure rooms which are like public subnets. The public subnets can be accessed following the requirements of the Internet Gateway, the Route Table and then the VM can be accessed if the Security Group, which are like the door to a room or the key to opening a box, rules are passed. The private subnets cannot be accessed by outside parties outside of some very specific conditions.

AWS automatically sets up a path between subnets (rooms) within a VPC (building) also called a Route Table. We are not able to change this Route Table.

Access:
As a developer you want to access the VM in the subnet through an SSH using port 22.
Others want to access HTTP = port 80, Sparta app port = 3000, possibly an HTTPS = port 443 with a public IP for app VM.

(db VM should have private subnet (e.g. 10.0.3.0/24) & SG for port 27017. app VM should have public subnet (e.g. 10.0.2.0/24) & SG for ports 80, 22 & 3000.)

#### <a id="cidr">CIDR</a>

CIDR stands for Classless Inter-Domain Routing. A CIDR block is a notation used to define the network portion of an IP address and specify the size of the network. CIDR allows for flexible allocation of IP addresses and efficient routing by aggregating multiple IP addresses into a single block. It simplifies IP address management and enables efficient allocation of IP addresses within a network.

works with bytes, gives space limits in CIDR block. /16 dictates that 10.0. = fixed; 0.0 can range from 0-255.
10.0.0.0/24 or 10.0.2.0/24 or any no.from 0-255 in the second last place.

[AWS - What is CIDR?](https://aws.amazon.com/what-is/cidr/#:~:text=A%20CIDR%20block%20is%20a,regional%20internet%20registries%20(RIR)).

[SSH agent forwarding - SSH into private subnet](https://digitalcloud.training/ssh-into-ec2-in-private-subnet/#:~:text=You%20can%20SSH%20into%20EC2,located%20in%20a%20public%20subnet.)

## <a id="setting-up-a-vpc">Setting Up a VPC</a>

### <a id="create-vpc">1. Create VPC</a>

1. Navigate to 'Your VPCs' and 'Create VPC'.
2. Select 'VPC only', name it appropriately.
3. Manually input the IPv4 CIDR block with the desired values, then 'Create VPC'.

<img src="/images/vpc1.png"  width="60%" height="60%">

<img src="/images/vpc2.png"  width="60%" height="60%">

### <a id="create-internet-gateway">2. Create Internet Gateway</a>

1. Navigate to 'Internet Gateways' and 'Create internet gateway'.
2. Name it appropriately then 'Create internet gateway'.

<img src="/images/ig.png"  width="60%" height="60%">

### <a id="connect-internet-gateway-to-vpc">3. Connect Internet Gateway to VPC</a>

1. After creating the Internet Gateway you will return to the 'Internet Gateways' page where there will be a green bar at the top, which has the button 'Attach to a VPC', click this. Alternatively, go to the Internet Gateway then select the 'Actions' drop down and click 'Attach to a VPC'.
2. Search for the VPC you created and named and select it then click 'Attach internet gateway'.

<img src="/images/ig-vpc1.png"  width="60%" height="60%">

<img src="/images/ig-vpc2.png"  width="60%" height="60%">

### <a id="create-public-private-subnets">4. Create public/private Subnet(s)</a>

1. Navigate to 'Subnets' and 'Create subnet'.
2. Name it appropriately.
3. You may wish to select an Availability Zone, or leave it as 'No preference'.
4. Enter an appropriate IPv4 CIDR (_Note_: ensure IPv4 CIDR is different for each subnet you create).
5. Create as many subnets as you require by clicking 'add subnet' and following steps 2-4.
6. Then click 'Create subnet'.

<img src="/images/sub1.png"  width="60%" height="60%">

<img src="/images/sub2.png"  width="60%" height="60%">

### <a id="create-public-route-table">5. Create (public) Route Table</a>

1. Navigate to 'Route Tables' and 'Create route table'.
2. Name it appropriately, then select the VPC you created and named, then 'Create route table'.

<img src="/images/rt1.png"  width="60%" height="60%">

<img src="/images/rt2.png"  width="60%" height="60%">

### <a id="explicitly-connect-subnet-to-route-table">6. Explicitly connect Subnet to Route Table</a>

1. Navigate to 'Route Tables' scroll down and select the 'Subnet Associations' tab, then 'Edit Subnet Associations'.
2. Check the subnet you want to create a link to then 'Save associations'.

_Note_: Only link the public subnet (app VM's subnet) with public route table.

<img src="/images/sub-rt1.png"  width="60%" height="60%">

<img src="/images/sub-rt2.png"  width="60%" height="60%">

### <a id="link-route-table-to-internet-gateway">7. Link Route Table to Internet Gateway</a>

1. Navigate to 'Route Tables' scroll down and select the 'Routes' tab, then 'Edit Routes'.
2. Then 'Add Route' and add your 'Destination' to '0.0.0.0/0' and your 'Target' as your created Internet Gateway that you named.

<img src="/images/rt-ig1.png"  width="60%" height="60%">

<img src="/images/rt-ig2.png"  width="60%" height="60%">

### <a id="create-ec2-instance-and-link-to-vpc">8. Create EC2 instance and link to VPC</a>

1. Create EC2 instance(s) or VM(s). _Note_: you can use an AMI to do this and add 'User Data' if necessary.
See information to create an EC2 instance here: [Create and EC2 instance](https://github.com/EstherSlabbert/tech230_AWS/blob/main/aws_ec2_instances_and_amis.md#create-ec2-instance).

2. While setting up your EC2 edit 'Network Settings':
  2.1. Select your created VPC, rather than the Default VPC.
  2.2. Enable assigning a public IP for the public subnet VM.
    
  <img src="/images/ec2-nw1.png"  width="60%" height="60%">
  
  2.3. Create a Security Group with the needed rules.

<img src="/images/ec2-nw2.png"  width="60%" height="60%">

<img src="/images/ec2-nw3.png"  width="60%" height="60%">

_Note_: Existing Security Groups will not work with a created VPC; you must create a new Security Group with rules for the required ports and a descriptive name.

3. Launch your EC2 instance (VM) and you should be able to access the webserver that it is running, provided it is a public subnet that you connected your VM to.


