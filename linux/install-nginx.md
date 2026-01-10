---
description: Create reverse proxy services.
icon: n
---

## 1. Install Nginx

Nginx is a web server and reverse proxy service.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y nginx

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y nginx
sudo yum install -y nginx

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm nginx

# OpenSUSE (Zypper)
sudo zypper -n install nginx

# Enable and start Nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

## 2. Specify permissions on the UFW side

Allow Nginx to be accessed from outside in the firewall.

```shell
# Allow required ports in UFW
sudo ufw allow "Nginx Full"
```

## 3. Create a service

Here we perform routing by creating a reverse proxy service.

```shell
# 🟥 Environment variables
# The name of the service will be service-b. '-b' represents 'backend'. You can use '-f' for your frontend server. It doesn't really matter, we just use it to separate the two servers
SERVICE_NAME="service-b"
SERVER_NAME="api.example.com"
LISTEN_PORT="80"
UPSTREAM_PORT="3000"

# Configure service
sudo tee /etc/nginx/sites-available/$SERVICE_NAME.conf &>/dev/null << EOF
server {
  server_name $SERVER_NAME;
  listen $LISTEN_PORT;

  location / {
    proxy_pass http://127.0.0.1:$UPSTREAM_PORT;
    proxy_http_version 1.1;

    proxy_set_header Upgrade \$http_upgrade;
    proxy_set_header Connection "upgrade";

    proxy_set_header Host \$host;
    proxy_set_header X-Real-IP \$remote_addr;
    proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto \$scheme;
  }
}
EOF

# Move to enable all services
sudo rm -rf /etc/nginx/sites-enabled/*.conf
sudo ln -sf /etc/nginx/sites-available/*.conf /etc/nginx/sites-enabled

# Restart the Nginx for everything to work properly
sudo systemctl restart nginx
```

## TIP: Uninstall Nginx

This is how you can uninstall Nginx.

```shell
# Stop service
sudo systemctl stop nginx

# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y nginx

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y nginx
sudo yum remove -y nginx

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm nginx

# OpenSUSE (Zypper)
sudo zypper -n remove -u nginx

# Remove unused files
sudo rm -rf /etc/nginx
sudo rm -rf /var/log/nginx
sudo rm -rf /var/www/html
sudo rm -rf /var/www/*
sudo rm -rf /usr/lib/nginx
sudo rm -rf /usr/share/nginx
sudo rm -rf /var/cache/nginx
sudo rm -rf /run/nginx
sudo rm -rf /usr/sbin/nginx
sudo rm -rf /usr/local/nginx
sudo rm -rf /usr/local/etc/nginx
sudo rm -rf /usr/local/var/log/nginx
```
