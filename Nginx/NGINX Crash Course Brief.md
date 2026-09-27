# NGINX Crash Course

Core NGINX concepts in one place: static site hosting, reverse proxy, load balancing, and SSL. For each feature you get the *why*, the *when*, and a working config sample. There's also a section on deployment strategies (canary and blue-green).

## Table of Contents

1. [Static Site Hosting](#1-static-site-hosting)
2. [Reverse Proxy](#2-reverse-proxy)
3. [Load Balancer](#3-load-balancer)
4. [SSL (HTTPS)](#4-ssl-https)
5. [Feature Summary Table](#5-feature-summary-table)
6. [Load Balancer Keywords (backup, weight, and more)](#6-load-balancer-keywords)
7. [Canary Deployment](#7-canary-deployment)
8. [Blue-Green Deployment](#8-blue-green-deployment)
9. [Deployment Summary Table](#9-deployment-summary-table)

---

## 1. Static Site Hosting

**Why use it?**
Serve HTML, CSS, JS, and images directly from the server. It is fast, simple, and uses very few resources.

**When to use it?**
Portfolios, documentation, landing pages, blogs, or any website that does not change based on user input.

**How to use it**

```nginx
server {
    listen 80;
    server_name example.com;

    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- Put your static files in `/var/www/html`.
- Reload NGINX after editing the config:

```bash
sudo nginx -s reload
```

---

## 2. Reverse Proxy

**Why use it?**
Forward incoming requests to one or more backend servers while hiding the internal details. It improves scalability, security, and manageability.

**When to use it?**
When you separate frontend and backend (e.g. a React frontend and a Node.js backend), or when serving APIs and microservices.

**How to use it**

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://localhost:3000;  # backend app
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Requests to `api.example.com` are quietly forwarded to your backend server. The `proxy_set_header` lines pass the real client details to the backend.

---

## 3. Load Balancer

**Why use it?**
Spread traffic across multiple servers for high availability and better resource use. No single server gets overloaded.

**When to use it?**
Apps with high or changing traffic that need horizontal scaling for reliability and performance.

**How to use it**

```nginx
http {
    upstream app_servers {
        server 192.168.1.101;
        server 192.168.1.102;
    }

    server {
        listen 80;
        server_name www.example.com;

        location / {
            proxy_pass http://app_servers;
        }
    }
}
```

By default, NGINX sends requests to both servers using round-robin (one after the other).

---

## 4. SSL (HTTPS)

**Why use it?**
Encrypt traffic to protect user data and privacy. It is essential for security and expected by modern browsers.

**When to use it?**
Any time you serve sensitive content. Always recommended for public-facing sites.

**How to use it**

First get an SSL certificate (e.g. from Let's Encrypt or a certificate authority).

```nginx
server {
    listen 443 ssl;
    server_name secure.example.com;

    ssl_certificate     /etc/ssl/certs/example.crt;
    ssl_certificate_key /etc/ssl/private/example.key;

    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

- Make sure the certificate and key paths are correct.
- For Let's Encrypt, files are usually under `/etc/letsencrypt/live/domain.com/`.

**Redirect HTTP to HTTPS**

```nginx
server {
    listen 80;
    server_name secure.example.com;
    return 301 https://$host$request_uri;
}
```

---

## 5. Feature Summary Table

| Feature | Why Use It | When to Use It | Key Config |
| :-- | :-- | :-- | :-- |
| Static Hosting | Fast, simple hosting | Simple, unchanging sites | `root /var/www/html;` |
| Reverse Proxy | Security, flexibility | Fronting backends behind one endpoint | `proxy_pass http://localhost:3000;` |
| Load Balancer | Scaling, redundancy | High or variable traffic | `upstream app_servers { ... }` |
| SSL | Secure communication | Any site, especially with logins/data | `listen 443 ssl;` + cert paths |

**Tip:** These features are often combined — host a static frontend, reverse-proxy to backend APIs, secure it with SSL, and load-balance across several backends. Always test the config and reload NGINX after changes.

---

## 6. Load Balancer Keywords

Extra parameters you can add inside an `upstream` block.

### `backup`

Marks a server as a backup. It only receives traffic when all primary servers are down. Useful for failover and high availability.

```nginx
upstream myapp {
    server app1.example.com;
    server app2.example.com;
    server backup1.example.com backup;
}
```

Here `backup1` only gets requests if both `app1` and `app2` are down.

### `weight`

Controls how much traffic each server gets. A higher weight means more requests.

```nginx
upstream myapp {
    server app1.example.com weight=5;
    server app2.example.com weight=1;
}
```

`app1` receives five times as many requests as `app2`.

### Other useful parameters

- **`max_fails`** — number of failed attempts before a server is marked unavailable.
- **`fail_timeout`** — how long to treat a server as unavailable after failures.
- **`down`** — marks a server as permanently down (for maintenance).

---

## 7. Canary Deployment

Send a small share of traffic to a new version while most users stay on the stable version. Good for testing new features with real traffic at low risk.

You define two upstreams (stable and canary) and use `split_clients` to route a percentage to each.

```nginx
http {
    split_clients "${remote_addr}" $upstream_name {
        95%     "stable";
        5%      "canary";
    }

    upstream stable {
        server stable.example.com;
    }

    upstream canary {
        server canary.example.com;
    }

    server {
        listen 80;
        server_name app.example.com;
        location / {
            proxy_pass http://$upstream_name;
        }
    }
}
```

This sends 5% of users to the canary version and 95% to stable.

---

## 8. Blue-Green Deployment

Use two identical environments (Blue and Green). One is live, the other holds the next release. Deploy to the idle one, test it, then switch all traffic over by changing the upstream and reloading NGINX. If something breaks, switch back.

```nginx
upstream app_backend {
    server blue.example.com;   # currently live
    # To switch, change to: server green.example.com;
}

# ...in your server block
location / {
    proxy_pass http://app_backend;
}
```

Change the `upstream` to point to the green environment and reload NGINX to cut over all traffic. Automation can make this a near zero-downtime switch.

---

## 9. Deployment Summary Table

| Keyword / Strategy | Use Case | Example |
| :-- | :-- | :-- |
| `backup` | Failover to a backup server | `server backend backup;` |
| `weight` | Shape traffic by server capacity | `server backend weight=5;` |
| Canary | Gradual rollout of a new version | `split_clients ... {}` |
| Blue-Green | Zero-downtime switch between two environments | Change the upstream target |

**Tip:** Use `backup` for reliability, `weight` for load shaping, and config switching (`split_clients` or upstream edits) for safer, faster deployments.
