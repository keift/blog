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

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use NextDNS in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=45.90.28.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c0::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=45.90.30.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c1::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNSOverTLS=yes
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

server_names = ["NextDNS-${NEXTDNS_ID}"]

[static]
  [static."NextDNS-${NEXTDNS_ID}"]
  stamp = "${NEXTDNS_STAMP}"
EOF

# Restart the DNSCrypt Proxy for everything to work properly
sudo systemctl restart dnscrypt-proxy

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use DNSCrypt Proxy in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=[::1]:5300
DNS=45.90.28.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c0::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=45.90.30.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
DNS=2a07:a8c1::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use DNSCrypt Proxy in it
sudo tee /etc/resolv.conf &>/dev/null << EOF
nameserver 127.0.0.1
nameserver 45.90.28.0
nameserver 2a07:a8c0::
nameserver 45.90.30.0
nameserver 2a07:a8c1::
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
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

# Leave the /etc/resolv.conf file safe
sudo tee /etc/resolv.conf &>/dev/null << EOF
nameserver 1.1.1.1
nameserver 2606:4700:4700::1111
nameserver 1.0.0.1
nameserver 2606:4700:4700::1001
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```
