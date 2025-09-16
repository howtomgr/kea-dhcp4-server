# kea Installation Guide

kea is a free and open-source modern DHCP server. Kea is ISCs next-generation DHCP server with JSON configuration and REST API, replacing ISC DHCP

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
  - CPU: 1 core minimum
  - RAM: 256MB minimum
  - Storage: 100MB for installation
  - Network: DHCP protocols
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 67 (default kea port)
  - Port 8000 for API
- **Dependencies**:
  - See official documentation for specific requirements
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

# Install kea
sudo dnf install -y kea-dhcp4-server

# Enable and start service
sudo systemctl enable --now kea-dhcp4

# Configure firewall
sudo firewall-cmd --permanent --add-port=67/tcp
sudo firewall-cmd --reload

# Verify installation
kea-dhcp4 -V
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install kea
sudo apt install -y kea-dhcp4-server

# Enable and start service
sudo systemctl enable --now kea-dhcp4

# Configure firewall
sudo ufw allow 67

# Verify installation
kea-dhcp4 -V
```

### Arch Linux

```bash
# Install kea
sudo pacman -S kea-dhcp4-server

# Enable and start service
sudo systemctl enable --now kea-dhcp4

# Verify installation
kea-dhcp4 -V
```

### Alpine Linux

```bash
# Install kea
apk add --no-cache kea-dhcp4-server

# Enable and start service
rc-update add kea-dhcp4 default
rc-service kea-dhcp4 start

# Verify installation
kea-dhcp4 -V
```

### openSUSE/SLES

```bash
# Install kea
sudo zypper install -y kea-dhcp4-server

# Enable and start service
sudo systemctl enable --now kea-dhcp4

# Configure firewall
sudo firewall-cmd --permanent --add-port=67/tcp
sudo firewall-cmd --reload

# Verify installation
kea-dhcp4 -V
```

### macOS

```bash
# Using Homebrew
brew install kea-dhcp4-server

# Start service
brew services start kea-dhcp4-server

# Verify installation
kea-dhcp4 -V
```

### FreeBSD

```bash
# Using pkg
pkg install kea-dhcp4-server

# Enable in rc.conf
echo 'kea-dhcp4_enable="YES"' >> /etc/rc.conf

# Start service
service kea-dhcp4 start

# Verify installation
kea-dhcp4 -V
```

### Windows

```bash
# Using Chocolatey
choco install kea-dhcp4-server

# Or using Scoop
scoop install kea-dhcp4-server

# Verify installation
kea-dhcp4 -V
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/kea-dhcp4-server

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
kea-dhcp4 -V
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable kea-dhcp4

# Start service
sudo systemctl start kea-dhcp4

# Stop service
sudo systemctl stop kea-dhcp4

# Restart service
sudo systemctl restart kea-dhcp4

# Check status
sudo systemctl status kea-dhcp4

# View logs
sudo journalctl -u kea-dhcp4 -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add kea-dhcp4 default

# Start service
rc-service kea-dhcp4 start

# Stop service
rc-service kea-dhcp4 stop

# Restart service
rc-service kea-dhcp4 restart

# Check status
rc-service kea-dhcp4 status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'kea-dhcp4_enable="YES"' >> /etc/rc.conf

# Start service
service kea-dhcp4 start

# Stop service
service kea-dhcp4 stop

# Restart service
service kea-dhcp4 restart

# Check status
service kea-dhcp4 status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start kea-dhcp4-server
brew services stop kea-dhcp4-server
brew services restart kea-dhcp4-server

# Check status
brew services list | grep kea-dhcp4-server
```

### Windows Service Manager

```powershell
# Start service
net start kea-dhcp4

# Stop service
net stop kea-dhcp4

# Using PowerShell
Start-Service kea-dhcp4
Stop-Service kea-dhcp4
Restart-Service kea-dhcp4

# Check status
Get-Service kea-dhcp4
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream kea-dhcp4-server_backend {
    server 127.0.0.1:67;
}

server {
    listen 80;
    server_name kea-dhcp4-server.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name kea-dhcp4-server.example.com;

    ssl_certificate /etc/ssl/certs/kea-dhcp4-server.example.com.crt;
    ssl_certificate_key /etc/ssl/private/kea-dhcp4-server.example.com.key;

    location / {
        proxy_pass http://kea-dhcp4-server_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName kea-dhcp4-server.example.com
    Redirect permanent / https://kea-dhcp4-server.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName kea-dhcp4-server.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/kea-dhcp4-server.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/kea-dhcp4-server.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:67/
    ProxyPassReverse / http://127.0.0.1:67/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend kea-dhcp4-server_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/kea-dhcp4-server.pem
    redirect scheme https if !{ ssl_fc }
    default_backend kea-dhcp4-server_backend

backend kea-dhcp4-server_backend
    balance roundrobin
    server kea-dhcp4-server1 127.0.0.1:67 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R kea-dhcp4-server:kea-dhcp4-server /etc/kea-dhcp4-server
sudo chmod 750 /etc/kea-dhcp4-server

# Configure firewall
sudo firewall-cmd --permanent --add-port=67/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status kea-dhcp4

# View logs
sudo journalctl -u kea-dhcp4 -f

# Monitor resource usage
top -p $(pgrep kea-dhcp4-server)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/kea-dhcp4-server"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/kea-dhcp4-server-backup-$DATE.tar.gz" /etc/kea-dhcp4-server /var/lib/kea-dhcp4-server

echo "Backup completed: $BACKUP_DIR/kea-dhcp4-server-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop kea-dhcp4

# Restore from backup
tar -xzf /backup/kea-dhcp4-server/kea-dhcp4-server-backup-*.tar.gz -C /

# Start service
sudo systemctl start kea-dhcp4
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u kea-dhcp4 -n 100
sudo tail -f /var/log/kea-dhcp4-server/kea-dhcp4-server.log

# Check configuration
kea-dhcp4 -V

# Check permissions
ls -la /etc/kea-dhcp4-server
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 67

# Test connectivity
telnet localhost 67

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep kea-dhcp4-server)

# Check disk I/O
iotop -p $(pgrep kea-dhcp4-server)

# Check connections
ss -an | grep 67
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  kea-dhcp4-server:
    image: kea-dhcp4-server:latest
    ports:
      - "67:67"
    volumes:
      - ./config:/etc/kea-dhcp4-server
      - ./data:/var/lib/kea-dhcp4-server
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update kea-dhcp4-server

# Debian/Ubuntu
sudo apt update && sudo apt upgrade kea-dhcp4-server

# Arch Linux
sudo pacman -Syu kea-dhcp4-server

# Alpine Linux
apk update && apk upgrade kea-dhcp4-server

# openSUSE
sudo zypper update kea-dhcp4-server

# FreeBSD
pkg update && pkg upgrade kea-dhcp4-server

# Always backup before updates
tar -czf /backup/kea-dhcp4-server-pre-update-$(date +%Y%m%d).tar.gz /etc/kea-dhcp4-server

# Restart after updates
sudo systemctl restart kea-dhcp4
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/kea-dhcp4-server

# Clean old logs
find /var/log/kea-dhcp4-server -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/kea-dhcp4-server
```

## Additional Resources

- Official Documentation: https://docs.kea-dhcp4-server.org/
- GitHub Repository: https://github.com/kea-dhcp4-server/kea-dhcp4-server
- Community Forum: https://forum.kea-dhcp4-server.org/
- Best Practices Guide: https://docs.kea-dhcp4-server.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
