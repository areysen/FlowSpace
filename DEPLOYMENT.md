# FlowSpace Deployment Guide

This document provides detailed instructions for deploying the FlowSpace landing page to various environments.

## Current Deployment

The FlowSpace landing page is currently deployed on **Netlify** at:
**https://flowspace-app.netlify.app**

## Prerequisites

- Node.js (v14 or higher) for build tools
- Git for version control
- Basic knowledge of command line operations
- Access to a web server or hosting platform

## Local Development Setup

1. Clone the repository:

```bash
git clone https://github.com/areysen/flowspace.git
cd flowspace
```

2. Install development dependencies (optional):

```bash
npm install
```

3. Start local development server:

```bash
npx serve
# or
python -m http.server 8000
```

## Production Deployment

### 1. Netlify Deployment (Current)

The site is deployed on Netlify with the following configuration:

- **Site URL**: https://flowspace-app.netlify.app
- **Repository**: https://github.com/areysen/flowspace
- **Build Command**: None (static site)
- **Publish Directory**: `./` (root directory)
- **Branch**: main

#### Netlify Features Enabled:

- Automatic HTTPS
- Global CDN
- Automatic deployments on git push
- Custom domain support (if needed)
- Form handling
- Redirects and rewrites

### 2. Build Process

1. Optimize images:

```bash
# Using Sharp.js for image optimization
npx sharp-cli -i ./images/* -o ./dist/images
```

2. Minify CSS:

```bash
# Using clean-css-cli
npx cleancss -o ./dist/styles.min.css ./styles.css
```

3. Minify JavaScript:

```bash
# Using Terser
npx terser ./script.js -o ./dist/script.min.js -c -m
```

### 3. Alternative Static Hosting

#### Vercel

1. Import your GitHub repository
2. Configure project settings
3. Deploy with default settings
4. Set up custom domain (optional)

#### GitHub Pages

1. Go to repository settings
2. Enable GitHub Pages
3. Select branch and directory
4. Configure custom domain (optional)

### 4. Traditional Web Hosting

1. Upload files via FTP/SFTP:

```bash
# Example using lftp
lftp -u username,password ftp.yourserver.com
mirror -R ./dist /public_html
```

2. Configure server settings:
   - Set correct file permissions
   - Configure MIME types
   - Enable GZIP compression

## Environment Setup

### 1. Server Requirements

- Apache or Nginx web server
- PHP (optional, for form processing)
- SSL certificate for HTTPS
- Proper MIME type configuration

### 2. Apache Configuration

```apache
# .htaccess
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
</IfModule>

# Enable GZIP compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css application/javascript
</IfModule>

# Set security headers
<IfModule mod_headers.c>
    Header set X-Content-Type-Options "nosniff"
    Header set X-Frame-Options "SAMEORIGIN"
    Header set X-XSS-Protection "1; mode=block"
    Header set Referrer-Policy "strict-origin-when-cross-origin"
    Header set Permissions-Policy "interest-cohort=()"
</IfModule>

# Cache control
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/webp "access plus 1 year"
    ExpiresByType text/css "access plus 1 month"
    ExpiresByType application/javascript "access plus 1 month"
</IfModule>
```

### 3. Nginx Configuration

```nginx
# nginx.conf
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    root /var/www/html;
    index index.html;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Permissions-Policy "interest-cohort=()" always;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/javascript image/svg+xml;
    gzip_min_length 1000;

    # Cache control
    location ~* \.(jpg|jpeg|png|webp|svg|ico)$ {
        expires 1y;
        add_header Cache-Control "public, no-transform";
    }

    location ~* \.(css|js)$ {
        expires 1M;
        add_header Cache-Control "public, no-transform";
    }
}
```

## Performance Monitoring

### 1. Setup Monitoring Tools

- Google Analytics 4
- Google Search Console
- Web Vitals monitoring

### 2. Configure Error Tracking

- Set up error logging
- Configure performance monitoring
- Implement uptime monitoring

## SSL/TLS Setup

1. Obtain SSL certificate:

```bash
# Using Let's Encrypt
certbot --nginx -d yourdomain.com
```

2. Configure automatic renewal:

```bash
# Add to crontab
0 0 1 * * /usr/bin/certbot renew --quiet
```

## Backup Strategy

1. Configure automated backups:

```bash
# Example backup script
#!/bin/bash
DATE=$(date +%Y%m%d)
tar -czf backup-$DATE.tar.gz ./
aws s3 cp backup-$DATE.tar.gz s3://your-bucket/
```

2. Set up backup rotation:

```bash
# Keep last 30 days of backups
find ./backups -type f -mtime +30 -delete
```

## Troubleshooting

### Common Issues

1. **404 Errors**

   - Check file permissions
   - Verify .htaccess configuration
   - Confirm file paths

2. **SSL Issues**

   - Verify certificate installation
   - Check SSL configuration
   - Confirm DNS settings

3. **Performance Issues**
   - Review server logs
   - Check resource usage
   - Verify caching configuration

## Maintenance

### Regular Tasks

1. Monitor performance metrics
2. Update dependencies
3. Renew SSL certificates
4. Review security headers
5. Check error logs

### Security Updates

1. Keep server software updated
2. Review access logs
3. Update security headers
4. Perform security audits
