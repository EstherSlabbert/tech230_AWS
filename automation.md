# Automation scripts for the App and Database

When automating scripts you want idempotent, meaning the script should work no matter how many times you run it.

## Creating a provision script on Linux

1. Create the provision file with `touch file_name.sh` (replace file_name with the name you wish to use).
2. Open the provision file with `nano file_name.sh`.
3. Write to the file/copy over the commands and save and exit the file with Ctrl + X, Y, Enter.
4. To enable you to execute the provision file use the following command: `chmod u+x file_name`. This only allows the user to execute it. (Note: to check permissions use `ls -l` in the directory the file is in.)
5. Use `./file_name` when you are in the directory containing your provision file to run and execute your code in the provision files.

## App scripts

Note that this is a script for Ubuntu 20.04.
[Nginx default configuration file contents](https://www.coderrocketfuel.com/article/default-nginx-configuration-file-inside-sites-available-default)

- Provisions the updates and installations required on the app VM:
```shell
#!/bin/bash

# Updates

# Update the sources list
sudo apt update -y

# Upgrade any available packages
sudo apt upgrade -y

# Installations

# gets sources list that could potentially be needed for the following installations
sudo apt update

# Installs Nginx
sudo apt install nginx -y

# Installs git
sudo apt install git -y

# installs node.js and npm
sudo apt install -y nodejs npm

# installs pm2
sudo npm install -g pm2

# Enables Nginx to run on start up of API or VM
sudo systemctl enable nginx
```

- Sets up Nginx Reverse Proxy for port 3000:
```shell
#!/bin/bash

# Nginx Reverse Proxy
# Replaces the default configurations for Nginx with the required ones for the reverse proxy for port 3000
<<COMMENT
sudo bash -c 'cat <<EOF > /etc/nginx/sites-available/default
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/html;
    server_name _;

    location / {
        proxy_pass http://localhost:3000/;
    }
}
EOF'
COMMENT

# OR just replace the relevant line:
sudo sed -i 's/^                try_files $uri $uri\/ =404;/            proxy_pass http:\/\/localhost:3000\/;/g' /etc/nginx/sites-available/default
# -i = in-place-editing; s/^ = substitute from the start of the line that has the following in

# Restarts Nginx to update configuration changes
sudo systemctl restart nginx
```

- Adds/replaces DB_HOST environment variable to link the database and the app VMs:
**NB**: Replace the '\<Place MongoDB EC2 IP here\>' with the actual IP of the database
```shell
#!/bin/bash

# Set the desired line to insert or replace
new_line='export DB_HOST="mongodb://<Place MongoDB EC2 IP here>:27017/posts"'

# Check if the line already exists in .bashrc
if grep -qxF "export DB_HOST=" ~/.bashrc; then
  # Line exists, so replace it
  sed -i "s|export DB_HOST=.*|$new_line|" ~/.bashrc
else
  # Line does not exist, so insert it at the end
  echo "$new_line" >> ~/.bashrc
fi

# Executes the updated commands in .bashrc
source .bashrc
```

- Gets the app directory:
```shell
#!/bin/bash

# Moves app directory from GitHub to EC2 or VM if not already there
if [ -d "/home/ubuntu/app" ]; then
    echo "App folder already exists."
else
    echo "Cloning app folder..."
    git clone https://github.com/EstherSlabbert/app.git ~/app # https://github.com/bradley-woods/app.git
fi
```

- Runs the app:
```shell
#!/bin/bash

# Runs app
# Stops app proccesses if already running so only one thing is using the port
pm2 stop all

# seeds database
node ~/app/seeds/seed.js

# Runs/Starts the app in the background
pm2 start ~/app/app.js --update-env
```

## Database script on start up

Note that this is a script for Ubuntu 20.04.

- Provisions the upgrades and installations needed for the MongoDB database VM:
```shell
#!/bin/bash

# Updates the sources list
sudo apt update -y

# Upgrades any available packages
sudo apt upgrade -y

# Adds a Key for MongoDB
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927

# gets sources list that could potentially be needed for the following installations
sudo apt update -y

# Installs MongoDB
sudo apt install -y mongodb

# Starts MongoDB service
sudo systemctl start mongodb

# Enables MongoDB to run on start up of EC2 or VM
sudo systemctl enable mongodb
```

- Replaces bindIP to connect to app VM:
```shell
#!/bin/bash

# Replaces the bind IP in the MongoDB configuration files to allow all to access
sudo sed -i 's/^bind_ip = 127.0.0.1/bind_ip = 0.0.0.0/g' /etc/mongodb.conf

# restart (stops and then starts again) mongoDB
sudo systemctl restart mongodb
```

## Commands/actions to check everything is running as it should

### - App:
```shell
# check the contents of current directory
ls

# check status of nginx to ensure active and running
sudo systemctl status nginx

# check if app is runnning if started with pm2
pm2 status

# stops and deletes all pm2 processes so that they cannot be restarted unless started once more
pm2 delete all

# stops all processes (can replace all with app)
pm2 stop all

# otherwise check app is running with this that check all running processes
ps aux

# opens configuration file for nginx so you can check/change the contents for your server location for the reverse proxy
sudo nano /etc/nginx/sites-available/default

# checks nginx configuration file for syntax errors
nginx -t

# reloads nginx
sudo systemctl reload nginx

# checks if the environment variable DB_HOST is there
printenv DB_HOST

# opens .bashrc file so you can check/change the contents for your environment variable DB_HOST
sudo nano ~/.bashrc
# runs the .bashrc file to implement the changes
source .bashrc

# -v or --version returns the version of the specified program or an error if not installed
# check/verify nginx installation
nginx -v

# check/verify git installation
git --version

# check/verify nodejs installation
node --version

# check/verify npm (node package manager) installation
npm --version

# check/verify pm2 installation
pm2 --version
```
Ensure your security group allows for access to port 3000.

### - Database:
```shell
# check if MongoDB is active & running
sudo systemctl status mongodb

# look at .conf file to check the bindIP has been changed to 0.0.0.0
sudo nano /etc/mongodb.conf

# reload (starts again with updated configurations) mongoDB
sudo systemctl reload mongodb
```
Ensure your security group allows access to port 27017.

### - When logging in:

Check the ssh command follows this format: `ssh -i "~/.ssh/tech230.pem" ubuntu@ec2-IP-address-here.eu-west-1.compute.amazonaws.com`.

### - When copying files/folders over:

Check that you follow this format: `scp -i "~/.ssh/tech230.pem" -r "~/path/to/file" ubuntu@ec2-IP-address-here.eu-west-1.compute.amazonaws.com:/home/ubuntu`
