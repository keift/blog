---
title: Install Docker
description: Containerize your services.
icon: docker
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install Docker

Docker is a containerization service.

```shell
# Pull and run the specified shell script
curl -fsSL https://get.docker.com | sh
```

## TIP: Uninstall Docker

This is how you can uninstall Docker.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y docker* containerd.io
sudo apt autoremove -y

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y docker* containerd.io
sudo dnf autoremove -y
sudo yum remove -y docker* containerd.io
sudo yum autoremove -y

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm docker* containerd.io
```
