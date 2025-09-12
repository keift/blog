---
title: Use NextDNS
description: Use your own DNS settings with NextDNS.
icon: shield
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## ALTERNATIVE: NextDNS with Systemd-Resolved (Recommended)

Using NextDNS in Systemd distributions.

```shell
# 🟥 Environment variables
NEXTDNS_ID="aaaaaa"
DEVICE_NAME="HUAWEI MateBook D16"

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use NextDNS in it
sudo tee /etc/systemd/resolved.conf > /dev/null << EOF
  [Resolve]
  DNS=45.90.28.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io 45.90.30.0#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io 2a07:a8c0::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io 2a07:a8c1::#${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io
  DNSOverTLS=yes
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

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
sudo yum install -y stubby
sudo pacman -S --noconfirm stubby

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Enable and start Stubby
sudo systemctl enable stubby
sudo systemctl start stubby

# Configure Stubby
sudo tee /etc/stubby/stubby.yml > /dev/null << EOF
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
    - address_data: 45.90.30.0
      tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
    - address_data: 2a07:a8c0::0
      tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
    - address_data: 2a07:a8c1::0
      tls_auth_name: "${DEVICE_NAME// /--}-${NEXTDNS_ID}.dns.nextdns.io"
EOF

# Restart the Stubby for everything to work properly
sudo systemctl restart stubby

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Stubby in it
sudo tee /etc/systemd/resolved.conf > /dev/null << EOF
  [Resolve]
  DNS=127.0.0.1
  DNSStubListener=no
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use Stubby in it
sudo tee /etc/resolv.conf > /dev/null << EOF
  nameserver 127.0.0.1
  nameserver 45.90.28.0
  nameserver 45.90.30.0
  nameserver 2a07:a8c0::
  nameserver 2a07:a8c1::
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## TIP: Remove DNS settings

If you want to remove the DNS settings, you can do the following.

```shell
# Uninstall Stubby
sudo apt purge -y stubby
sudo apt autoremove -y
sudo dnf remove -y stubby
sudo dnf autoremove -y
sudo yum remove -y stubby
sudo yum autoremove -y
sudo pacman -Rns --noconfirm stubby

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Leave the Systemd-Resolved configuration blank
sudo tee /etc/systemd/resolved.conf > /dev/null <<< ""

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```
