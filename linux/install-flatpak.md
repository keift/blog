---
description: Use your Flatpak with Gnome Software.
icon: basket-shopping
---

## Install Flatpak and Gnome Software

You can install it as follows.

```shell
# Debian, Ubuntu, Linux Mint, Kali Linux, Pop!_OS (APT)
sudo apt install -y flatpak gnome-software gnome-software-plugin-flatpak

# RHEL, Fedora, CentOS, AlmaLinux, Rocky Linux (DNF)
sudo dnf install -y flatpak gnome-software gnome-software-plugin-flatpak

# Arch Linux, Manjaro, CachyOS, EndeavourOS, Artix Linux (Pacman)
sudo pacman -S --noconfirm flatpak gnome-software gnome-software-plugin-flatpak

# openSUSE Tumbleweed, openSUSE Leap, SUSE Linux Enterprise, GeckoLinux, Regata OS (Zypper)
sudo zypper -n install flatpak gnome-software gnome-software-plugin-flatpak

# Define Flathub remote reference
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## TIP: Uninstall Flatpak and Gnome Software

You can uninstall it as follows.

```shell
# Debian, Ubuntu, Linux Mint, Kali Linux, Pop!_OS (APT)
sudo apt purge -y flatpak gnome-software gnome-software-plugin-flatpak

# RHEL, Fedora, CentOS, AlmaLinux, Rocky Linux (DNF)
sudo dnf remove -y flatpak gnome-software gnome-software-plugin-flatpak

# Arch Linux, Manjaro, CachyOS, EndeavourOS, Artix Linux (Pacman)
sudo pacman -Rns --noconfirm flatpak gnome-software gnome-software-packagekit-plugin

# openSUSE Tumbleweed, openSUSE Leap, SUSE Linux Enterprise, GeckoLinux, Regata OS (Zypper)
sudo zypper -n remove -u flatpak gnome-software gnome-software-plugin-flatpak
```
