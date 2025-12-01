# 🚀 Deploying a Node.js Application on AWS EC2 with NGINX Reverse Proxy & PM2

This guide walks through deploying a Node.js application on an AWS EC2 instance, keeping it running with **PM2**, and using **NGINX** as a reverse proxy.

---

## 📋 Prerequisites

- AWS EC2 instance (Amazon Linux / CentOS / RHEL based)
- Security group configured to allow:
  - SSH (port 22)
  - HTTP (port 80)
  - Node.js app port (3000) if needed for direct access
- Node.js project files ready (`app.js`, `package.json`)
- Basic Linux command-line knowledge

---

## 1️⃣ Install Node.js and npm

```bash
sudo yum install nodejs -y
sudo yum install npm -y
```

Verify installation:
```bash
node -v
npm -v
```

---

## 2️⃣ Set Up Your Application Directory

```bash
mkdir nodeapp
cd nodeapp
```

Upload your Node.js project files to this directory (via SCP, Git clone, etc.).

---

## 3️⃣ Install Dependencies

```bash
npm install
```

This installs all packages listed in `package.json`.

---

## 4️⃣ Expose Port 3000

Ensure your **Node.js app** listens on **port 3000** in `app.js` or your main server file.

Example:
```javascript
app.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

In AWS Security Group, allow inbound traffic for port 3000 (optional if using reverse proxy).

---

## 5️⃣ Run the Node.js Application

```bash
node app.js
```

Test in browser:
```
http://<EC2-Public-IP>:3000
```

---

## 6️⃣ Keep the App Running with PM2

Install PM2 globally:
```bash
sudo npm install -g pm2
```

Start the app with PM2:
```bash
pm2 start app.js
```

Save the PM2 process list and enable startup script:
```bash
pm2 save
pm2 startup systemd
```

---

## 7️⃣ Configure NGINX as a Reverse Proxy

Install NGINX:
```bash
sudo yum install nginx -y
```

Start NGINX:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

Edit NGINX configuration:
```bash
sudo nano /etc/nginx/nginx.conf
```

Inside the `server` block, add:
```
location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

Test configuration:
```bash
sudo nginx -t
```

Restart NGINX:
```bash
sudo systemctl restart nginx
```

---

## 8️⃣ Final Test

Open in browser:
```
http://<EC2-Public-IP>/
```

You should see your Node.js application running behind NGINX.

---

## 📌 Optional Enhancements

- **Domain & SSL**: Point a domain to your EC2 instance and enable HTTPS with Certbot (Let's Encrypt).
- **Load Balancing**: Integrate with AWS Application Load Balancer (ALB).
- **Logging**: Use PM2 logs with `pm2 logs`.

---

## 🏆 Summary

You have successfully:
- Installed Node.js & npm
- Deployed a Node.js app on AWS EC2
- Kept it alive using PM2
- Configured NGINX as a reverse proxy

Your app is now production-ready 🚀

---


## 🤝 Connect With Me

📬 [LinkedIn](www.linkedin.com/in/pratik-das-a47493231)  
💬 Open to discussions on AWS, Linux, Web App Hosting, and DevOps!
