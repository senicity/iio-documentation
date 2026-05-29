# Deployment Guide

## Overview

InstanceIO supports multiple deployment strategies, from traditional PHP servers to modern serverless architectures. This guide covers all deployment options with step-by-step instructions.

## Deployment Options

### 1. Static HTML Export
- **Best for**: Blogs, marketing sites, documentation
- **Hosting**: S3, Netlify, Vercel, GitHub Pages
- **Requirements**: No PHP runtime needed
- **Performance**: Excellent (pre-rendered HTML)

### 2. Dynamic PHP Application
- **Best for**: Web applications, dashboards, CMS
- **Hosting**: Traditional web servers, PaaS
- **Requirements**: PHP 8.1+, web server
- **Performance**: Good (runtime rendering)

### 3. Docker Container
- **Best for**: Microservices, cloud-native apps
- **Hosting**: Docker, Kubernetes, ECS, GKE
- **Requirements**: Docker runtime
- **Performance**: Excellent (containerized)

### 4. Serverless Functions
- **Best for**: API endpoints, micro-frontends
- **Hosting**: AWS Lambda, Cloudflare Workers
- **Requirements**: Serverless platform
- **Performance**: Variable (cold starts)

## Preparation

### 1. Environment Configuration
Update `config.json` for production:

```json
{
  "name": "My Production App",
  "template": "geneva",
  "cache": true,
  "assets": {
    "url": "https://cdn.example.com"
  },
  "api": {
    "endpoint": "https://api.example.com"
  }
}
```

### 2. Build Optimization
```bash
# Enable production optimizations
php console/cmd.php Build --optimize

# Minify assets
php console/cmd.php Build --minify

# Generate sitemap
php console/cmd.php Build --sitemap
```

### 3. Security Review
- Update `.htaccess` or `nginx.conf`
- Configure SSL certificates
- Set proper file permissions
- Enable security headers

## Static HTML Deployment

### 1. Build Static Site
```bash
# Build with production settings
php console/cmd.php Build --production

# Output: dist/ directory with HTML files
```

### 2. Deploy to AWS S3
```bash
# Install AWS CLI
aws configure

# Sync to S3 bucket
aws s3 sync dist/ s3://your-bucket-name --delete

# Enable static website hosting
aws s3 website s3://your-bucket-name --index-document index.html

# Set up CloudFront CDN (optional)
aws cloudfront create-distribution \
  --origin-domain-name your-bucket-name.s3.amazonaws.com
```

### 3. Deploy to Netlify
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Deploy from dist directory
netlify deploy --dir=dist --prod

# Or connect Git repository
netlify init
```

### 4. Deploy to Vercel
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel --prod

# Or connect Git repository
vercel git connect
```

### 5. Deploy to GitHub Pages
```bash
# Create gh-pages branch
git checkout --orphan gh-pages
git rm -rf .
cp -r dist/* .
git add .
git commit -m "Deploy to GitHub Pages"
git push origin gh-pages

# Enable in repository settings
# Settings → Pages → Source: gh-pages branch
```

## Dynamic PHP Deployment

### 1. Traditional Web Server (Apache)

**Apache Configuration:**
```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html/public
    
    <Directory /var/www/html/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

**Deployment Steps:**
```bash
# Copy files to server
rsync -avz --exclude='.git' --exclude='node_modules' ./ user@server:/var/www/html/

# Set permissions
chown -R www-data:www-data /var/www/html
chmod -R 755 /var/www/html

# Restart Apache
systemctl restart apache2
```

### 2. Traditional Web Server (Nginx)

**Nginx Configuration:**
```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/html/public;
    index index.php;
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }
    
    location ~ /\.ht {
        deny all;
    }
}
```

**Deployment Steps:**
```bash
# Copy files to server
scp -r ./ user@server:/var/www/html/

# Set permissions
chown -R nginx:nginx /var/www/html
chmod -R 755 /var/www/html

# Restart Nginx and PHP-FPM
systemctl restart nginx
systemctl restart php8.3-fpm
```

### 3. Platform as a Service (PaaS)

**Heroku:**
```bash
# Create Procfile
echo "web: vendor/bin/heroku-php-apache2 public/" > Procfile

# Deploy
git push heroku main

