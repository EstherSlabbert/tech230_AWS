# Autoscaling and load balancing

Auto scaling, also referred to as autoscaling, auto-scaling, and sometimes automatic scaling, is a cloud computing technique for dynamically allocating computational resources. Depending on the load to a server farm or pool, the number of servers that are active will typically vary automatically as user needs fluctuate.

Autoscaling is a cloud computing feature that enables organizations to scale cloud services such as server capacities or virtual machines up or down automatically, based on defined situations such as traffic ir utilization levels. Cloud computing providers, such as Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP), offer autoscaling tools.

![Alt text](/images/autoscaling_group.png)

[What is  Auto scaling?](https://avinetworks.com/glossary/auto-scaling/)

Auto scaling is useful for managing microservices, as we do not know when demand for service changes and by how much and auto scaling automates this tracking and provides resources needed according to the configurations layed out in the auto scale policy.

Auto Scaling Group - configures launch templates then spins up the instances. Has an autoscaling policy.

Auto Scale Policy - Specify the launch templates. Set threshold - when it checks to make more instances (scale out), what the minimum and maximum number of instances. (Ours will be min 2, desired 2, max 3, Scales out at CPU 50%).

Load Balancer - receives then directs/distributes traffic to instances.

When an auto scaling group is deleted, so too are it's instances.

## Steps to Creating an Auto Scaling Group

### Create EC2 instance for app

See steps here: [EC2 App](https://github.com/EstherSlabbert/tech230_AWS/blob/main/aws_ec2_instances_and_amis.md)

Or follow steps to automate the EC2 set up here: [EC2 App Automation](https://github.com/EstherSlabbert/tech230_AWS/blob/main/automation.md)

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

7. You may choose to add in “User Data” under “Advanced Settings” with start up commands.
8. Create template by clicking on “Create launch template” once you are satisfied with your settings. You may choose to terminate your instance now.

### Create/Configure the Auto Scaling Group

1. Navigate to Auto Scaling Groups, then click on “Create an Auto Scaling group”.

![Alt text](/images/asg1.png)

2. Name your group, then select the launch template you want. Then "Next".

![Alt text](/images/asg2.png)

3. Configure VPC and Availability Zones. Then "Next".

![Alt text](/images/asg3.png)

4. 

### Create a Load Balancer
