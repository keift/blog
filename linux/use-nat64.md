---
description: Fix access problems only on providers that use IPv6.
icon: '6'
---

## NAT64 DNS with Systemd-Resolved

Using NAT64 with Systemd-Resolved.

```shell
# Install Systemd-Resolved
sudo apt install -y systemd-resolved
sudo dnf install -y systemd-resolved
sudo pacman -S --noconfirm systemd-resolved
sudo zypper -n install systemd-resolved

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Configure Systemd-Resolved
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=2606:4700:4700::64
DNS=2606:4700:4700::6400

Domains=~.
DNSOverTLS=no
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## TIP: Remove DNS settings

You can remove it as follows.

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Leave the Systemd-Resolved configuration blank
sudo tee /etc/systemd/resolved.conf &>/dev/null <<< ""

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```
