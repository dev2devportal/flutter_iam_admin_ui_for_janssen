# Installation Guide

This guide provides comprehensive instructions for installing and configuring the Flutter IAM Admin UI for Janssen in both development and production environments.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Development Environment Setup](#development-environment-setup)
- [Production Environment Setup](#production-environment-setup)
- [Database Configuration](#database-configuration)
- [Web Server Configuration](#web-server-configuration)
- [SSL Certificate Setup](#ssl-certificate-setup)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### Required Software
1. Flutter SDK (3.22+)
   ```bash
   # Install Flutter
   git clone https://github.com/flutter/flutter.git
   export PATH="$PATH:`pwd`/flutter/bin"
   flutter doctor
   
   # Enable web support
   flutter config --enable-web
   ```

2. Janssen Server (1.1.1+)
   - jans-auth-server
   - jans-config-api
   - jans-fido2
   - jans-scim

3. Database Server
   - PostgreSQL 12+ or MySQL 8+
   - Redis 6+ (for caching)

4. Web Server
   - Nginx (recommended) or Apache
   - Valid SSL certificates

### System Requirements
- CPU: 2+ cores
- RAM: 4GB minimum (8GB recommended)
- Storage: 20GB minimum
- Network: Static IP and domain name
- Operating System: Linux (Ubuntu 20.04+ or Debian 11+ recommended)

## Development Environment Setup

1. Clone Repository
   ```bash
   git clone https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen.git
   cd flutter_iam_admin_ui_for_janssen
   ```

2. Install Dependencies
   ```bash
   flutter pub get
   ```

3. Configure Development Settings
   ```bash
   # Copy example configurations
   cp config/janssen_config.example.yaml config/janssen_config.yaml
   cp config/oauth2_config.example.yaml config/oauth2_config.yaml
   cp config/ui_config.example.yaml config/ui_config.yaml
   
   # Edit configurations with your settings
   vim config/janssen_config.yaml
   ```

4. Setup Development Database
   ```bash
   # PostgreSQL setup
   sudo -u postgres psql
   CREATE DATABASE janssen_admin;
   CREATE USER janssen_admin WITH ENCRYPTED PASSWORD 'your_password';
   GRANT ALL PRIVILEGES ON DATABASE janssen_admin TO janssen_admin;
   ```

5. Run Development Server
   ```bash
   # Basic development server
   flutter run -d chrome --web-port=18080 --web-renderer=html

   # With specific environment
   flutter run --dart-define=ENVIRONMENT=development
   ```

## Production Environment Setup

1. Build Production Release
   ```bash
   # Generate production build
   flutter build web --release --web-renderer html
   
   # Optimize assets
   flutter pub run flutter_asset_builder
   ```

2. Configure Production Database
   ```bash
   # Create production database
   sudo -u postgres psql
   CREATE DATABASE janssen_admin_prod;
   CREATE USER janssen_admin_prod WITH ENCRYPTED PASSWORD 'secure_password';
   GRANT ALL PRIVILEGES ON DATABASE janssen_admin_prod TO janssen_admin_prod;
   ```

3. Web Server Setup
   ```bash
   # Install Nginx
   sudo apt update
   sudo apt install nginx

   # Configure SSL
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d your.domain.com
   ```

4. Deploy Application
   ```bash
   # Copy build files
   sudo mkdir -p /var/www/janssen-admin
   sudo cp -r build/web/* /var/www/janssen-admin/
   
   # Set permissions
   sudo chown -R www-data:www-data /var/www/janssen-admin
   ```

## Database Configuration

### PostgreSQL Setup
```sql
-- Create extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "hstore";

-- Create tables
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    username VARCHAR(255) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Set up indexes
CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_users_email ON users(email);
```

### Redis Configuration
```bash
# Install Redis
sudo apt install redis-server

# Configure Redis
sudo vim /etc/redis/redis.conf

# Important settings
maxmemory 2gb
maxmemory-policy allkeys-lru
```

## Web Server Configuration

### Nginx Configuration
```nginx
server {
    listen 443 ssl http2;
    server_name your.domain.com;

    root /var/www/janssen-admin;
    index index.html;

    # SSL configuration
    ssl_certificate /etc/letsencrypt/live/your.domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your.domain.com/privkey.pem;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    # Modern configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;

    # CORS configuration
    add_header 'Access-Control-Allow-Origin' 'https://your.janssen.server' always;
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT, DELETE' always;
    add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization' always;
    add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range' always;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Proxy configuration for Janssen server
    location /jans-auth/ {
        proxy_pass https://your.janssen.server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Security headers
    add_header Strict-Transport-Security "max-age=63072000" always;
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'";
}
```

## SSL Certificate Setup

1. Using Let's Encrypt
   ```bash
   # Install Certbot
   sudo apt install certbot python3-certbot-nginx
   
   # Generate certificate
   sudo certbot --nginx -d your.domain.com
   
   # Auto-renewal setup
   sudo systemctl enable certbot.timer
   sudo systemctl start certbot.timer
   ```

2. Using Self-signed Certificates (Development Only)
   ```bash
   # Generate self-signed certificate
   openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
     -keyout /etc/ssl/private/nginx-selfsigned.key \
     -out /etc/ssl/certs/nginx-selfsigned.crt
   ```

## Troubleshooting

### Common Issues

1. CORS Errors
   ```bash
   # Check CORS headers
   curl -I -X OPTIONS https://your.domain.com/api/endpoint
   
   # Verify Nginx CORS configuration
   sudo nginx -t
   ```

2. Database Connection Issues
   ```bash
   # Check PostgreSQL logs
   sudo tail -f /var/log/postgresql/postgresql-12-main.log
   
   # Verify connection
   psql -h localhost -U janssen_admin -d janssen_admin
   ```

3. SSL Certificate Problems
   ```bash
   # Test SSL configuration
   openssl s_client -connect your.domain.com:443 -tls1_2
   
   # Check certificate validity
   echo | openssl s_client -servername your.domain.com -connect your.domain.com:443 2>/dev/null | openssl x509 -noout -dates
   ```

### Debug Logging

1. Enable Flutter Web Debug Logging
   ```dart
   // In main.dart
   void main() {
     if (kDebugMode) {
       debugPrintRebuildDirtyWidgets = true;
       debugPrintLayouts = true;
     }
     runApp(MyApp());
   }
   ```

2. Nginx Debug Logs
   ```bash
   # Enable debug logging
   error_log /var/log/nginx/debug.log debug;
   
   # Monitor logs
   tail -f /var/log/nginx/debug.log
   ```

For additional assistance, please:
- Check our [Issue Tracker](https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen/issues)
- Join our [Discussion Forum](https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen/discussions)
- Review the [FAQ](docs/faq.md)
