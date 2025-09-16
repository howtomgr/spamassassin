# SpamAssassin Installation Guide

SpamAssassin is a free and open-source Anti-Spam. An extensible email filter to identify spam

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 783 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 783 (default spamassassin port)
- **Dependencies**:
  - perl, perl-Mail-SpamAssassin
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install spamassassin
sudo dnf install -y spamassassin perl, perl-Mail-SpamAssassin

# Enable and start service
sudo systemctl enable --now spamassassin

# Configure firewall
sudo firewall-cmd --permanent --add-service=spamassassin
sudo firewall-cmd --reload

# Verify installation
spamassassin --version || systemctl status spamassassin
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install spamassassin
sudo apt install -y spamassassin perl, perl-Mail-SpamAssassin

# Enable and start service
sudo systemctl enable --now spamassassin

# Configure firewall
sudo ufw allow 783

# Verify installation
spamassassin --version || systemctl status spamassassin
```

### Arch Linux

```bash
# Install spamassassin
sudo pacman -S spamassassin

# Enable and start service
sudo systemctl enable --now spamassassin

# Verify installation
spamassassin --version || systemctl status spamassassin
```

### Alpine Linux

```bash
# Install spamassassin
apk add --no-cache spamassassin

# Enable and start service
rc-update add spamassassin default
rc-service spamassassin start

# Verify installation
spamassassin --version || rc-service spamassassin status
```

### openSUSE/SLES

```bash
# Install spamassassin
sudo zypper install -y spamassassin perl, perl-Mail-SpamAssassin

# Enable and start service
sudo systemctl enable --now spamassassin

# Configure firewall
sudo firewall-cmd --permanent --add-service=spamassassin
sudo firewall-cmd --reload

# Verify installation
spamassassin --version || systemctl status spamassassin
```

### macOS

```bash
# Using Homebrew
brew install spamassassin

# Start service
brew services start spamassassin

# Verify installation
spamassassin --version
```

### FreeBSD

```bash
# Using pkg
pkg install spamassassin

# Enable in rc.conf
echo 'spamassassin_enable="YES"' >> /etc/rc.conf

# Start service
service spamassassin start

# Verify installation
spamassassin --version || service spamassassin status
```

### Windows

```powershell
# Using Chocolatey
choco install spamassassin

# Or using Scoop
scoop install spamassassin

# Verify installation
spamassassin --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/spamassassin

# Set up basic configuration
sudo tee /etc/spamassassin/spamassassin.conf << 'EOF'
# SpamAssassin Configuration
--max-children 5 --max-connections-per-child 200
EOF

# Test configuration
sudo spamassassin -t || sudo spamassassin configtest

# Reload service
sudo systemctl reload spamassassin
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R spamassassin:spamassassin /etc/spamassassin
sudo chmod 750 /etc/spamassassin

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable spamassassin

# Start service
sudo systemctl start spamassassin

# Stop service
sudo systemctl stop spamassassin

# Restart service
sudo systemctl restart spamassassin

# Reload configuration
sudo systemctl reload spamassassin

# Check status
sudo systemctl status spamassassin

# View logs
sudo journalctl -u spamassassin -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add spamassassin default

# Start service
rc-service spamassassin start

# Stop service
rc-service spamassassin stop

# Restart service
rc-service spamassassin restart

# Check status
rc-service spamassassin status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'spamassassin_enable="YES"' >> /etc/rc.conf

# Start service
service spamassassin start

# Stop service
service spamassassin stop

# Restart service
service spamassassin restart

# Check status
service spamassassin status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start spamassassin
brew services stop spamassassin
brew services restart spamassassin

# Check status
brew services list | grep spamassassin
```

### Windows Service Manager

```powershell
# Start service
net start spamassassin

# Stop service
net stop spamassassin

# Using PowerShell
Start-Service spamassassin
Stop-Service spamassassin
Restart-Service spamassassin

