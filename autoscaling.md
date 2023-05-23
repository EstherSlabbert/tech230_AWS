# Autoscaling and load balancing

## Table of Contents
- [What is Auto scaling and Load balancing?](#what-is-auto-scaling-and-load-balancing)
- [Auto Scaling Group](#auto-scaling-group)
- [Steps to Creating an Auto Scaling Group](#steps-to-creating-an-auto-scaling-group)
  - [Create EC2 instance for app](#create-ec2-instance-for-app)
  - [Create your AMI from an EC2 instance for the app](#create-your-ami-from-an-ec2-instance-for-the-app)
  - [Create your launch template from an AMI for the app](#create-your-launch-template-from-an-ami-for-the-app)
  - [Create/Configure the Auto Scaling Group](#create/configure-the-auto-scaling-group)


## What is Auto scaling and Load balancing?

**Auto scaling**, also referred to as autoscaling, auto-scaling, and sometimes automatic scaling, is a cloud computing technique for dynamically allocating computational resources. Depending on the load to a server farm or pool, the number of servers that are active will typically vary automatically as user needs fluctuate.

Autoscaling is a cloud computing feature that enables organizations to scale cloud services such as server capacities or virtual machines up or down automatically, based on defined situations such as traffic ir utilization levels. Cloud computing providers, such as Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP), offer autoscaling tools.

![Alt text](/images/autoscaling_group.png)

[What is  Auto scaling?](https://avinetworks.com/glossary/auto-scaling/)

Auto scaling is useful for managing microservices, as we do not know when demand for service changes and by how much and auto scaling automates this tracking and provides resources needed according to the configurations layed out in the auto scale policy.

**Load balancing** is a technique used in computer networking and distributed systems to distribute incoming network traffic across multiple servers, ensuring optimal resource utilization, improved performance, and high availability. It helps to evenly distribute the workload and prevent any single server from becoming overloaded, thus enhancing the overall reliability and scalability of the system.

[AWS - Load balancing](https://aws.amazon.com/what-is/load-balancing/)

## Auto Scaling Group

**Auto Scaling Group** - configures launch templates then spins up the instances. Has an autoscaling policy.

Auto Scale Policy - Specify the launch templates. Set threshold - when it checks to make more instances (scale out), what the minimum and maximum number of instances. (Ours will be min 2, desired 2, max 3, Scales out at CPU 50%).

Load Balancer - receives then directs/distributes traffic to instances.

When an auto scaling group is deleted, so too are it's instances.

## Steps to Creating an Auto Scaling Group

### Create EC2 instance for app

See steps here: [EC2 App](https://github.com/EstherSlabbert/tech230_AWS/blob/main/aws_ec2_instances_and_amis.md)

Or follow steps to automate the EC2 set up here: [EC2 App Automation](https://github.com/EstherSlabbert/tech230_AWS/blob/main/automation.md)

Or try use the following in your user data for creating your instance:
```shell
#!/bin/bash

# Update the sources list
sudo apt update -y

# Upgrade any available packages
sudo apt upgrade -y

# navigates to home directory
cd /home/ubuntu

# gets sources list that could potentially be needed for the following installations
sudo apt update

# Installs Nginx
sudo apt install nginx -y

# Installs git
sudo apt install git -y

# sets source to retrieve nodejs
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

# installs node.js
sudo apt install -y nodejs

# installs pm2
sudo npm install -g pm2

# Enables Nginx to run on start up of API or VM
sudo systemctl enable nginx

# Moves app directory from GitHub to EC2 or VM if not already there
if [ -d "/home/ubuntu/app" ]; then
    echo "App folder already exists."
else
    echo "Cloning app folder..."
    git clone https://github.com/bradley-woods/app.git /home/ubuntu/app
fi

# changes line for reverse proxy
sudo sed -i "s/try_files \$uri \$uri\/ =404;/proxy_pass http:\/\/localhost:3000\/;/" /etc/nginx/sites-available/default

# Restarts Nginx to update configuration changes
sudo systemctl restart nginx

# navigates into the correct directory
cd /home/ubuntu/app

# installs app
npm install

# Runs/Starts the app in the background
pm2 start app.js
```
Ensure that your Sparta test page works.
Have Nginx, the static page for the app and the reverse proxy set up.

### Create your AMI from an EC2 instance for the app

1. With your app EC2 instance up and running with your app working create an AMI.

![Alt text](/images/ami_creation1.png)

2. Name the AMI appropriately and review configurations.

![Alt text](/images/ami_creation2.png)

3. Navigate to AMIs page and ensure it has a name.

![Alt text](/images/ami_creation3.png)

### Create your launch template from an AMI for the app

1. Navigate to the "Launch templates" page and select "Create Launch Template".

![Alt text](/images/lt1.png)

2. Name your template appropriately.

![Alt text](/images/lt2.png)

3. Choose the AMI you want to base your template on.

![Alt text](/images/lt3.png)

4. Specify instance type (in this case t2.micro).

![Alt text](/images/lt4.png)

5. Specify ssh key pair login.

![Alt text](/images/lt5.png)

6. Specify security group for template.

![Alt text](/images/lt6.png)

7. You may choose to add the code below in “User Data” under “Advanced Settings” with start up commands.
```shell
#!/bin/bash

# navigates to correct folder
cd /home/ubuntu/app

# installs app
npm install

# starts app
pm2 start app.js
```

8. Create template by clicking on “Create launch template” once you are satisfied with your settings. You may choose to terminate your instance now.

### Create/Configure the Auto Scaling Group <a id="create/configure-the-auto-scaling-group">Create/Configure the Auto Scaling Group</a>

1. Navigate to Auto Scaling Groups, then click on “Create an Auto Scaling group”.

![Alt text](/images/asg1.png)

2. Name your group, then select the launch template you want. Then "Next".

![Alt text](/images/asg2.png)

3. Configure VPC and Availability Zones. Then "Next".

![Alt text](/images/asg3.png)

4. **Create a load balancer**. Name it and configure it for HTTP and internet-facing. Then create a target group and name it. Turn on ‘Elastic Load Balancing health checks’. Then "Next".

![Alt text](/images/asg4.png)

![Alt text](/images/asg5.png)

![Alt text](/images/asg6.png)

5. Set the group size capacities (in this case 2, 2, 3). Add a scaling policy for averaging 50% CPU usage. Then "Next".

![Alt text](/images/asg7.png)

![Alt text](/images/asg8.png)

6. You may choose to add notifications so that you are notified when the instances are created or destroyed. We will not be adding this for now. Then "Next".

7. Add a tag to identify your Auto Scaling Group.

![Alt text](/images/asg9.png)

8. Review and check your settings and configurations. When ready click "Create Auto Scaling group".
9. You should be able to access the app static page from the load balancer DNS address in your browser.

![Alt text](/images/app.png)
