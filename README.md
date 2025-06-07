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


## Deployment Steps

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
- add virtual host folder # sudo nano /etc/apache2/sites-available/my-bhutan.com.conf
- create sample index html using nano and save the file by # sudo nano /var/www/ my-bhutan /index.html and # Ctrl+O → Enter → Ctrl+X
- Copy the default Apache config to create your own site config so that you dont have to touch the default file by sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/my-bhutan.conf
- Edit the new config file with your domain and web folder details by sudo nano /etc/apache2/sites-available/my-bhutan.conf
- reload the server before executing the a2ensite command due to modifications made to the configuration file.# sudo systemctl reload apache2
- enable the file with the a2ensite tool: # sudo a2ensite my-bhutan.conf
- Disable the default site 000-default.conf so that Apache uses only your VirtualHost (my-bhutan.conf) to serve your website without any confusion and issues. # sudo a2dissite 000-default.conf
- check your work by # ls /etc/apache2/sites-available/



### 4. Upload and unzip my folder with website content 
- enable your virtual host. #sudo a2ensite my-bhutan.com.conf
- reload the Apache #sudo systemctl reload apache2
- exit the ec2 and perform 
- scp -i "myBhutanweb.pem" myBhutanWebsite.zip ubuntu@your-ec2-ip:~ on your local computer
- then ssh -i "myBhutanweb.pem" ubuntu@3.27.146.62 to EC2
- unzip myBhutanWebsite.zip
- sudo cp -r myBhutanWebsite/* /var/www/html/

### 4. Restart Apache
- sudo systemctl restart apache2

### 5. Configure domain name with public IP
- **Domain Provider for the Project - GoDaddy**
- Create a new account in GoDaddy to purchase the desired domain.
- login into the account and search for the domain
- purchase the domain and checkout.
  
### connect your domain to EC2 server
- Go to GoDaddy → Manage Domains → DNS → Manage Zones.
- Find your domain my-bhutan.com.
- Under DNS Records, create or update these A Records:
#A	@	your EC2 Public IPv4 address	1 hour or default
#A	www	your EC2 Public IPv4 address	1 hour or default
- Wait for DNS to propagate as it might take some time.
- After DNS propagates, go to your browser and http://my-bhutan.com / http://www.my-bhutan.com which link show your website hosted of EC2. 


# Security Setup 
### SSH key authentication - add inbound rules on EC2 instance dialog box
- Select the running instance
- Scroll to the bottom of the instance details pane.
- Under Security, click the Security group name
- Click “Edit inbound rules
- Save the rule 
- 

### SSL installation using Certbot inside EC2 to turn your website to https
* Encrypt traffic between your server and users' browsers — required for HTTPS (https://my-bhutan.com).
* Let's Encrypt using Certbot
  - Let’s Encrypt provides free SSL certificates.
  - Certbot is the tool to request + auto-renew these certificates.
1.Let's Encrypt using Certbot 
-  sudo apt update && sudo apt upgrade –y
-  sudo apt install snapd
2. Remove certbot-auto and any Certbot OS packages 
  - sudo apt-get remove certbot  
3.  sudo snap install --classic certbot -  to install Certbot
* Request or renew an SSL certificate from Let’s Encrypt
   - sudo certbot -apache -d my-bhutan.com -d www.my-bhutan.com

### Scripting using bash script and cronjob 
* Automatic backup of the website folder
- sudo mkdir -p /home/mybhutan/backup -create a backup folder</home/ubuntu/backups/>
- or run mkdir /home/ubuntu/backups in the terminal
- ls -lh /home/mybhutan – check if folder is created or not 
- create the backup script # sudo nano /home/ubuntu/backup-mybhutan.sh. Save and exit
- execute the script - sudo chmod +x /home/ubuntu/backup-mybhutan.sh
-  run the command to test - sudo /home/ubuntu/backup-mybhutan.sh
-  ls /home/ubuntu/backups - check the backup file
- 
### Set up the automatic daily backup (cronjob)
-   On the terminal, type #crontab -e  
- Add the script as below 0 2 * * * /home/ubuntu/backup-mybhutan.sh 
 * This means: run every day at 2:00 AM
- Save (Ctrl + O, then Enter) → Exit (Ctrl + X).

 # Challenges and Troubleshooting
 1.	DNS Changes Not Reflecting Immediately
-	Problem: After updating the domain’s A records, changes didn’t appear instantly, causing confusion.
- Solution: Used public DNS resolvers (nslookup www.mybhutan.com 8.8.8.8) and tools like dnschecker.org to track global propagation. Waited until updates were globally live before continuing.

3.	Apache Virtual Host Not Configured Initially
-	Problem: Website was using Apache’s default config (000-default.conf), which caused conflicts during SSL installation.
-	Solution: Created a new virtual host config (mybhutan.com.conf) with the correct ServerName, DocumentRoot, and enabled it using a2ensite.

4.	Website Content Not Updating
-	Problem: Changes made to HTML files weren’t reflecting in the browser.
-	Solution: Cleared browser cache and restarted Apache using sudo systemctl restart apache2 to ensure updated content was served.

5.	Website not visible in the browser. 
- Problem: after completing all the configuration related to hosting the website on ec2 instance, website is not opening when you search the public ip in the browser.
- Solution – check the ports enabled in the inbound rules under the instance > security> security group. 
  Add the necessary port rule like port 80, port 22. 


# Live Site
link to the public ip - Visit myBhutan website
- https://www.my-bhutan.com



