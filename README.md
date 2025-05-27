# myBhutan-cloud-server-project

## About
This project is a static tourism website called **myBhutan**, deployed on an AWS EC2 instance using Apache web server. It aims to showcase the beauty and travel highlights of Bhutan.

### Video Walkthrough
Click here to watch the full walkthrough - link to access the video explaining the project 

##  Technologies Used
- HTML / CSS
- Apache2
- AWS EC2 (Ubuntu 22.04)
- Domain: GoDaddy (.com)
- GitHub


## Deployment Steps

### 1. Launched EC2 on AWS
- Chose Ubuntu 22.04 LTS
- Selected t2.micro instance
- Set up security groups to allow ports 22, 80, 443
- ssh -i "myBhutanweb.pem" ubuntu@your-ec2-ip

### 2. Installed Apache
- sudo apt update  
- sudo apt install apache2 -y

### 3. upload and unzip my folder with website content 
- scp -i "myBhutanweb.pem" myBhutanWebsite.zip ubuntu@your-ec2-ip:~
- 
- unzip myBhutanWebsite.zip
- sudo cp -r myBhutanWebsite/* /var/www/html/


## Security Setup 
SSH key authentication
Enabled UFW firewall:

## Live Site
link to the public ip - Visit myBhutan website



