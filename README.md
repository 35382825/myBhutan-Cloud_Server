# myBhutan-cloud-server-project

## Project Overview
This project is a static tourism website called **myBhutan**, deployed on an AWS EC2 instance using Apache web server. It aims to showcase the beauty and travel highlights of Bhutan.

### Video Walkthrough
Click here to watch the full walkthrough - link to access the video explaining the project 

####  Technologies Used
- HTML / CSS for website index html
- Ubuntu 22.04 LTS virtual machine operating system 
- Apache2 webserver 
- AWS EC2 as my platform to host my website 
- GoDaddy to purchase domain for my webiste 
- GitHub to maintain my documentation of my project 
- Bash scripting & Cron jobs (for backups)


#### Deployment Steps

### 1. Launched EC2 Instance on AWS Platform 
- Login to my AWS account 
- Chose Ubuntu 22.04 LTS
- Selected t2.micro instance
- Set up security groups to allow ports 22, 80, 443
- ssh -i "myBhutanweb.pem" ubuntu@your-ec2-ip

### 2. Installed Apache
- sudo apt update  
- sudo apt install apache2 -y

### 3. Setup Virtual Host
- create a directory for your domain my-bhutan by applying #sudo mkdir /var/www/ my-bhutan
- perform # sudo chown -R $USER:$USER /var/www/ my-bhutan to assign the ownership of the directory to the user you are currently signed in as.
- allow permission to read,write and execute to the owner and read and execute to the groups and others involved with # sudo chmod -R 755 /var/www/ my-bhutan
- create sample index html using nano and save the file by # sudo nano /var/www/ my-bhutan /index.html and # Ctrl+O → Enter → Ctrl+X
- Copy the default Apache config to create your own site config so that you dont have to touch the default file by sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/my-bhutan.conf
- Edit the new config file with your domain and web folder details by sudo nano /etc/apache2/sites-available/my-bhutan.conf
- reload the server before executing the a2ensite command due to 
modifications made to the configuration file.# sudo systemctl reload apache2
- enable the file with the a2ensite tool: # sudo a2ensite my-bhutan.conf
- Disable the default site 000-default.conf so that Apache uses only your VirtualHost (my-bhutan.conf) to serve your website without any confusion and issues. # sudo a2dissite 000-default.conf


### 4. Upload and unzip my folder with website content 
- scp -i "myBhutanweb.pem" myBhutanWebsite.zip ubuntu@your-ec2-ip:~
- 
- unzip myBhutanWebsite.zip
- sudo cp -r myBhutanWebsite/* /var/www/html/

### 4. Restart Apache
- sudo systemctl restart apache2

### 5. Configure domain name with public IP
## Domain Provider for the Project - GoDaddy
- Create a new account in GoDaddy to purchase the desired domain.
- login into the account and search for the domain
- purchase the domain and checkout.
  
## connect your domain to EC2 server
- Go to GoDaddy → Manage Domains → DNS → Manage Zones.
- Find your domain my-bhutan.com.
- Under DNS Records, create or update these A Records:
#A	@	your EC2 Public IPv4 address	1 hour or default
#A	www	your EC2 Public IPv4 address	1 hour or default
- Wait for DNS to propagate as it might take some time.
- After DNS propagates, go to your browser and http://my-bhutan.com / http://www.my-bhutan.com which link show your website hosted of EC2. 


###### Security Setup 
### SSH key authentication - add inbound rules on EC2 instance dialog box
- Select the running instance
- Scroll to the bottom of the instance details pane.
- Under Security, click the Security group name
- Click “Edit inbound rules
- Save the rule 
- 

### SSL installation using Certbot inside EC2 to turn your website to https
- sudo apt update
- sudo snap install --classic certbot -  to install Certbot
- sudo certbot --apache -d my-bhutan.com -d www.my-bhutan.com

### Scripting using bash script and cronjob for backup 
-  run mkdir /home/ubuntu/backups in the terminal
-  nano /home/ubuntu/backup-webroot.sh - write down the script for the backup as you desire. save and exit. 
-  chmod +x /home/ubuntu/backup-webroot.sh - execute the script
-  run the command /home/ubuntu/backup-webroot.sh to test.
-  ls /home/ubuntu/backups - check the backup file 


###### Live Site
link to the public ip - Visit myBhutan website
- https://www.my-bhutan.com