# Set environment variables
heroku config:set APP_ENV=production
```

**DigitalOcean App Platform:**
```yaml
# app.yaml
name: instanceio-app
region: nyc
services:
- name: web
  github:
    branch: main
    deploy_on_push: true
  build_command: php install.php
  run_command: php -S 0.0.0.0:8080 -t public
  http_port: 8080
  instance_count: 1
  instance_size_slug: basic-xxs
```

## Docker Deployment

### 1. Build Production Image
```bash
# Build PHP application image
php console/cmd.php Docker build prod-app

# Or build static HTML image
php console/cmd.php Docker build prod-html
```

### 2. Run Container
```bash
# Run PHP application
docker run -d -p 80:80 --name instanceio-app instanceio/prod-app

# Run static HTML
docker run -d -p 80:80 --name instanceio-html instanceio/prod-html
```

### 3. Docker Compose
```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    image: instanceio/prod-app
    build:
      context: .
      dockerfile: Dockerfile.prod
    ports:
      - "80:80"
    environment:
      - APP_ENV=production
      - CACHE_DRIVER=redis
    volumes:
      - ./storage:/var/www/html/storage
    depends_on:
      - redis
  
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
```

### 4. Kubernetes Deployment
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: instanceio-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: instanceio
  template:
    metadata:
      labels:
        app: instanceio
    spec:
      containers:
      - name: instanceio
        image: instanceio/prod-app:latest
        ports:
        - containerPort: 80
        env:
        - name: APP_ENV
          value: "production"
---
apiVersion: v1
kind: Service
metadata:
  name: instanceio-service
spec:
  selector:
    app: instanceio
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```

## Serverless Deployment

### 1. AWS Lambda
```bash
# Install Bref for PHP on Lambda
composer require bref/bref

# Create serverless.yml
service: instanceio-app

provider:
  name: aws
  runtime: provided.al2

functions:
  web:
    handler: public/index.php
    layers:
      - ${bref:layer.php-83-fpm}
    events:
      - httpApi: '*'

plugins:
  - ./vendor/bref/bref
```

### 2. Cloudflare Workers
```javascript
// worker.js
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  // Route requests to appropriate handler
  const url = new URL(request.url)
  
  if (url.pathname.startsWith('/api/')) {
    return handleAPI(request)
  } else {
    return serveStatic(request)
  }
}
```

## Database Deployment

### 1. MySQL/MariaDB
```sql
-- Create database and user
CREATE DATABASE instanceio_db;
CREATE USER 'instanceio_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON instanceio_db.* TO 'instanceio_user'@'localhost';
FLUSH PRIVILEGES;
```

### 2. PostgreSQL
```sql
-- Create database and user
CREATE DATABASE instanceio_db;
CREATE USER instanceio_user WITH PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE instanceio_db TO instanceio_user;
```

### 3. Redis (for caching)
```bash
# Install Redis
apt-get install redis-server

# Configure Redis
vim /etc/redis/redis.conf

# Test connection
redis-cli ping
```

## CDN Configuration

### 1. CloudFront (AWS)
```bash
# Create distribution
aws cloudfront create-distribution \
  --origin-domain-name example.com \
  --default-root-object index.html \
  --enabled \
  --comment "InstanceIO CDN"
```

### 2. Cloudflare
1. Add domain to Cloudflare
2. Update nameservers
3. Configure caching rules
4. Enable SSL/TLS
5. Set up page rules

### 3. Fastly
```bash
# Configure VCL
sub vcl_recv {
  # Cache static assets
  if (req.url ~ "^/assets/") {
    set req.backend_hint = assets;
  }
}
```

## Monitoring & Logging

### 1. Application Logging
```php
// Enable logging in config.json
{
  "logging": {
    "enabled": true,
    "level": "error",
    "path": "/var/log/instanceio"
  }
}
```

### 2. Error Tracking
- **Sentry**: PHP error tracking
- **Bugsnag**: Application monitoring
- **Rollbar**: Real-time error monitoring

### 3. Performance Monitoring
- **New Relic**: Application performance
- **Datadog**: Infrastructure monitoring
- **Blackfire**: PHP profiling

## Backup Strategy

### 1. Database Backups
```bash
# MySQL backup script
#!/bin/bash
BACKUP_DIR="/backups/mysql"
DATE=$(date +%Y%m%d_%H%M%S)
mysqldump -u user -p password instanceio_db > $BACKUP_DIR/backup_$DATE.sql

# Keep 30 days of backups
find $BACKUP_DIR -name "*.sql" -mtime +30 -delete
```

