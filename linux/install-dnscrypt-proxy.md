---
description: Encrypt your DNS queries with DNSCrypt Proxy.
icon: notebook
---

## DNSCrypt with Systemd-Resolved

Using DNSCrypt with Systemd-Resolved.

```shell
# Install Systemd-Resolved
sudo apt install -y systemd-resolved
sudo dnf install -y systemd-resolved
sudo pacman -S --noconfirm systemd-resolved
sudo zypper -n install systemd-resolved

# Install DNSCrypt Proxy
sudo apt install -y dnscrypt-proxy
sudo dnf install -y dnscrypt-proxy
sudo pacman -S --noconfirm dnscrypt-proxy
sudo zypper -n install dnscrypt-proxy

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Enable and start DNSCrypt Proxy
sudo systemctl enable dnscrypt-proxy
sudo systemctl start dnscrypt-proxy

# Configure DNSCrypt Proxy
sudo tee /etc/dnscrypt-proxy/dnscrypt-proxy.toml &>/dev/null << EOF
listen_addresses = ["127.0.0.1:5300", "[::1]:5300"]

[sources."public-resolvers"]
urls = ["https://raw.github.com/dnscrypt/dnscrypt-resolvers/master/v3/public-resolvers.md", "https://download.dnscrypt.info/resolvers-list/v3/public-resolvers.md"]
minisign_key = "RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3"
cache_file = "public-resolvers.md"
EOF

# Restart DNSCrypt Proxy for the changes to take effect
sudo systemctl restart dnscrypt-proxy

# Configure Systemd-Resolved
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=[::1]:5300

Domains=~.
DNSOverTLS=no
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo test -f /run/systemd/resolve/stub-resolv.conf && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## TIP: Uninstall DNSCrypt Proxy

You can uninstall it as follows.

```shell
# Uninstall DNSCrypt Proxy
sudo apt purge -y --autoremove dnscrypt-proxy
sudo dnf remove -y dnscrypt-proxy
sudo pacman -Rns --noconfirm dnscrypt-proxy
sudo zypper -n remove -u dnscrypt-proxy

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Leave the Systemd-Resolved configuration blank
sudo tee /etc/systemd/resolved.conf &>/dev/null <<< ""

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo test -f /run/systemd/resolve/stub-resolv.conf && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```
