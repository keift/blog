---
description: Use your Flatpak with Gnome Software.
icon: basket-shopping
---

## Install Flatpak and Gnome Software

Flatpak and Gnome Software integration.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y flatpak gnome-software gnome-software-plugin-flatpak

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y flatpak gnome-software gnome-software-plugin-flatpak
sudo yum install -y flatpak gnome-software gnome-software-plugin-flatpak

# OpenSUSE (Zypper)
sudo zypper -n install flatpak gnome-software gnome-software-plugin-flatpak

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm flatpak gnome-software gnome-software-packagekit-plugin

# Define Flathub remote reference
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## TIP: Uninstall Flatpak and Gnome Software

This is how you can uninstall Flatpak and Gnome Software.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y flatpak gnome-software gnome-software-plugin-flatpak

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y flatpak gnome-software gnome-software-plugin-flatpak
sudo yum remove -y flatpak gnome-software gnome-software-plugin-flatpak

# OpenSUSE (Zypper)
sudo zypper -n remove -u flatpak gnome-software gnome-software-plugin-flatpak

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm flatpak gnome-software gnome-software-packagekit-plugin
```
