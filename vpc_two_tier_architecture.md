# VPC 2-tier architecture

If you want more information on VPCs go to this markdown file: [VPC](https://github.com/EstherSlabbert/tech230_AWS/edit/main/VPC.md).

Disclaimer: All images may not match the IDs and names exactly as this process was repeated for testing until it was successful.

## Table of Contents
- [Setting up a two tier architecture in a VPC](#setting-up-a-two-tier-architecture-in-a-vpc)
  - [1. Create a VPC](#create-a-vpc)
  - [2. Create an Internet Gateway](#create-an-internet-gateway)
  - [3. Connect the Internet Gateway to the VPC](#connect-the-internet-gateway-to-the-vpc)
  - [4. Create Subnets](#create-subnets)
  - [5. Create public Route Table](#create-public-route-table)
  - [6. Explicitly connect Subnet(s) to Route Table(s)](#explicitly-connect-subnets-to-route-tables)
  - [7. Connect Route Table(s) to the Internet Gateway](#connect-route-table-to-the-internet-gateway)
  - [8. Create EC2 instances and configure them into the VPC](#create-ec2-instances-and-configure-them-into-the-vpc)
  - [Results](#results)

## <a id="setting-up-a-two-tier-architecture-in-a-vpc">Setting up a two tier architecture in a VPC</a>

- _What is a two tier architecture?_

A two-tier architecture is a software design pattern that separates an application into two main layers: the **presentation tier** (front-end) and the **data/application tier** (back-end). The presentation tier handles user interaction and displays information, while the data/application tier handles data storage, business logic, and processing. This architecture provides a clear separation of concerns, simplifies maintenance, and enables scalability and flexibility in building and deploying applications.

- _Why would you want to use a VPC two-tier architecture?_
 
Using a Virtual Private Cloud (VPC) for a two-tier architecture offers enhanced **security**, **network isolation**, **scalability**, and **control**. It enables you to define security policies, scale your architecture, and establish secure connectivity with other resources. VPC provides a **robust** foundation for building resilient and **highly available** architectures while meeting compliance and regulatory requirements.

### <a id="create-a-vpc">1. Create a VPC</a>

1. Navigate to 'VPC' > 'Your VPCs' and click 'Create VPC'.
2. Select 'VPC only', name it appropriately.
3. Manually input the IPv4 CIDR block with the desired values (i.e. 10.0.0.0/16).
![Create VPC1](/images/1.png)
4. Then click 'Create VPC'.
![Create VPC2](/images/2.png)
You should get:
![Create VPC3](/images/3.png)

### <a id="create-an-internet-gateway">2. Create an Internet Gateway</a>

1. Navigate to 'VPC' > 'Internet gateways' and click 'Create internet gateway'.
![Create internet gateway1](/images/4.png)
2. Name it appropriately, then click 'Create internet gateway'.
![Create internet gateway2](/images/5.png)

### <a id="connect-the-internet-gateway-to-the-vpc">3. Connect the Internet Gateway to the VPC</a>

You should get:
![Internet gateway](/images/6.png)
1. Click 'Attach to VPC' either from the 'Actions' drop down or from the green bar at the top of the screen.
2. Search of your VPC by name and select it. Then click 'Attach internet gateway'.
![Attach internet gateway](/images/7.png)
You should get:
![Internet gateway attached](/images/8.png)

### <a id="create-subnets">4. Create Subnets</a>

1. Navigate to 'VPC' > 'Subnets' and click 'Create subnet'.
![Create Subnets1](/images/9.png)
2. Select VPC under the 'VPC' heading. Once you do so you should be able to see the 'Subnet settings' if you scroll down a bit.
![Create Subnets2](/images/10.png)
3. Under 'Subnet settings' name your public subnet appropriately and give it a IPv4 CIDR (i.e. 10.0.2.0/24). You may choose which Availability Zone to place it in (i.e. 1a).
![Create Subnets3](/images/11.png)
4. Under 'Subnet settings' click 'Add new subnet'. A new Subnet block should appear.
5. Name your private subnet appropriately and give it a IPv4 CIDR (i.e. 10.0.3.0/24). You may choose which Availability Zone to place it in (i.e. 1b).
6. Then click 'Create subnet'.
![Create Subnets4](/images/12.png)
You should get:
![Create Subnets5](/images/13.png)

### <a id="create-public-route-table">5. Create public Route Table</a>

1. Navigate to 'VPC' > 'Route Tables' and click 'Create route table'.
![Create RT1](/images/14.png)
2. Name the route table appropriately (i.e. tech230-esther-two-tier-public-RT).
![Create RT2](/images/15.png)
3. Search of your VPC by name and select it. Then click 'Create route table'.
You should get:
![Create RT3](/images/16.png)

### <a id="explicitly-connect-subnets-to-route-tables">6. Explicitly connect Subnet(s) to Route Table(s)</a>

1. While still on your route table page scroll down and select the 'Subnet associations' tab.
![Connect RT to Subnet1](/images/17.png)
2. Click 'Edit subnet associations'.
3. Select the public subnet you want associated, then click 'Save associations'.
![Connect RT to Subnet2](/images/18.png)
You should get:
![Connect RT to Subnet3](/images/19.png)

### <a id="connect-route-table-to-the-internet-gateway">7. Connect Route Table(s) to the Internet Gateway</a>

1. While still on your route table page scroll down and select the 'Routes' tab.
2. Click 'Edit routes'.
3. Then click 'Add route' and set the 'Destination' to 0.0.0.0/0 and the 'Target' to your internet gateway by searching for its name and selecting it.
![Connect RT to IG1](/images/20.png)
4. Then click 'Save changes'.
You should get:
![Connect RT to IG2](/images/21.png)

### <a id="create-ec2-instances-and-configure-them-into-the-vpc">8. Create EC2 instances and configure them into the VPC</a>

We will deploy 2 EC2 instances: the database first as we need its IP for the app to connect, then the app.
1. Navigate to 'EC2' and click on 'Launch instance'.
2. Name the database VM appropriately and select your working database AMI (image of Ubuntu 18.04 with working DB on).
![DB EC2 1](/images/22.png)
3. Change the 'Network settings' by clicking 'Edit'. Then select your VPC and your private subnet.
![DB EC2 2](/images/db-network-settings.png)
4. Create a Security Group, give it a name and description, and add SSH and port 27017 for MongoDB to connect to the app VM.
![DB EC2 3](/images/24.png)
5. Review your configurations and click 'Launch instance' when you are ready.
6. Navigate to 'EC2' and click on 'Launch instance'.
7. Name the app VM appropriately and select the OS you want to use (we used Ubuntu 20.04).
8. Change the 'Network settings' by clicking 'Edit'. Then select your VPC and your public subnet.
![App EC2 1](/images/25.png)
9. Create a Security Group, give it a name and description, and add SSH and HTTP to the app VM so it can be accessed. (You may also add port 3000 for the app if you have not set up a reverse proxy).
![App EC2 2](/images/26.png)
10. Add the appropriate 'User Data', which can be found [here](https://github.com/EstherSlabbert/tech230_AWS/blob/main/automation.md) in the 'Advanced settings' to automate the set up and running of your web app. Remember to **change the IP address** in the export command to the database's IP (to find it open another tab with your running EC2 instances and click on the EC2 ID of your database then copy the IPv4).
11. Review your configurations, and click 'Launch instance' when you are ready.
12. Go to the public IPv4 of your app VM in your web browser and you should see this:
![App page](/images/page.png)
and `/posts`:
![posts page](/images/posts-page.png)

### <a id="results">Results</a>

VPC:
![VPC](/images/vpc-setup.png)
Resource Map:
![Resource Map](/images/VPC_resource_map.png)
Subnets:
![Subnets](/images/subnets1.png)
![Public Subnet](/images/public-subnet.png)
![Private Subnet](/images/private-subnet.png)
RT:
![RT](/images/route-tables.png)
![RT pub1](/images/public-route-table1.png)
![RT pub2](/images/public-route-table2.png)
![RT priv1](/images/private-route-table1.png)
![RT priv2](/images/private-route-table2.png)
IG:
![IG](/images/internet-gateway.png)
![IG1](/images/internet-gateway1.png)
Security Groups:
![SG](/images/security-groups.png)
![SG db](/images/db-sg.png)
![SG app](/images/app-sg.png)
EC2 instances:
![running](/images/running-instances.png)
![DB](/images/db-instance.png)
![App](/images/app-instance.png)
