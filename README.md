# cloud-server-basics
A full-stack deployment project that showcases how to configure an Ubuntu EC2 server, install and set up Nginx as a reverse proxy, deploy a Node.js web app, and keep it running using systemd.
# 🟢 Deploying a Node.js Web App on AWS EC2 with Nginx & systemd

This project showcases how I deployed a personal landing page built with Node.js on an AWS EC2 instance. I used **Nginx** as a reverse proxy, and **systemd** to keep the app running in the background.

---

## 🚀 Live Site

🌐 [http://13.61.174.137](http://13.61.174.137)

---

## 📋 Summary of What I Did

1. Set up an AWS EC2 instance
2. Installed and configured Nginx reverse proxy
3. Built a Node.js server and landing page
4. Created a systemd service to keep the server running
5. Deployed everything and tested it live

---

## 🔧 Steps Taken

### ✅ 1. Launch an EC2 Instance

- OS: **Ubuntu 22.04 LTS**
- Key pair generated for SSH access
- Security group rules:
  - ✅ HTTP (Port 80)
  - ✅ TCP (Port 3000 – Node.js internal)
  - ✅ SSH (Port 22 for access)

---

### 🔐 2. SSH into the Instance

Used [Termius](https://termius.com/) to connect:

```bash
ssh -i path/to/key.pem ubuntu@<your-ec2-public-ip>
🌐 3. Install and Configure Nginx
sudo apt update
sudo apt install nginx

Configure Nginx Reverse Proxy:
sudo nano /etc/nginx/sites-available/default

Replace the contents with:
server {
    listen 80;
    server_name 13.61.174.137;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Restart Nginx:
bash
Copy
Edit
sudo nginx -t
sudo systemctl restart nginx
🗂️ 4. Create Project Directory & Files
bash
Copy
Edit
mkdir altschool
cd altschool
Created the following files:

index.html — a landing page with my info, project, and contact

server.js — a basic Node.js server

🟩 5. Build the Node.js Server
js
Copy
Edit
// server.js
const http = require('http');
const fs = require('fs');
const path = require('path');

const server = http.createServer((req, res) => {
  fs.readFile(path.join(__dirname, 'index.html'), (err, data) => {
    if (err) {
      res.writeHead(500);
      return res.end('Error loading page');
    }
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end(data);
  });
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
⚙️ 6. Set Up systemd to Keep Server Running
Create the service file:
bash
Copy
Edit
sudo nano /etc/systemd/system/node-server.service
Paste this:

ini
Copy
Edit
[Unit]
Description=Node.js Web Server
After=network.target

[Service]
ExecStart=/usr/bin/node /home/ubuntu/altschool/server.js
Restart=always
User=ubuntu
Environment=PATH=/usr/bin:/usr/local/bin
WorkingDirectory=/home/ubuntu/altschool

[Install]
WantedBy=multi-user.target
Start the service:
bash
Copy
Edit
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable node-server
sudo systemctl start node-server
Monitor logs:
bash
Copy
Edit
journalctl -u node-server -f
📁 Folder Structure
pgsql
Copy
Edit
altschool/
├── index.html
└── server.js

/etc/systemd/system/
└── node-server.service
