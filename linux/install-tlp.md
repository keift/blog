---
description: Extend the life of your battery by limiting it.
icon: battery-three-quarters
---

## 1. Install TLP

TLP is a power management service.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y tlp tlp-rdw

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y tlp tlp-rdw
sudo yum install -y tlp tlp-rdw

# OpenSUSE (Zypper)
sudo zypper -n install tlp tlp-rdw

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm tlp tlp-rdw

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

This is how you can uninstall TLP.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt purge -y tlp tlp-rdw

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf remove -y tlp tlp-rdw
sudo yum remove -y tlp tlp-rdw

# OpenSUSE (Zypper)
sudo zypper -n remove -u tlp tlp-rdw

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm tlp tlp-rdw
```
