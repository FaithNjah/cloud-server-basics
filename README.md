# cloud-server-basics
A full-stack deployment project that showcases how to configure an Ubuntu EC2 server, install and set up Nginx as a reverse proxy, deploy a Node.js web app, and keep it running using systemd.
# 🟢 Deploying a Node.js Web App on AWS EC2 with Nginx & systemd

This project showcases how I deployed a personal landing page built with Node.js on an AWS EC2 instance. I used **Nginx** as a reverse proxy, and **systemd** to keep the app running in the background.

---
## 🚀 Public IP address:
13.61.174.137:3000


## 🚀 Live Site

🌐 [http://cloudfaith.duckdns.org](http://cloudfaith.duckdns.org:3000/)
![Screenshot 2025-06-13 200227](https://github.com/user-attachments/assets/c74d4c02-4b25-4421-a3ae-26bc52bda333)

---

## 📋 Summary of What I Did

1. Set up an AWS EC2 instance
2. Installed and configured Nginx reverse proxy
3. Built a Node.js server and landing page
4. Created a systemd service to keep the server running
5. Mapped public IP address to a custom subdomain (Custom domain setup using Duck DNS)
6. Deployed everything and tested it live

---

## ✅ Server Setup

To set up a cloud-based server using AWS EC2:

- Log in to AWS and launch a new EC2 instance
- Choose Ubuntu as the OS
- Select instance type (e.g., `t2.micro`)
- Create a key pair for SSH access and download the `.pem` file
- Configure a security group to allow:
  - Port `22` (SSH)
  - Port `80` (HTTP)
  - Port `443` (HTTPS)
- Launch the instance
  
---
## ✅ Accessing the Server

SSH into the EC2 instance:

`ssh -i <path-to-key-pair.pem-file> ubuntu@13.61.174.137 `

---
Update your package list:
`sudo apt update `

---
## ✅ Setting up NGINX Reverse Proxy

Install NGINX:
`sudo apt install nginx -y `

Edit the default site config:
`sudo nano /etc/nginx/sites-available/default `

Paste the following inside the server block:
`server {
    listen 80;
    server_name cloudfaith.duckdns.org;
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}`

Test and reload NGINX:
`sudo nginx -t
sudo systemctl reload nginx`

---
## ✅  Node.js Web App Deployment
Install Node.js:
`sudo apt install nodejs npm -y`

Verify installation:
`node -v
npm -v`

Start the app to verify it works:
`node server.js`

## ✅  Keeping the App Running with systemd
Create a systemd service file:
`sudo nano /etc/systemd/system/node-server.service`

`[Unit]
Description=Node.js Server
After=network.target
[Service]
ExecStart=/usr/bin/node /home/ubuntu/altschool/server.js
Restart=always
User=ubuntu
Environment=PATH=/usr/bin:/usr/local/bin
WorkingDirectory=/home/ubuntu/altschool
[Install]
WantedBy=multi-user.target`

Enable and start the service:
`sudo systemctl daemon-reload
sudo systemctl enable node-server
sudo systemctl start node-server
`

---

## ✅  Setting Up a Subdomain
Used DuckDNS for a free dynamic DNS:
-Registered subdomain cloudfaith.duckdns.org
-Mapped to EC2 public IP
-Now available at:
🌍 http://cloudfaith.duckdns.org

This project demonstrates the process of launching and configuring a cloud-based Node.js web app with NGINX and systemd on AWS. It serves as a foundational hands-on for understanding cloud deployment basics and server management.

---
