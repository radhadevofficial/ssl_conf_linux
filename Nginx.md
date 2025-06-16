setting up **SSL in Nginx on Ubuntu**, covering **both automatic (Let's Encrypt)** and **manual (self-signed or purchased certificate)** methods.

---

## ✅ NGINX SSL CONFIGURATION (Ubuntu)

---

## 🔐 **Option 1: Automatic – Let’s Encrypt (Recommended for public domains)**

### 1️⃣ Install Certbot and Nginx Plugin

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

### 2️⃣ Get and Install Certificate Automatically

```bash
sudo certbot --nginx
```

* Follows interactive steps:

  * Choose domain(s)
  * Automatically edits your Nginx config to include SSL
  * Option to redirect HTTP to HTTPS

### 3️⃣ Test Automatic Renewal

```bash
sudo certbot renew --dry-run
```

---

## 🔐 **Option 2: Manual SSL Configuration (Self-Signed / Purchased Certificate)**

---

### 🛠️ Step-by-step Manual Setup

### 1️⃣ Generate a Self-Signed Certificate

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/mydomain.key \
-out /etc/ssl/certs/mydomain.crt
```

> Replace `mydomain` with your actual domain.

---

### 2️⃣ Modify Your Nginx Site Configuration

```bash
sudo nano /etc/nginx/sites-available/mydomain.conf
```

#### Example SSL Config (Port 443)

```nginx
server {
    listen 443 ssl;
    server_name mydomain.com www.mydomain.com;

    ssl_certificate /etc/ssl/certs/mydomain.crt;
    ssl_certificate_key /etc/ssl/private/mydomain.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

### 3️⃣ Redirect HTTP to HTTPS (Optional but Recommended)

Also add an HTTP redirect block:

```nginx
server {
    listen 80;
    server_name mydomain.com www.mydomain.com;
    return 301 https://$host$request_uri;
}
```

---

### 4️⃣ Enable the Config & Restart Nginx

```bash
sudo ln -s /etc/nginx/sites-available/mydomain.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🔁 Certificate Renewal

* **Let’s Encrypt**: Auto-renewed via cron (`/etc/cron.d/certbot`)
* **Self-signed**: You must reissue manually before it expires (default 365 days)

---

## 📁 Directory Summary

| File                                       | Purpose                                |
| ------------------------------------------ | -------------------------------------- |
| `/etc/ssl/certs/mydomain.crt`              | Public SSL cert                        |
| `/etc/ssl/private/mydomain.key`            | Private key                            |
| `/etc/nginx/sites-available/mydomain.conf` | Nginx config                           |
| `/etc/letsencrypt/`                        | Let's Encrypt certs (if using certbot) |

---