# Check status
Get-Service spamassassin
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/spamassassin/spamassassin.conf << 'EOF'
--max-children 5 --max-connections-per-child 200
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart spamassassin
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream spamassassin_backend {
    server 127.0.0.1:783;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name spamassassin.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name spamassassin.example.com;

    ssl_certificate /etc/ssl/certs/spamassassin.example.com.crt;
    ssl_certificate_key /etc/ssl/private/spamassassin.example.com.key;

    location / {
        proxy_pass http://spamassassin_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName spamassassin.example.com
    Redirect permanent / https://spamassassin.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName spamassassin.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/spamassassin.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/spamassassin.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:783/
    ProxyPassReverse / http://127.0.0.1:783/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:783/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend spamassassin_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/spamassassin.pem
    redirect scheme https if !{ ssl_fc }
    default_backend spamassassin_backend

backend spamassassin_backend
    balance roundrobin
    option httpchk GET /health
    server spamassassin1 127.0.0.1:783 check
    server spamassassin2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R spamassassin:spamassassin /etc/spamassassin
sudo chmod 750 /etc/spamassassin

# Configure firewall
sudo firewall-cmd --permanent --add-service=spamassassin
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/spamassassin.conf << 'EOF'
[spamassassin]
enabled = true
port = 783
filter = spamassassin
logpath = /var/log/spamassassin/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/spamassassin.key \
    -out /etc/ssl/certs/spamassassin.crt

# Configure SSL in spamassassin
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE spamassassin_db;
CREATE USER spamassassin_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE spamassassin_db TO spamassassin_user;
EOF

# Configure spamassassin to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE spamassassin_db;
CREATE USER 'spamassassin_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON spamassassin_db.* TO 'spamassassin_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# SpamAssassin specific tuning
--max-children 5 --max-connections-per-child 200
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
spamassassin soft nofile 65535
spamassassin hard nofile 65535
spamassassin soft nproc 32768
spamassassin hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'spamassassin'
    static_configs:
      - targets: ['localhost:783']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet spamassassin; then
    echo "SpamAssassin is running"
    exit 0
else
    echo "SpamAssassin is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/spamassassin << 'EOF'
/var/log/spamassassin/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 spamassassin spamassassin
    postrotate
        systemctl reload spamassassin > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# SpamAssassin backup script
BACKUP_DIR="/backup/spamassassin"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop spamassassin

# Backup configuration
tar -czf "$BACKUP_DIR/spamassassin-config-$DATE.tar.gz" /etc/spamassassin

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/spamassassin-data-$DATE.tar.gz" /var/lib/spamassassin

# Start service
systemctl start spamassassin

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop spamassassin

# Restore configuration
sudo tar -xzf /backup/spamassassin/spamassassin-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/spamassassin/spamassassin-data-*.tar.gz -C /

# Set permissions
sudo chown -R spamassassin:spamassassin /etc/spamassassin
sudo chown -R spamassassin:spamassassin /var/lib/spamassassin

# Start service
sudo systemctl start spamassassin
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u spamassassin -n 100
sudo tail -f /var/log/spamassassin/*.log

# Check configuration
sudo spamassassin -t || sudo spamassassin configtest

# Check permissions
ls -la /etc/spamassassin
ls -la /var/lib/spamassassin
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 783
sudo netstat -tlnp | grep 783

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 783
nc -zv localhost 783
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep spamd)
htop -p $(pgrep spamd)

# Check connections
ss -ant | grep :783 | wc -l

# Monitor I/O
iotop -p $(pgrep spamd)
```

### Debug Mode

```bash
# Run in debug mode
sudo spamassassin -d
# or
sudo spamassassin debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  spamassassin:
    image: spamassassin:latest
    container_name: spamassassin
    ports:
      - "783:783"
    volumes:
      - ./config:/etc/spamassassin
      - ./data:/var/lib/spamassassin
    environment:
      - spamassassin_CONFIG=/etc/spamassassin/spamassassin.conf
    restart: unless-stopped
    networks:
      - spamassassin_net

networks:
  spamassassin_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spamassassin
spec:
  replicas: 3
  selector:
    matchLabels:
      app: spamassassin
  template:
    metadata:
      labels:
        app: spamassassin
    spec:
      containers:
      - name: spamassassin
        image: spamassassin:latest
        ports:
        - containerPort: 783
        volumeMounts:
        - name: config
          mountPath: /etc/spamassassin
      volumes:
      - name: config
        configMap:
          name: spamassassin-config
---
apiVersion: v1
kind: Service
metadata:
  name: spamassassin
spec:
  selector:
    app: spamassassin
  ports:
  - port: 783
    targetPort: 783
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure SpamAssassin
  hosts: all
  become: yes
  tasks:
    - name: Install spamassassin
      package:
        name: spamassassin
        state: present
    
    - name: Configure spamassassin
      template:
        src: spamassassin.conf.j2
        dest: /etc/spamassassin/spamassassin.conf
        owner: spamassassin
        group: spamassassin
        mode: '0640'
      notify: restart spamassassin
    
    - name: Start and enable spamassassin
      systemd:
        name: spamassassin
        state: started
        enabled: yes
  
  handlers:
    - name: restart spamassassin
      systemd:
        name: spamassassin
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update spamassassin

# Debian/Ubuntu
sudo apt update && sudo apt upgrade spamassassin

# Arch Linux
sudo pacman -Syu spamassassin

# Alpine Linux
apk update && apk upgrade spamassassin

# openSUSE
sudo zypper update spamassassin

# FreeBSD
pkg update && pkg upgrade spamassassin

# Always backup before updates
tar -czf /backup/spamassassin-pre-update-$(date +%Y%m%d).tar.gz /etc/spamassassin

# Restart after updates
sudo systemctl restart spamassassin
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/spamassassin -name "*.log" -mtime +30 -delete

# Verify integrity
sudo spamassassin --verify || sudo spamassassin check

# Update databases (if applicable)
sudo spamassassin-update-db

# Optimize performance
sudo spamassassin-optimize

# Check for security updates
sudo spamassassin --security-check
```

## Additional Resources

- Official Documentation: https://docs.spamassassin.org/
- GitHub Repository: https://github.com/spamassassin/spamassassin
- Community Forum: https://forum.spamassassin.org/
- Wiki: https://wiki.spamassassin.org/
- Comparison vs Rspamd, ASSP, MailScanner, Bogofilter: https://docs.spamassassin.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
