---
description: Use your own DNS settings with NextDNS.
icon: shield
---

## ALTERNATIVE: NextDNS with Systemd-Resolved (Recommended)

Using NextDNS with Systemd-Resolved.

```shell
# 🟥 Environment variables
NEXTDNS_ID="aaaaaa"
DEVICE_NAME="HUAWEI MateBook D16"

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
DNS=45.90.28.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c0::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=45.90.30.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c1::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
Domains=~.
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## ALTERNATIVE: NextDNS with DNSCrypt Proxy

Using NextDNS with DNSCrypt Proxy.

```shell
# 🟥 Environment variables
NEXTDNS_ID="aaaaaa"
NEXTDNS_STAMP="sdns://AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA"
DEVICE_NAME="HUAWEI MateBook D16"

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

# Configure Systemd-Resolved
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=[::1]:5300
DNS=45.90.28.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c0::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=45.90.30.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c1::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
Domains=~.
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved

# Configure DNSCrypt Proxy
sudo tee /etc/dnscrypt-proxy/dnscrypt-proxy.toml &>/dev/null << EOF
listen_addresses = ["127.0.0.1:5300", "[::1]:5300"]

server_names = ["NextDNS-${NEXTDNS_ID}"]

[static]
  [static."NextDNS-${NEXTDNS_ID}"]
  stamp = "${NEXTDNS_STAMP}"
EOF

# Restart DNSCrypt Proxy for the changes to take effect
sudo systemctl restart dnscrypt-proxy
```

## TIP: Remove DNS settings

You can remove it as follows.

```shell
# Uninstall DNSCrypt Proxy
sudo apt purge -y dnscrypt-proxy
sudo dnf remove -y dnscrypt-proxy
sudo pacman -Rns --noconfirm dnscrypt-proxy
sudo zypper -n remove -u dnscrypt-proxy

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
