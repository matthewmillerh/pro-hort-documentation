---
layout: default
title: Dashboard Deployment Guide
parent: Dashboard
---

# Dashboard Deployment Guide

## Overview

This guide covers deploying the Pro-Hort Dashboard to production environments.

## Deployment Options

### Option 1: Netlify (Recommended for Quick Setup)

#### Prerequisites
- Netlify account (free at netlify.com)
- GitHub repository

#### Steps

1. **Connect to GitHub**
   - Log into Netlify
   - Click "New site from Git"
   - Select GitHub and authorize
   - Choose the dashboard repository

2. **Configure Build Settings**
   - Build command: `npm run build`
   - Publish directory: `dist`
   - Leave Node version at default (or set to 18+)

3. **Environment Variables**
   - Click "Build & Deploy" → "Environment"
   - Add environment variables:
     ```
     VITE_API_URL=https://your-api-domain.com/api
     ```

4. **Deploy**
   - Push changes to GitHub
   - Netlify automatically builds and deploys

#### Netlify Configuration File

Create `netlify.toml` in the root:

```toml
[build]
command = "npm run build"
publish = "dist"

[build.environment]
NODE_VERSION = "18"

[[redirects]]
from = "/*"
to = "/index.html"
status = 200
```

### Option 2: Docker Deployment

#### Dockerfile

```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### Build and Deploy

```bash
# Build Docker image
docker build -t pro-hort-dashboard:latest .

# Tag for registry
docker tag pro-hort-dashboard:latest your-registry/pro-hort-dashboard:latest

# Push to registry
docker push your-registry/pro-hort-dashboard:latest

# Run container
docker run -p 80:80 your-registry/pro-hort-dashboard:latest
```

### Option 3: VPS Deployment

#### Prerequisites
- VPS with Ubuntu/Debian
- Node.js 18+
- Nginx or Apache
- SSL certificate (Let's Encrypt)

#### Setup Steps

1. **SSH into VPS**
   ```bash
   ssh root@your-vps-ip
   ```

2. **Install Dependencies**
   ```bash
   sudo apt update
   sudo apt install nodejs npm nginx
   ```

3. **Clone Repository**
   ```bash
   cd /var/www
   git clone <dashboard-repo-url> pro-hort-dashboard
   cd pro-hort-dashboard
   ```

4. **Install and Build**
   ```bash
   npm install
   npm run build
   ```

5. **Configure Nginx**

   Create `/etc/nginx/sites-available/pro-hort-dashboard`:

   ```nginx
   server {
       listen 80;
       server_name your-domain.com;
       
       location / {
           alias /var/www/pro-hort-dashboard/dist/;
           try_files $uri $uri/ /index.html;
       }
       
       location /api {
           proxy_pass http://localhost:3000;
           proxy_http_version 1.1;
       }
   }
   ```

6. **Enable Site**
   ```bash
   sudo ln -s /etc/nginx/sites-available/pro-hort-dashboard \
              /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

7. **SSL Certificate (Let's Encrypt)**
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

#### CI/CD with GitHub Actions

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to VPS

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install dependencies
        run: npm install
      
      - name: Build
        run: npm run build
      
      - name: Deploy to VPS
        uses: appleboy/scp-action@master
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}
          source: "dist/"
          target: "/var/www/pro-hort-dashboard"
      
      - name: Restart service
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}
          script: sudo systemctl restart nginx
```

## Environment Configuration

### Production Environment Variables

```env
VITE_API_URL=https://api.your-domain.com/api
VITE_APP_NAME=Pro-Hort Dashboard
NODE_ENV=production
```

### API Endpoints

Ensure your API endpoint is configured correctly:
- Development: `http://localhost:3000/api`
- Production: `https://api.your-domain.com/api`

## Performance Optimization

### Build Optimization

```bash
# Generate production build with analysis
npm run build -- --analyze

# Check bundle size
npm run build
```

### Caching Strategies

The Nginx configuration should include:
```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
    expires 30d;
    add_header Cache-Control "public, immutable";
}

location / {
    expires -1;
    add_header Cache-Control "no-cache, no-store, must-revalidate";
}
```

## Monitoring and Logging

### Nginx Logs

```bash
# Access logs
tail -f /var/log/nginx/access.log

# Error logs
tail -f /var/log/nginx/error.log
```

### Browser Console

Monitor browser console for JavaScript errors in production.

## Troubleshooting

### 404 Errors on Routes

Ensure `try_files $uri $uri/ /index.html;` is in Nginx config for SPA routing.

### CORS Issues

Configure server API to accept requests from your domain:
```
Access-Control-Allow-Origin: https://your-domain.com
```

### SSL Certificate Issues

```bash
# Check certificate status
sudo certbot certificates

# Renew certificate
sudo certbot renew
```

## Rollback Procedure

Keep previous builds for quick rollback:

```bash
# Keep backup
cp -r dist dist.backup

# Deploy new version
npm run build

# Rollback if needed
rm -rf dist
mv dist.backup dist
```

## Next Steps

- Monitor performance metrics
- Set up automated backups
- Configure monitoring and alerts
- Review [Usage Guide](02-usage.md)
