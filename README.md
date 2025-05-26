# myBhutan-cloud-server-project

## About
This project is a static tourism website called **myBhutan**, deployed on an AWS EC2 instance using Apache web server. It aims to showcase the beauty and travel highlights of Bhutan.

---

##  Technologies Used
- HTML / CSS
- Apache2
- AWS EC2 (Ubuntu 22.04)
- GitHub

---

## Deployment Steps

### 1. Launched EC2 on AWS
- Chose Ubuntu 22.04 LTS
- Selected t2.micro instance
- Set up security groups to allow ports 22, 80, 443

### 2. Installed Apache
```bash
sudo apt update  
sudo apt install apache2


