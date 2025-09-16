# dnsmasq Installation Guide

dnsmasq is a free and open-source lightweight DNS/DHCP/TFTP server. dnsmasq provides integrated DNS, DHCP, and TFTP services in a small footprint, perfect for small networks

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
  - RAM: 64MB minimum
  - Storage: 10MB for installation
  - Network: DNS/DHCP ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 53 (default dnsmasq port)
  - Port 67 for DHCP
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

# Install dnsmasq
sudo dnf install -y dnsmasq

# Enable and start service
sudo systemctl enable --now dnsmasq

# Configure firewall
sudo firewall-cmd --permanent --add-port=53/tcp
sudo firewall-cmd --reload

# Verify installation
dnsmasq --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install dnsmasq
sudo apt install -y dnsmasq

# Enable and start service
sudo systemctl enable --now dnsmasq

# Configure firewall
sudo ufw allow 53

# Verify installation
dnsmasq --version
```

### Arch Linux

```bash
# Install dnsmasq
sudo pacman -S dnsmasq

# Enable and start service
sudo systemctl enable --now dnsmasq

# Verify installation
dnsmasq --version
```

### Alpine Linux

```bash
# Install dnsmasq
apk add --no-cache dnsmasq

# Enable and start service
rc-update add dnsmasq default
rc-service dnsmasq start

# Verify installation
dnsmasq --version
```

### openSUSE/SLES

```bash
# Install dnsmasq
sudo zypper install -y dnsmasq

# Enable and start service
sudo systemctl enable --now dnsmasq

# Configure firewall
sudo firewall-cmd --permanent --add-port=53/tcp
sudo firewall-cmd --reload

# Verify installation
dnsmasq --version
```

### macOS

```bash
# Using Homebrew
brew install dnsmasq

# Start service
brew services start dnsmasq

# Verify installation
dnsmasq --version
```

### FreeBSD

```bash
# Using pkg
pkg install dnsmasq

# Enable in rc.conf
echo 'dnsmasq_enable="YES"' >> /etc/rc.conf

# Start service
service dnsmasq start

# Verify installation
dnsmasq --version
```

### Windows

```bash
# Using Chocolatey
choco install dnsmasq

# Or using Scoop
scoop install dnsmasq

# Verify installation
dnsmasq --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/dnsmasq

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
dnsmasq --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable dnsmasq

# Start service
sudo systemctl start dnsmasq

# Stop service
sudo systemctl stop dnsmasq

# Restart service
sudo systemctl restart dnsmasq

# Check status
sudo systemctl status dnsmasq

# View logs
sudo journalctl -u dnsmasq -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add dnsmasq default

# Start service
rc-service dnsmasq start

# Stop service
rc-service dnsmasq stop

# Restart service
rc-service dnsmasq restart

# Check status
rc-service dnsmasq status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'dnsmasq_enable="YES"' >> /etc/rc.conf

# Start service
service dnsmasq start

# Stop service
service dnsmasq stop

# Restart service
service dnsmasq restart

# Check status
service dnsmasq status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start dnsmasq
brew services stop dnsmasq
brew services restart dnsmasq

# Check status
brew services list | grep dnsmasq
```

### Windows Service Manager

```powershell
# Start service
net start dnsmasq

# Stop service
net stop dnsmasq

# Using PowerShell
Start-Service dnsmasq
Stop-Service dnsmasq
Restart-Service dnsmasq

# Check status
Get-Service dnsmasq
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream dnsmasq_backend {
    server 127.0.0.1:53;
}

server {
    listen 80;
    server_name dnsmasq.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name dnsmasq.example.com;

    ssl_certificate /etc/ssl/certs/dnsmasq.example.com.crt;
    ssl_certificate_key /etc/ssl/private/dnsmasq.example.com.key;

    location / {
        proxy_pass http://dnsmasq_backend;
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
    ServerName dnsmasq.example.com
    Redirect permanent / https://dnsmasq.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName dnsmasq.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/dnsmasq.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/dnsmasq.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:53/
    ProxyPassReverse / http://127.0.0.1:53/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend dnsmasq_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/dnsmasq.pem
    redirect scheme https if !{ ssl_fc }
    default_backend dnsmasq_backend

backend dnsmasq_backend
    balance roundrobin
    server dnsmasq1 127.0.0.1:53 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R dnsmasq:dnsmasq /etc/dnsmasq
sudo chmod 750 /etc/dnsmasq

# Configure firewall
sudo firewall-cmd --permanent --add-port=53/tcp
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
sudo systemctl status dnsmasq

# View logs
sudo journalctl -u dnsmasq -f

# Monitor resource usage
top -p $(pgrep dnsmasq)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/dnsmasq"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/dnsmasq-backup-$DATE.tar.gz" /etc/dnsmasq /var/lib/dnsmasq

echo "Backup completed: $BACKUP_DIR/dnsmasq-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop dnsmasq

# Restore from backup
tar -xzf /backup/dnsmasq/dnsmasq-backup-*.tar.gz -C /

# Start service
sudo systemctl start dnsmasq
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u dnsmasq -n 100
sudo tail -f /var/log/dnsmasq/dnsmasq.log

# Check configuration
dnsmasq --version

# Check permissions
ls -la /etc/dnsmasq
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 53

# Test connectivity
telnet localhost 53

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep dnsmasq)

# Check disk I/O
iotop -p $(pgrep dnsmasq)

# Check connections
ss -an | grep 53
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  dnsmasq:
    image: dnsmasq:latest
    ports:
      - "53:53"
    volumes:
      - ./config:/etc/dnsmasq
      - ./data:/var/lib/dnsmasq
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update dnsmasq

# Debian/Ubuntu
sudo apt update && sudo apt upgrade dnsmasq

# Arch Linux
sudo pacman -Syu dnsmasq

# Alpine Linux
apk update && apk upgrade dnsmasq

# openSUSE
sudo zypper update dnsmasq

# FreeBSD
pkg update && pkg upgrade dnsmasq

# Always backup before updates
tar -czf /backup/dnsmasq-pre-update-$(date +%Y%m%d).tar.gz /etc/dnsmasq

# Restart after updates
sudo systemctl restart dnsmasq
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/dnsmasq

# Clean old logs
find /var/log/dnsmasq -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/dnsmasq
```

## Additional Resources

- Official Documentation: https://docs.dnsmasq.org/
- GitHub Repository: https://github.com/dnsmasq/dnsmasq
- Community Forum: https://forum.dnsmasq.org/
- Best Practices Guide: https://docs.dnsmasq.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