### 2. File Backups
```bash
# Backup application files
tar -czf /backups/files/instanceio_$(date +%Y%m%d).tar.gz /var/www/html

# Sync to S3
aws s3 sync /backups s3://backup-bucket/instanceio/
```

### 3. Automated Backups
```cron
# Daily database backup at 2 AM
0 2 * * * /usr/local/bin/backup-database.sh

# Weekly file backup on Sunday at 3 AM
0 3 * * 0 /usr/local/bin/backup-files.sh
```

## Security Hardening

### 1. SSL/TLS Configuration
```nginx
# Nginx SSL configuration
ssl_certificate /etc/ssl/certs/example.com.crt;
ssl_certificate_key /etc/ssl/private/example.com.key;
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers HIGH:!aNULL:!MD5;
```

### 2. Security Headers
```php
// Add security headers
header("X-Frame-Options: DENY");
header("X-Content-Type-Options: nosniff");
header("X-XSS-Protection: 1; mode=block");
header("Referrer-Policy: strict-origin-when-cross-origin");
header("Content-Security-Policy: default-src 'self'");
```

### 3. File Permissions
```bash
# Secure file permissions
chmod 644 /var/www/html/*.php
chmod 755 /var/www/html/public
chmod 600 /var/www/html/config.json
chown -R www-data:www-data /var/www/html
```

## Scaling Strategies

### 1. Horizontal Scaling
- Load balancer (HAProxy, Nginx)
- Multiple application servers
- Shared session storage (Redis)
- Centralized logging

### 2. Vertical Scaling
- Upgrade server resources
- Optimize PHP configuration
- Enable OPcache
- Database optimization

### 3. Database Scaling
- Read replicas
- Database partitioning
- Connection pooling
- Query caching

## Disaster Recovery

### 1. Recovery Plan
1. **Identification**: Detect failure
2. **Containment**: Isolate affected systems
3. **Recovery**: Restore from backup
4. **Testing**: Verify functionality
5. **Documentation**: Update procedures

### 2. Recovery Time Objectives (RTO)
- **Critical**: 1 hour
- **Important**: 4 hours
- **Normal**: 24 hours

### 3. Recovery Point Objectives (RPO)
- **Critical**: 15 minutes
- **Important**: 1 hour
- **Normal**: 24 hours

## Cost Optimization

### 1. Cloud Cost Management
- Right-size instances
- Use reserved instances
- Implement auto-scaling
- Monitor usage patterns

### 2. CDN Optimization
- Enable compression
- Set cache headers
- Use HTTP/2
- Implement image optimization

### 3. Database Optimization
- Query optimization
- Index management
- Connection pooling
- Read replicas for heavy reads

## Deployment Checklist

### Pre-Deployment
- [ ] Test in staging environment
- [ ] Backup current production
- [ ] Update configuration for production
- [ ] Verify dependencies
- [ ] Check security settings

### During Deployment
- [ ] Deploy to production
- [ ] Run database migrations
- [ ] Clear cache
- [ ] Verify functionality
- [ ] Monitor error logs

### Post-Deployment
- [ ] Test all features
- [ ] Monitor performance
- [ ] Check security headers
- [ ] Update documentation
- [ ] Notify stakeholders

## Troubleshooting

### Common Deployment Issues

**1. 500 Internal Server Error**
- Check PHP error logs
- Verify file permissions
- Check PHP extensions
- Review .htaccess configuration

**2. Database Connection Issues**
- Verify credentials
- Check network connectivity
- Ensure database is running
- Review firewall rules

**3. Asset Loading Problems**
- Check CDN configuration
- Verify asset paths
- Clear browser cache
- Check CORS headers

**4. Performance Issues**
- Enable caching
- Optimize images
- Minify assets
- Database query optimization

## Support & Resources

### Documentation
- This deployment guide
- Framework documentation
- Platform-specific guides

### Community
- Licensed user forums
- Example deployments
- Best practices sharing

### Professional Services
- Deployment consulting
- Performance optimization
- Security auditing
- Training and support

## Next Steps

1. **Choose Deployment Strategy**: Select based on your needs
2. **Prepare Environment**: Configure for production
3. **Test Deployment**: Verify in staging
4. **Deploy to Production**: Follow deployment checklist
5. **Monitor and Optimize**: Continuous improvement

**Need help with deployment? Contact us at info@senicity.com**

---

*InstanceIO - Deploy with confidence, scale with ease.*