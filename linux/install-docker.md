---
description: Containerize your services.
icon: docker
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "/^127\.0\.1\.1\s\+/s/\S\+$/$(hostname)/" /etc/hosts
```

## 2. Install Docker

Docker is a containerization service.

```shell
# Pull and run the specified shell script
curl -fsSL https://get.docker.com | sh

# Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker
```

## TIP: Uninstall Docker

This is how you can uninstall Docker.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y docker* containerd.io

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y docker* containerd.io
sudo yum remove -y docker* containerd.io

# OpenSUSE (Zypper)
sudo zypper -n remove -u docker* containerd.io

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm docker* containerd.io
```
