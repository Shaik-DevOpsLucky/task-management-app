# 🚀 Task Management App – EC2 Deployment Guide

## 📌 Tech Stack

* Frontend: Angular (TypeScript)
* Backend: Node.js (Express)
* Database: MongoDB
* Process Manager: PM2
* Web Server: Nginx
* Cloud: AWS EC2 (Ubuntu)

---

# 🖥 1️⃣ EC2 Server Setup

## Update System

```bash
sudo apt update && sudo apt upgrade -y
```

---

# 🟢 2️⃣ Install Required Tools

## Install Node.js (v18 recommended)

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

Verify:

```bash
node -v
npm -v
```

---

## Install Angular CLI

```bash
sudo npm install -g @angular/cli
```

Verify:

```bash
ng version
```

---

## Install MongoDB (if using local DB)

```bash
sudo apt install mongodb -y
sudo systemctl start mongodb
sudo systemctl enable mongodb
```

---

## Install PM2 (Production Process Manager)

```bash
sudo npm install -g pm2
```

---

## Install Nginx

```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

# 📂 3️⃣ Clone Project

```bash
git clone https://github.com/Shaik-DevOpsLucky/task-management-app.git
cd task-management-app
```

---

# ⚙ 4️⃣ Backend Setup

```bash
cd backend
npm install
```

### Create `.env` file

```bash
cp .env.example .env
nano .env
```

Example:

```
PORT=3000
MONGO_URI=mongodb://localhost:27017/taskdb
JWT_SECRET=your_secret
```

---

## Start Backend with PM2

```bash
pm2 start index.js --name task-backend
pm2 save
pm2 startup
```

Check logs:

```bash
pm2 logs task-backend
```

---

# 🎨 5️⃣ Frontend Build (Angular Production)

Go to root project:

```bash
cd ~/task-management-app
npm install
ng build --configuration production
```

Build output will be inside:

```
dist/task-management-app/browser/
```

---

# 🌐 6️⃣ Deploy Angular to Nginx

Clear default files:

```bash
sudo rm -rf /var/www/html/*
```

Copy correct build folder:

```bash
sudo cp -r dist/task-management-app/browser/* /var/www/html/
```

Fix permissions:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

---

# 🔁 7️⃣ Configure Nginx Reverse Proxy

Edit config:

```bash
sudo nano /etc/nginx/sites-available/default
```

Replace server block with:

```nginx
server {
    listen 80;
    server_name _;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:3000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Test config:

```bash
sudo nginx -t
```

Restart:

```bash
sudo systemctl restart nginx
```

---

# 🔐 8️⃣ EC2 Security Group Rules

Open:

| Port | Purpose                          |
| ---- | -------------------------------- |
| 22   | SSH                              |
| 80   | HTTP                             |
| 3000 | Only if testing backend directly |

After reverse proxy setup, you can remove port 3000 from public access.

---

# 🧪 9️⃣ Testing

### Test Backend

```bash
curl localhost:3000
```

### Test Nginx

```bash
curl localhost
```

Browser:

```
http://<EC2-PUBLIC-IP>
```

---

# 🚨 Troubleshooting Guide

---

## ❌ 403 Forbidden (Nginx)

### Cause:

* index.html not in `/var/www/html`
* Wrong Angular folder copied

### Fix:

```bash
sudo rm -rf /var/www/html/*
sudo cp -r dist/task-management-app/browser/* /var/www/html/
```

---

## ❌ Port 3000 Not Accessible

Check:

```bash
sudo netstat -tulpn | grep 3000
```

Ensure:

* Backend running
* Security group allows 3000

---

## ❌ Angular Page Loads but API Not Working

Make sure Angular API calls use:

```
/api/tasks
```

NOT:

```
http://localhost:3000/api/tasks
```

Because Nginx handles routing.

---

## ❌ MongoDB Connection Error

Check:

```bash
sudo systemctl status mongodb
```

Verify `.env` MONGO_URI.

---

## ❌ PM2 Process Not Running After Reboot

Run:

```bash
pm2 save
pm2 startup
```

---

# 🏗 Final Production Architecture

```
Internet
   ↓
Nginx (Port 80)
   ↓
Angular Static Files
   ↓
Node.js Backend (Port 3000)
   ↓
MongoDB
```

---

# 🚀 Next Level Improvements

* Add SSL using Let's Encrypt
* Use Docker instead of manual setup
* Use Docker Compose
* Deploy to Kubernetes
* Setup CI/CD with GitHub Actions
* Add Monitoring (Prometheus + Grafana)

---

# 📌 Summary

You successfully deployed:

✔ Angular Production Build
✔ Node.js Backend with PM2
✔ MongoDB
✔ Nginx Reverse Proxy
✔ EC2 Production Setup

---
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/adccbe79-0114-4d97-9044-8b486760c4e7" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8a676a6d-e55c-461d-abbc-64e1678df6ef" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c007a452-8483-4eed-83b8-4c7ee5e8b8fc" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5ed2c428-95ca-4c40-ba49-953f52db92b7" />




# Prepared by:
# *Shaik Moulali*
# *Multi Cloud and DevOps Consultant*
# *Email: moula.cloud5@gmail.com*
