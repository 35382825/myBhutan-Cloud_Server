# myBhutan-cloud-server-project

## Project Overview
This is a static tourism website called **myBhutan**, showcasing the beauty and culture of Bhutan.  
It is deployed on an AWS EC2 instance running Ubuntu 22.04 and Apache web server.  
The project demonstrates end-to-end website deployment, DNS setup, SSL/TLS security, and automated backup.

## Video Walkthrough
Click here to watch the full walkthrough - link to access the video explaining the project 

##  Technologies Used
- HTML / CSS for website 
- Ubuntu 22.04 LTS virtual machine operating system 
- Apache2 webserver 
- AWS EC2 as my platform to host my website 
- GoDaddy to purchase domain for my webiste 
- GitHub to maintain my documentation of the project 
- Bash scripting & Cron jobs (for automated backup of my website files)


## Deployment Steps

### 1. Launched EC2 Instance on AWS Platform 
- Logged to my AWS account 
- Chose Ubuntu 22.04 LTS
- Selected t2.micro instance
- Set up security groups to allow ports 22, 80, 443
- Connected to EC2 via SSH
  ssh -i "myBhutanweb.pem" ubuntu@3.27.146.62

### 2. Installed Apache
- sudo apt update  
- sudo apt install apache2 -y (-y to answer yes to the prompts in the process)

### 3. Setup Virtual Host
A Virtual Host allows Apache to serve my website (my-bhutan.com) with its own settings and content from the /var/www/my-bhutan directory.

 1. Create a directory for my  domain "my-bhutan"
  - sudo mkdir -p /var/www/my-bhutan
3. Assign the ownership of the directory to the current user (currently signed in as)
  - sudo chown -R $USER:$USER /var/www/my-bhutan 
3. Allow permission to read, write and execute to the owner and read and execute to the groups and others involved.
  - sudo chmod -R 755 /var/www/ my-bhutan
4. Create a new virtual host folder
  - sudo nano /etc/apache2/sites-available/my-bhutan.com.conf
5.Create sample index html using nano and save the file
  - sudo nano /var/www/ my-bhutan /index.html and # Ctrl+O → Enter → Ctrl+X
6. Copy the default Apache config to create your own site config so that you dont have to touch the default file.
  - sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/my-bhutan.conf
7. Edit the new config file with your domain and web folder details.
  - sudo nano /etc/apache2/sites-available/my-bhutan.conf
8. Reload the server before executing the a2ensite command due to modifications made to the configuration file.
  - sudo systemctl reload apache2
9. Enable the Virtual host with the a2ensite tool:
  - sudo a2ensite my-bhutan.conf
10. Disable the default site ensure that Apache uses only your VirtualHost (my-bhutan.conf) to serve your website without any confusion and issues. But you can enable if required anytime. 
  - sudo a2dissite 000-default.conf
11. You can check if it's disabled as expected
  - ls /etc/apache2/sites-enabled/ OR
12. Verify that your Virtual Host config 
  - ls /etc/apache2/sites-available/

* Output :
-  000-default.conf - means default file
- default-ssl.conf - SSL of default file
- my-bhutan.com.conf - Website file 


### 3. Upload and Unzip Website Content 
1. Enabled the virtual host.
     - sudo a2ensite my-bhutan.com.conf
2. Reload the Apache to apply the changes
     - sudo systemctl reload apache2
3. Exit the EC2 instance
     -  Exit
4. From the local computer, upload the  website zip file to EC2:
     - scp -i "myBhutanweb.pem" myBhutanWebsite.zip ubuntu@your-ec2-ip:~ 
5. SSH back to EC2 Instance
     - ssh -i "myBhutanweb.pem" ubuntu@3.27.146.62 
6. Unzip the webiste file content
     -  unzip myBhutanWebsite.zip
