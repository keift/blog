---
description: Containerize your services.
icon: docker
---

## Install Docker

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
# Stop service
sudo systemctl stop docker

# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y docker* containerd.io

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y docker* containerd.io
sudo yum remove -y docker* containerd.io

# OpenSUSE (Zypper)
sudo zypper -n remove -u docker* containerd.io

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm docker* containerd.io

# Remove unused files
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
sudo rm -rf /etc/apt/sources.list.d/docker.sources
sudo rm -rf /etc/apt/keyrings/docker.asc
```
