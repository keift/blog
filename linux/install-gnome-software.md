---
title: "Install Gnome Software"
description: "Use your Flatpak with interface."
icon: "💡"
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install Gnome Software

Gnome Software and Flatpak integration.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y flatpak gnome-software gnome-software-plugin-flatpak

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y flatpak gnome-software gnome-software-plugin-flatpak
sudo yum install -y flatpak gnome-software gnome-software-plugin-flatpak

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm flatpak gnome-software gnome-software-packagekit-plugin
```

## TIP: Uninstall Gnome Software

This is how you can uninstall Gnome Software.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y flatpak gnome-software gnome-software-plugin-flatpak
sudo apt autoremove -y

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y flatpak gnome-software gnome-software-plugin-flatpak
sudo dnf autoremove -y
sudo yum remove -y flatpak gnome-software gnome-software-plugin-flatpak
sudo yum autoremove -y

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm flatpak gnome-software gnome-software-packagekit-plugin
```
