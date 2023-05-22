# AWS EC2 instances and AMIs

## Table of contents
- [EC2 instances](#ec2-instances)
  - [SSH key storage](#ssh-key-storage)
  - [Login to AWS](#login-to-aws)
  - [Create EC2 instance](#create-ec2-instance)
  - [Launch EC2 instance](#launch-ec2-instance)
  - [Exploring the EC2 instance information](#exploring-the-ec2-instance-information)
  - [Connecting to the EC2 instance](#connecting-to-the-ec2-instance)
  - [Setting up Nginx on EC2 instance](#setting-up-nginx-on-ec2-instance)
  - [Stop or Terminate your EC2 instance](#stop-or-terminate-your-ec2-instance)
  - [Provisioning Nginx with User Data](#provisioning-nginx-with-user-data)
 - [AMI - Amazon Machine Images](#ami---amazon-machine-images)
   - [Create Launch template](#create-launch-template)
   - [Launch instance using Launch template](#launch-instance-using-launch-template)
   - [MongoDB Launch template](#mongodb-launch-template)
   - [Check MongoDB Launch template works](#check-mongodb-launch-template-works)
   - [Add app directory to the EC2 instance](#add-app-directory-to-the-ec2-instance)
   - [Install app](#install-app)
   - [Make an AMI for the running app](#make-an-ami-for-the-running-app)
   - [Link the posts page](#link-the-posts-page)

# EC2 instances
### SSH key storage

1. Move the appropriate file, in this case tech230.pem, containing your private SSH key provided to you by your organisation to your '.ssh' folder, located at 'C:/User/Username/.ssh'.

### Login to AWS

Your Login details must be kept confidential!
1. Use the provided details to login to AWS at [AWS DevOps login page](https://sparta-devops.signin.aws.amazon.com/console).
2. Ensure your region is the correct one. At Sparta we use 'Europe (Ireland) eu-west-1'.

![Region](/images/select_region.png)

### Create EC2 instance

EC2 (Elastic Cloud 2) instance is essentially a Virtual Machine.

1. Search for EC2 in the search bar and select EC2.

![EC2](/images/select_EC2.png)

2. Scroll down and click on the orange 'Launch instance' and select 'Launch instance' from the drop down.

![EC2 launch](/images/launch_ec2.png)

3. Name the instance according to good practice naming conventions for your organisation. Sparta follows this format: group, name, type of instance/resource (eg. tech230_esther_first_ec2)

![Name](/images/name_instance.png)

4. Scroll down and select the 'Quick Start' menu and select the OS you want and configure it with the settings you select. In this case: Ubuntu Server 22.04 etc. as shown in the image below.

![Quick Start](/images/quick_start.png)

5. Scroll further and select the 'Instance type' shown below.  This, essentially, tells us how much hardware we need. (Note: We use t2.micro for all instances unless specifically told otherwise.)

![Instance type](/images/instance_type.png)

6. Scroll down to 'Key pair (login)' and select the appropriate one, in this case 'tech230'.

![key login](/images/key_login.png)

7. Scroll down to 'Network Settings' and click 'edit'. Then add a name, in this case 'tech230_esther_first_sg', and a description, in this case 'my first security group'.

![network settings](/images/network_settings1.png)

![network name](/images/network_settings2.png)

8. Scroll down to the 'Security Group' section - this is where you customise who can access your EC2 instance.

![SG](/images/security_group.png)

9. Scroll down to 'Configure storage', where you can add more or less storage to your instance.

![Storage](/images/storage.png)

### Launch EC2 instance

10. On the side you can see the 'Summary' block where you can see all the configurations you have made. If you are happy with all of them then click the orange 'Launch instance' button at the bottom of the 'Summary' block.

### Exploring the EC2 instance information

If you go to 'Instances' page and it will show all the instances available. You can filter them to show the runnning instances or search for the name of your instance or your instance ID. You can see important information relating to the EC2 from here and the 'Status check' should say '2/2 checks passed' or 'initializing' if it has not yet done so. Once it says '2/2 checks passed' then you should be able to login to the EC2 instance. The public IPv4 is the address people will use to access the content the EC2 instance is sharing based on the settings specified. The Private IP is used to get into the EC2 instance itself.

### Connecting to the EC2 instance

11. Find your instance on the 'Instances' page and click on the blue 'Instance ID'.

![EC2 instance info](/images/private_ip.png)

12. Click on the 'Connect' button on that page.

![Connect instance](/images/connect_instance.png)

13. Ensure you select the 'SSH client'. It gives you instructions to connect, which I will outline in the next steps.
14. Open your terminal and cd into your '.ssh' directory, then enter `chmod 400 tech230.pem`, which allows reading privileges for all. It should not return anything. (Note: to see the permissions type `ls -l`)
15. Copy the generated code under 'Example' and paste it into your terminal and enter. (Breakdown command: `ssh i-` uses ssh protocol, i=identity; `"tech230.pem"` which is how we are gaining ssh access; `ubuntu` is the username that it should provide, if not change it before entering; `@ec2` followed by the relevant IP address; then followed by the data center)

![connect](/images/connect1.png)

Your terminal should return the following if it is the first time you log in to your EC2 instance:

![authenticate](/images/authenticate.png)

Which you should then type `yes` and enter to authenticate your connection. This should return that it has permanently added the host and log you in to the EC2 instance in the cloud.

### Setting up Nginx on EC2 instance

1. While logged in to EC2 instance run `sudo apt get update -y` and then `sudo apt get upgrade` (later versions of Linux do not need `get`).

(Note: If you get a pink screen about kernel upgrade 

![Kernel error](/images/error_kernel.png)

press 'Enter', then 'Esc' on the next screen

![2nd error page](/images/error_kernel2.png).

2. Run `sudo apt install nginx -y` to install Nginx. (If you had the error run `sudo apt get update -y` and then `sudo apt get upgrade` again at this point.)
3. Run `sudo systemctl start nginx` and then `sudo systemctl enable nginx` to start and enable Nginx. (Note: To check the status of Nginx use `sudo systemctl status nginx` which should show that it is active and running and hit 'q' to exit back to where you can type.)
4. Navigate back to your 'Instance summary' page on your web browser and scroll down and select the 'Security' tab.

![Security Tab](/images/security_tab.png)

5. Click on the blue link under 'Security groups' above the 'Inbound rules' section.
6. Scroll down and click 'Edit inbound rules'.
7. Click 'Add rule' and configure it to be 'Type' 'HTTP' with a '0.0.0.0/0' 'Source' so everyone can access it.

![add rule](/images/add_rule_fin.png)

8. You should be able to access your Nginx webserver at the 'Public IPv4' found under 'Instance summary' on your web browser. Anyone who has the IP address and enters it into their web browser should be able to see the page, provided they have internet connection.

### Stop or Terminate your EC2 instance

1. Navigate back to your 'Instance summary' page and click on 'Instance state' drop down located next to 'Connect'.

![Instance state](/images/instance_state.png)

2. Select 'Stop instance' to stop the instance so it can be resumed at a later stage or 'Terminate instance' to remove the instance entirely (in this case 'Terminate instance').

### Provisioning Nginx with User Data

When setting up your EC2 instance got to 'Advanced settings' and scroll down to the bottom and you can add the following code into the 'User data' block for the app EC2 instance:
```
#!/bin/bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

## AMI - Amazon Machine Images

AMI is a template for an EC2 instance that is the same as a host system (EC2 instance) state - all files and installations and settings. It enables us to create multiple identical instances. Images (backup) are a snapshot of the state of the entire system. (requires storage to make)

Launch templates are the settings in creating an EC2 instance and can have some set up instructions in 'User Data'.

### Create Launch template

To create an AMI the EC2 must be up and running.

1. Select the check box of the instance you would like the AMI to be based on, the click the 'Actions' drop down, then the 'Images and templates' and click on 'Create template from instance'.

![create ami template](/images/create_ami.png)

2. You will be taken to the 'Create launch template' screen where you will have to name your template, in this case 'tech230_esther_nginx_ami' and you can give it a description.

![name ami](/images/ami1.png)

3. Check the settings and if you are happy with them, click 'Create launch template' in the 'Summary' box.
4. At this point you can terminate your base EC2 instance.

### Launch instance using Launch template

1. You can then select the orange 'Launch instance' drop down and select the 'Launch instance from template' option.
2. It will take you to a page where you can select your ami instance by its name and check the settings, which if you are happy with you can then click the orange 'Launch instance' button in the 'Summary' box.
3. You can then go to the 'Instances' page and search for your instance. Once the 'Status check' has '2/2 checks passed' you should be able to log in to your instance or see the provisioned web page using the Public IP if it is the Nginx AMI template.

### MongoDB Launch template

1. Create an EC2 instance and add the following to the 'User data' in the 'Advanced settings' section:
```
#!/bin/bash
sudo apt update
sudo apt upgrade -y
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927
sudo apt install -y mongodb
sudo systemctl start mongodb
sudo systemctl enable mongodb
```
2. Launch your EC2 instance.
3. Create an AMI template by selecting your EC2 instance on AWS and click the 'Actions' drop down, then the 'Images and templates' and click on 'Create template from instance'.
4. Name it and give it details and check the settings, then click 'Create launch template' in the 'Summary' box.
5. You can now terminate your EC2 instance.

### Check MongoDB Launch template works

1. Launch EC2 instance from the template you created.
2. Log in to the EC2 and type `sudo systemctl status mongodb`.
3. If it returns active and running then you are good to go.


### Add app directory to the EC2 instance

1. While in bash in .ssh directory: `scp -i tech230.pem -r ~/Documents/tech_230_sparta/tech230_virtualisation/tech230_app_deployment/app ubuntu@ec2-34-245-215-245.eu-west-1.compute.amazonaws.com:/home/ubuntu` (`scp -i "~/.ssh/tech230.pem -r app ubuntu@<Insert your EC2 Public DNS here>:/home/ubuntu"` will work if you have `cd` into the folder containing the 'app' directory.)
2. Log in to the EC2 instance and us `ls` to check that the app directory tranferred over. (Use `ssh -i "~/.ssh/tech230.pem" ubuntu@ec2-34-245-215-245.eu-west-1.compute.amazonaws.com` to log in to your EC2 if not in .ssh directory.)

### Install app

1. `sudo apt update`
2. `sudo apt install -y nodejs npm`
3. `sudo npm install -g pm2`
4. Use `pm2 --version` to verify pm2 installation

![pm2](/images/pm2_version.png)

5. `cd app`
6. `pm2 start app.js` to run the app in the background

![app_in_background](/images/app_in_bg.png)

8. On AWS change the Security group to include a custom access to port 3000, for all 0.0.0.0. You can also change the SSH rule to only accept your IP.

![inbound rule](/images/inbound_rule_3000.png)

10. Use the public IP address to navigate to the deployed webpage.

### Make an AMI for the running app

1. While the EC2 instance for running the app is running follow the steps to create the AMI but this time select the 'Actions' drop down and click 'Create an image' (follow the instructions and name it and give descriptions etc.).
2. Launch the AMI from Images/AMIs OR the on the same 'Launch instance' page instead of 'Quick Start' select 'My AMIs' on AWS and then log in to the EC2.
3. `cd app`
4. Run the app with: `pm2 start app.js`
5. Check it use the Public IP address in your web browser, then add ':3000' and it should show the Sparta Provisioning Test Page from the running app.

### Link the posts page

1. Launch the AMIs for both the database and the app.

2. Change security groups:

- Add inbound rules to MongoDB EC2 instance so that the App EC2 instance can connect with it. (port 27017 for MongoDB)

- Add inbound rules to App EC2 instance so that it can connect with the MongoDB EC2 instance. 

3. Connect/login to both EC2 instances using the SSH method.

4. Configure the appropriate default files and add the relevant environment variables.

To do this run the following commands in your EC2 instances:

Check that MongoDB is running with `sudo systemctl status mongodb` on your Database EC2. If it is not running follow the steps detailed earlier in this markdown to set it up before continuing.
```bash
# in database
sudo sed -i 's/^bind_ip = 127.0.0.1/bind_ip = 0.0.0.0/g' /etc/mongodb.conf
sudo systemctl restart mongodb
sudo systemctl enable mongodb
```

If Nginx is not installed and running on your App EC2 follow the steps detailed earlier in this markdown to set it up.
And if you use `ls` and the 'app' directory is not in the EC2 instance then follow the steps detailed earlier in this markdown to get it onto the EC2 before continuing.

```bash
# in app
echo 'export DB_HOST=mongodb://<Place MongoDB EC2 IP here>:27017/posts' >> /home/ubuntu/.bashrc # may work with either private/public IP
source .bashrc
sudo apt update
sudo apt install -y nodejs npm
sudo npm install -g pm2
sudo systemctl reload nginx # optional command
cd app
node seeds/seed.js
pm2 start app.js
```
Alternatively add the DB_HOST with `export DB_HOST=mongodb://<Place MongoDB EC2 IP here>:27017/posts` - this will not be permanent (use `printenv DB_HOST` to check it took). Then you do not have to run the echo and source commands in the block above.
To check if the app is running use `pm2 status`.
If your app had previously been running use `pm2 stop app` to stop app, then try `pm2 start app.js --update-env` to run it with the changes to the environment vairables.
To check what is working use these commands: Nginx status `sudo systemctl status nginx`, Nodejs `nodejs --version` and pm2 `pm2 --version`.

5. If you followed all the steps go to the {Insert the App EC2 Public IP address here}:3000/posts page in your web browser and you should see the Sparta App page with the populated data.

