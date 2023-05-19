# Automation scripts for the App and Database

When automating scripts you want idempotent, meaning the script should work no matter how many times you run it.

## Creating a provision script on Linux

1. Create the provision file with `touch file_name.sh` (replace file_name with the name you wish to use).
2. Open the provision file with `nano file_name.sh`.
3. Write to the file/copy over the commands and save and exit the file with Ctrl + X, Y, Enter.
4. To enable you to execute the provision file use the following command: `chmod u+x file_name`. This only allows the user to execute it. (Note: to check permissions use `ls -l` in the directory the file is in.)
5. Use `./file_name` when you are in the directory containing your provision file to run and execute your code in the provision files.

## App script

Note that this is a script for Ubuntu 20.04.
[Nginx default configuration file contents](https://www.coderrocketfuel.com/article/default-nginx-configuration-file-inside-sites-available-default)

```shell
#!/bin/bash

# Updates
# Update the sources list
sudo apt update -y

# Upgrade any available packages
sudo apt upgrade -y

# Nginx
# Installs Nginx
sudo apt install nginx -y

# Enables Nginx to run on start up of API or VM
sudo systemctl enable nginx

# Nginx Reverse Proxy
# Replaces the default configurations for Nginx with the required ones for the reverse proxy for port 3000
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

# Alternatively just replace the relevant line:
sudo sed -i 's/^                try_files $uri $uri\/ =404;/            proxy_pass http:\/\/localhost:3000\/;/g' /etc/nginx/sites-available/default
# -i = in-place-editing; s/^ = substitute from the start of the line that has the following in

# Reloads Nginx to 
sudo systemctl reload nginx

# Set up for connection to database
# Creates a global environment variable by adding it to the .bashrc file in order to connect to the Database
# echo 'export DB_HOST=mongodb://<Place MongoDB EC2 IP here>:27017/posts' >> /home/ubuntu/.bashrc # replace with database IP

# Executes the updated commands in .bashrc
# source .bashrc

# temporary environment variable
export DB_HOST=mongodb://<Place MongoDB EC2 IP here>:27017/posts # replace with database IP

# Gets app directory
# Installs git
# sudo apt install git

# Moves app directory from GitHub to EC2 or VM if not already there
if [ -d "/home/ubuntu/app" ]; then
    echo "App folder already exists."
else
    echo "Cloning app folder..."
    git clone https://github.com/EstherSlabbert/app.git /home/ubuntu
fi

# Installations
# gets sources list that could potentially be needed for the following installations
sudo apt update

# installs node.js and npm
sudo apt install -y nodejs npm

# installs pm2
sudo npm install -g pm2

# Runs app
# Stops app if already running so only one thing is using the port
pm2 stop all # stop all as pm2 is only running app

# Navigates into the folder containing the app
cd /home/ubuntu/app

# Runs/Starts the app in the background
pm2 start app.js

```

## Database script on start up

Note that this is a script for Ubuntu 20.04.
**I have not tested this yet.**

```shell
#!/bin/bash

# Updates the sources list
sudo apt update -y

# Upgrades any available packages
sudo apt upgrade -y

# Adds a Key for MongoDB
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927

# Adds a software repository entry to MongoDB in the APT package manager on Ubuntu
# echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

# gets sources list that could potentially be needed for the following installations
sudo apt update -y

# Installs MongoDB
sudo apt install -y mongodb

# Replaces the bind IP in the MongoDB configuration files to allow all to access
sudo sed -i 's/^bind_ip = 127.0.0.1/bind_ip = 0.0.0.0/g' /etc/mongodb.conf

# Starts MongoDB service
sudo systemctl start mongodb

# Enables MongoDB to run on start up of EC2 or VM
sudo systemctl enable mongodb
```
