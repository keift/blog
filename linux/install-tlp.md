## 1. Keep Hosts content up to date

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install TLP

TLP is a power management service.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y tlp tlp-rdw

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y tlp tlp-rdw
sudo yum install -y tlp tlp-rdw

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm tlp tlp-rdw
```

## 3. Continue with the installation

Set up and use TLP.

```shell
# Start TLP
sudo tlp start

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

# Arch, Manjaro (Pacman)
sudo pacman -Rns --noconfirm tlp tlp-rdw
```
