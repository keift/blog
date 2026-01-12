---
description: Use your own DNS settings with NextDNS.
icon: shield
---

## ALTERNATIVE: NextDNS with Systemd-Resolved (Recommended)

Using NextDNS in Systemd distributions.

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

## ALTERNATIVE: NextDNS with Stubby

Using NextDNS in non-Systemd distributions.

```shell
# 🟥 Environment variables
NEXTDNS_ID="aaaaaa"
DEVICE_NAME="HUAWEI MateBook D16"

# Install Stubby
sudo apt install -y stubby
sudo dnf install -y stubby
sudo pacman -S --noconfirm stubby
sudo zypper -n install stubby

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Enable and start Stubby
sudo systemctl enable stubby
sudo systemctl start stubby

# Configure Stubby
sudo tee /etc/stubby/stubby.yml &>/dev/null << EOF
resolution_type: GETDNS_RESOLUTION_STUB
tls_authentication: GETDNS_AUTHENTICATION_REQUIRED
round_robin_upstreams: 1
idle_timeout: 10000

dns_transport_list:
  - GETDNS_TRANSPORT_TLS

listen_addresses:
  - 127.0.0.1@53

upstream_recursive_servers:
  - address_data: 45.90.28.0
    tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
  - address_data: 2a07:a8c0::
    tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
  - address_data: 45.90.30.0
    tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
  - address_data: 2a07:a8c1::
    tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
EOF

# Restart the Stubby for everything to work properly
sudo systemctl restart stubby

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Stubby in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1
DNSStubListener=no
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use Stubby in it
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
# Uninstall Stubby
sudo apt purge -y stubby
sudo dnf remove -y stubby
sudo pacman -Rns --noconfirm stubby
sudo zypper -n remove -u stubby

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
