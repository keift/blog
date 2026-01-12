---
description: Containerize your services.
icon: docker
---

## Install Docker

You can install it as follows.

```shell
# Pull and run the specified shell script
curl -fsSL https://get.docker.com | sh

# Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker
```

## TIP: Uninstall Docker

You can uninstall it as follows.

```shell
# Stop service
sudo systemctl stop docker

# Debian, Ubuntu, Linux Mint, Kali Linux, Pop!_OS (APT)
sudo apt purge -y docker* containerd.io

# RHEL, Fedora, CentOS, AlmaLinux, Rocky Linux (DNF)
sudo dnf remove -y docker* containerd.io

# Arch Linux, Manjaro, CachyOS, EndeavourOS, Artix Linux (Pacman)
sudo pacman -Rns --noconfirm docker* containerd.io

# openSUSE Tumbleweed, openSUSE Leap, SUSE Linux Enterprise, GeckoLinux, Regata OS (Zypper)
sudo zypper -n remove -u docker* containerd.io

# Remove unused files
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
sudo rm -rf /etc/apt/sources.list.d/docker.sources
sudo rm -rf /etc/apt/keyrings/docker.asc
```
