# AWS EC2 Overview and Deployment Guide

## 🌟 Introduction to AWS EC2
Amazon Elastic Compute Cloud (**EC2**) is a web service that provides **scalable, resizable compute capacity** in the cloud. It enables users to run virtual machines (VMs) with various configurations, operating systems, and networking options.

### ☁️ What is EC2?
EC2 instances are **virtual servers** in the AWS cloud. The name **EC2** stands for:
1. **Elastic** – Ability to increase/decrease machine size dynamically.
2. **Compute** – Virtual machines for computing needs.

- To launch a new EC2 instance, simply navigate to the **AWS Management Console** and spin up an instance.

---

## 🚀 How to Deploy an Application on AWS EC2

### ☁️ Step 1: Create an EC2 Instance
1️⃣ **Log in to AWS Console**  
   - Visit the [AWS EC2 Console](https://console.aws.amazon.com/ec2/)
   
2️⃣ **Launch an Instance**  
   - Click on **"Launch Instance"**.
   
3️⃣ **Give Your Instance a Name**  
   - Choose a meaningful name for easy identification.
   
4️⃣ **Select an Operating System**  
   - Choose an OS (e.g., Ubuntu, Amazon Linux, Windows Server).
   
5️⃣ **Choose Instance Type**  
   - Select an instance size (e.g., `t2.micro` for **free-tier** users).
   
6️⃣ **Create a Key Pair**  
   - Download the `.pem` key file for **secure SSH access**.
   
7️⃣ **Add Storage**  
   - Choose an **EBS** storage size (default is 8GB for free-tier users).
   
8️⃣ **Configure Security Group**  
   - Allow necessary **ports** (e.g., **22** for SSH, **80** for HTTP, **443** for HTTPS).
   
9️⃣ **Launch the Instance!**  
   - Click on **"Launch"** and wait for your instance to start.

---

## 🔗 Step 2: Connecting to Your EC2 Instance

### 1️⃣ Move to the Directory Containing the `.pem` File
```bash
cd /path/to/your/pem-file
```

### 2️⃣ Set Correct File Permissions
```bash
chmod 400 your-key.pem
```
💡 This ensures only you can access the file.

### 3️⃣ Connect via SSH
For **Amazon Linux**:
```sh
ssh -i your-key.pem ec2-user@your-instance-public-ip
```
For **Ubuntu instances**:
```sh
ssh -i your-key.pem ubuntu@your-instance-public-ip
```
🔹 Replace `your-instance-public-ip` with the **actual public IP** of your EC2 instance.

---

## 📦 Step 3: Deploy Your Application

### ✅ 1. Update System Packages
```sh
sudo apt update && sudo apt upgrade -y  # For Ubuntu
sudo yum update -y  # For Amazon Linux
```

### ✅ 2. Install Necessary Software
For Node.js applications:
```sh
sudo apt install -y nodejs npm nginx
```

### ✅ 3. Clone Your Application Repository
```sh
git clone https://github.com/your-repo.git
```
💡 **Troubleshooting:** If you get a "temporary failure in name resolution" error, ensure your EC2 instance has internet access.

### ✅ 4. Install Dependencies and Start the App
```sh
cd your-repo
npm install
npm start
```

---

## 🌍 Accessing Your EC2 Server

### 🔹 Once your application is running, access it via:
```http://your-instance-public-ip:3000```

### 🔹 Security Group Configuration
- You can open the specific port your app runs on (e.g., 8080) or process requests on port 80.
- Port 80 (HTTP) and port 443 (HTTPS) are default ports for web traffic.
- External requests to non-opened ports (e.g., `http://your-instance-public-ip:8080`) will be blocked.

---

## 🔐 Step 4: Configure Firewall & Security

### ✅ Allow Traffic on Required Ports
```sh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

### ✅ Set Up Nginx as a Reverse Proxy (For Node.js Apps)

#### 🔹 Install Nginx
```sh
sudo apt update
sudo apt install nginx
```

#### 🔹 Configure Nginx
Edit the Nginx configuration file:
```sh
sudo vi /etc/nginx/sites-available/default
```

**Add the following configuration:**
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### 🔹 Restart Nginx
```sh
sudo nginx -s reload
```

---

## ✅ Step 5: Enable HTTPS with Let's Encrypt

### 🔧 Step 1: Install Certbot & Nginx Plugin
```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx -y
```

### 🔑 Step 2: Obtain a Free SSL Certificate
```bash
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

### 🔄 Step 3: Auto-Renew SSL Certificate
Test renewal with:
```bash
sudo certbot renew --dry-run
```

### 🚀 Step 4: Restart Nginx & Verify HTTPS
```bash
sudo systemctl restart nginx
```
Now visit your website using:
```https
https://your-domain.com
```

---

## 🎯 Conclusion
This guide provided a step-by-step walkthrough for launching and deploying an application on AWS EC2.

**Next Steps:**
- Configure **auto-scaling** for handling traffic spikes.
- Set up an **RDS database** for data management.
- Use **AWS Elastic Load Balancer (ELB)** for better performance.

Happy Deploying! 🚀
