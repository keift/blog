---
description: Extend the life of your battery by limiting it.
icon: battery-three-quarters
---

## 1. Install TLP

You can install it as follows.

```shell
# Debian, Ubuntu, Linux Mint, Kali Linux, Pop!_OS (APT)
sudo apt install -y tlp tlp-rdw

# RHEL, Fedora, CentOS, AlmaLinux, Rocky Linux (DNF)
sudo dnf install -y tlp tlp-rdw

# Arch Linux, Manjaro, CachyOS, EndeavourOS, Artix Linux (Pacman)
sudo pacman -S --noconfirm tlp tlp-rdw

# openSUSE Tumbleweed, openSUSE Leap, SUSE Linux Enterprise, GeckoLinux, Regata OS (Zypper)
sudo zypper -n install tlp tlp-rdw

# Start TLP
sudo tlp start
```

## 2. Complete the TLP setup

Set up and use TLP.

```shell
# Enable battery limiting in TLP config
sudo sed -i "/START_CHARGE_THRESH_BAT0/s/^#//" /etc/tlp.conf
sudo sed -i "/STOP_CHARGE_THRESH_BAT0/s/^#//" /etc/tlp.conf

# Restart TLP for the changes to take effect
sudo systemctl restart tlp
```

## TIP: Uninstall TLP

You can uninstall it as follows.

```shell
# Debian, Ubuntu, Linux Mint, Kali Linux, Pop!_OS (APT)
sudo apt purge -y tlp tlp-rdw

# RHEL, Fedora, CentOS, AlmaLinux, Rocky Linux (DNF)
sudo dnf remove -y tlp tlp-rdw

# Arch Linux, Manjaro, CachyOS, EndeavourOS, Artix Linux (Pacman)
sudo pacman -Rns --noconfirm tlp tlp-rdw

# openSUSE Tumbleweed, openSUSE Leap, SUSE Linux Enterprise, GeckoLinux, Regata OS (Zypper)
sudo zypper -n remove -u tlp tlp-rdw
```