7. Copy the website files to the web server directory
     - sudo cp -r myBhutanWebsite/* /var/www/my-bhutan/
8. Verify the process.
    - ls -lh /var/www/my-bhutan/
* Output - you will be able to see all the contents of your website folder displayed.


### 4. Restart Apache
- sudo systemctl restart apache2

### 5. Configure domain name with public IP
- Domain Provider for the Project - GoDaddy
- Create a new account in GoDaddy to purchase the domain for my website. 
- login into the account and search for the domain
- Purchased domain my-bhutan.com.
  
### DNS Configuration:
- Go to GoDaddy → Manage Domains → DNS → Manage Zones.
- Find your domain my-bhutan.com.
- Under DNS Records, create or update these A Records:
  - A	@	your EC2 Public IPv4 address	1 hour or default
  - A	www	your EC2 Public IPv4 address	1 hour or default
- Waited for DNS to propagate as it takes some time.
- Verified by accessing link show your website hosted of EC2  http://my-bhutan.com / https://www.my-bhutan.com


# Security Setup

### SSH key authentication - add inbound rules on EC2 instance dialog box
- Select the running instance
- Scroll to the bottom of the instance details pane.
- Under Security, click the Security group name
- Click “Edit inbound rules
- Added ports 22, 80, 443.
- Save the rule 


### SSL installation using Certbot inside EC2 to turn your website to https
* Encrypt traffic between your server and user's browser. It is required for HTTPS setup for the website.(https://my-bhutan.com).
* Secures the data tranfer between server and the user
* SSL certificates provided by "Let's Encrypt".
* Certbot is used to request and auto-renew these certificates.

#### Steps:
1.Update and upgrade the packages:
   - sudo apt update && sudo apt upgrade –y
   - sudo apt install snapd
2. Remove certbot-auto and any Certbot OS packages 
   - sudo apt-get remove certbot  
3. Install Certbot using snap
  - sudo snap install --classic certbot -  
4. Request or renew an SSL certificate from Let’s Encrypt
   - sudo certbot -apache -d my-bhutan.com -d www.my-bhutan.com

### Scripting Using Bash Script and Cronjob 
* Used to create automatic backup of the website files and stored it in /home/mybhutan/backup.

#### Steps:
1. Create a backup folder.
  - sudo mkdir -p /home/mybhutan/backup
2. Check if the folder is created:
  - ls -lh /home/mybhutan/backup
3. Create the script for the backup
 - sudo nano /home/ubuntu/backup-mybhutan.sh.
4. Save and exit the script file.
5. Make the script executable 
  - sudo chmod +x /home/ubuntu/backup-mybhutan.sh
6. Run the script to test
  - sudo /home/ubuntu/backup-mybhutan.sh
7. check the backup file
  - /home/mybhutan/backup

* Output:
  - rw-r--r-- 1 root root   31 Jun  7 10:28 backup.log
  - rw-r--r-- 1 root root  491 Jun  7 10:28 my-bhutan-apache-config-2025-06-07.conf
  - rw-r--r-- 1 root root 7.8M Jun  7 10:28 my-bhutan-site-2025-06-07.tar.gz

### Set up the automatic daily backup (cronjob)
- Automates the backup process using cronjob.
- Ensures that the website and its configuration are backed up daily without requiring manual action from the user.

#### Steps:
1. Open the crontab editor:
  - crontab -e  
2. Add the following line at the bottom:
  -  0 2 * * * /home/ubuntu/backup-mybhutan.sh 
  - This means:Run the backup script automatically every day at 2:00 AM.
3. Save (Ctrl + O, then Enter) → Exit (Ctrl + X).

 # Challenges and Troubleshooting

1. DNS Changes Not Reflecting Immediately
   - Problem: After updating the domain’s A records, changes didn’t appear instantly, causing confusion.
   - Solution: Used public DNS resolvers (`nslookup www.mybhutan.com 8.8.8.8`) and tools like `dnschecker.org` to track global propagation.
   -  Waited until updates were globally live before continuing.

2. Apache Virtual Host Not Configured Initially
   - Problem: Website was using Apache’s default config (`000-default.conf`), which caused conflicts during SSL installation.
   - Solution: Created a new virtual host config (`mybhutan.com.conf`) with the correct `ServerName` and `DocumentRoot`, and enabled it using `sudo a2ensite mybhutan.com.conf`.

3. Website Content Not Updating
   - Problem: Changes made to HTML files weren’t reflecting in the browser.
   - Solution: Cleared browser cache and restarted Apache using `sudo systemctl restart apache2` to ensure updated content was served.

4. Website Not Visible in the Browser
   - Problem: After completing all configurations for hosting the website on the EC2 instance, the website was not opening when entering the public IP in the browser.
   - Solution: Checked the inbound rules under EC2 → Security Groups → Inbound Rules. Added necessary port rules (port 80 for HTTP and port 22 for SSH). Once port 80 was enabled, the website became accessible via the browser.


# Modifications of the Website
1. Added the back to top button at the end of the website
   - Improves user experience by allowing quick navigation back to the top of the page, especially on long scrolling websites.
   - Enhance the accessibility and usability, making the site easier and convenient to explore.
   - To do this, i simply add the script to my html by steps below:
     1. cd Downloads
     2. ssh -i "myBhutanweb.pem" ubuntu@3.27.146.62
     3. sudo nano /var/www/my-bhutan/index.html
     4. Pasted the script from my Notepad just before the `</body>` tag.
        
2. Added the Contact Form under the Contact section
   - Provides visitors with an easy and direct way to submit inquiries or feedback through the website.
   - Enhances user engagement and encourages communication between the website owner and visitors.
   - To do this, I used the following steps:
     1. cd Downloads
     2. ssh -i "myBhutanweb.pem" ubuntu@3.27.146.62
     3. sudo nano /var/www/my-bhutan/index.html
     4. Replaced the original Contact section with a new block of code that includes:
        - A link saying "Click here to Contact Us for further support".
        - A Contact Form that appears when the link is clicked (toggle script used).
       
 # Live Site
link to the public ip - Visit myBhutan website
  - https://www.my-bhutan.com
    

 # Re-Deployment Guide with Updated Website Content 
To update or redeploy the website content after initial deployment following the above steps:

1. Prepare the updated website files on your local machine as desired.(e.g., update HTML, images, styles)
2. Compress the updated files into a zip file (e.g., `myBhutanWebsite.zip`).
3. Upload the updated zip file to your EC2 instance. Replace ec2-ip with public ip. 
4.  SSH into your EC2 instance
5.  Unzip and overwrite the existing website file
6.  Restart Apache to apply the changes
7.  Verify the website in your browser to confirm that the updates are live.
   
```bash
scp -i "myBhutanweb.pem" myBhutanWebsite.zip ubuntu@your-ec2-ip:~
ssh -i "myBhutanweb.pem" ubuntu@your-ec2-ip
unzip -o myBhutanWebsite.zip
sudo cp -r myBhutanWebsite/* /var/www/my-bhutan/
*'unzip -o'ensures that existing files are replaced with the new ones easily.
sudo systemctl restart apache2
https://my-bhutan.com


 




