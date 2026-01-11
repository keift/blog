---
description: Encrypt your DNS queries with Stubby.
icon: notebook
---

## Install Stubby

You can install it as follows.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y stubby

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y stubby
sudo yum install -y stubby

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm stubby

# OpenSUSE (Zypper)
sudo zypper -n install stubby

# Enable and start Stubby
sudo systemctl enable stubby
sudo systemctl start stubby
```

## ALTERNATIVE: Cloudflare DNS (Recommended)

Set up and use Stubby. We are using Cloudflare DNS here.

```shell
# Install Stubby
sudo apt install -y stubby
sudo dnf install -y stubby
sudo yum install -y stubby
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
  - address_data: 1.1.1.1
    tls_auth_name: "one.one.one.one"
  - address_data: 2606:4700:4700::1111
    tls_auth_name: "one.one.one.one"
  - address_data: 1.0.0.1
    tls_auth_name: "one.one.one.one"
  - address_data: 2606:4700:4700::1001
    tls_auth_name: "one.one.one.one"
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

## ALTERNATIVE: Mullvad DNS

Set up and use Stubby. We are using Mullvad DNS here.

```shell
# Install Stubby
sudo apt install -y stubby
sudo dnf install -y stubby
sudo yum install -y stubby
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
  - address_data: 194.242.2.4
    tls_auth_name: "base.dns.mullvad.net"
  - address_data: 2a07:e340::4
    tls_auth_name: "base.dns.mullvad.net"
  - address_data: 194.242.2.2
    tls_auth_name: "dns.mullvad.net"
  - address_data: 2a07:e340::2
    tls_auth_name: "dns.mullvad.net"
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

## ALTERNATIVE: Google DNS

Set up and use Stubby. We are using Google DNS here.

```shell
# Install Stubby
sudo apt install -y stubby
sudo dnf install -y stubby
sudo yum install -y stubby
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
  - address_data: 8.8.8.8
    tls_auth_name: "dns.google"
  - address_data: 2001:4860:4860::8888
    tls_auth_name: "dns.google"
  - address_data: 8.8.4.4
    tls_auth_name: "dns.google"
  - address_data: 2001:4860:4860::8844
    tls_auth_name: "dns.google"
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
nameserver 8.8.8.8
nameserver 2001:4860:4860::8888
nameserver 8.8.4.4
nameserver 2001:4860:4860::8844
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## ALTERNATIVE: Yandex DNS

Set up and use Stubby. We are using Yandex DNS here.

```shell
# Install Stubby
sudo apt install -y stubby
sudo dnf install -y stubby
sudo yum install -y stubby
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
  - address_data: 77.88.8.8
    tls_auth_name: "common.dot.dns.yandex.net"
  - address_data: 2a02:6b8::feed:0ff
    tls_auth_name: "common.dot.dns.yandex.net"
  - address_data: 77.88.8.1
    tls_auth_name: "common.dot.dns.yandex.net"
  - address_data: 2a02:6b8:0:1::feed:0ff
    tls_auth_name: "common.dot.dns.yandex.net"
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
nameserver 77.88.8.8
nameserver 2a02:6b8::feed:0ff
nameserver 77.88.8.1
nameserver 2a02:6b8:0:1::feed:0ff
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
[ -e /run/systemd/resolve/stub-resolv.conf ] && sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## TIP: Uninstall Stubby

You can uninstall it as follows.

```shell
# Uninstall Stubby
sudo apt purge -y stubby
sudo dnf remove -y stubby
sudo yum remove -y stubby
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
