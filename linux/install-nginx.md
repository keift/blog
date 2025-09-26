---
description: Create reverse proxy services.
icon: n
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "/^127\.0\.1\.1\s\+/s/\S\+$/$(hostname)/" /etc/hosts
```

## 2. Install Nginx

Nginx is a web server and reverse proxy service.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y nginx

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y nginx
sudo yum install -y nginx

# OpenSUSE (Zypper)
sudo zypper -n install nginx

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm nginx

# Enable and start Nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

## 3. Specify permissions on the UFW side

Allow Nginx to be accessed from outside in the firewall.

```shell
# Allow required ports in UFW
sudo ufw allow "Nginx Full"
```

## 4. Create a service

Here we perform routing by creating a reverse proxy service.

```shell
# 🟥 Environment variables
# The name of the service will be service-b. '-b' represents 'backend'. You can use '-f' for your frontend server. It doesn't really matter, we just use it to separate the two servers
SERVICE_NAME="service-b"
SERVER_NAME="api.example.com"
LISTEN_PORT="80"
UPSTREAM_PORT="3000"

# Configure service
sudo tee /etc/nginx/sites-available/$SERVICE_NAME.conf > /dev/null << EOF
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
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y nginx

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y nginx
sudo yum remove -y nginx

# OpenSUSE (Zypper)
sudo zypper -n remove -u nginx

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm nginx
```
