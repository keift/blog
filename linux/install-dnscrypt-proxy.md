---
description: Encrypt your DNS queries with DNSCrypt Proxy.
icon: notebook
---

## ALTERNATIVE: Cloudflare DNS (Recommended)

Set up and use DNSCrypt Proxy. We are using Cloudflare DNS here.

```shell
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
listen_addresses = ['127.0.0.1:5300']

server_names = ['cloudflare', 'cloudflare-ipv6']

[sources]
  [sources.'public-resolvers']
  url = 'https://raw.github.com/DNSCrypt/dnscrypt-resolvers/master/v3/public-resolvers.md'
  minisign_key = 'RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3'
  cache_file = '/var/cache/dnscrypt-proxy/public-resolvers-v3.md'
EOF

# Restart the DNSCrypt Proxy for everything to work properly
sudo systemctl restart dnscrypt-proxy

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use DNSCrypt Proxy in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=1.1.1.1#one.one.one.one
DNS=2606:4700:4700::1111#one.one.one.one
DNS=1.0.0.1#one.one.one.one
DNS=2606:4700:4700::1001#one.one.one.one
DNSOverTLS=yes
Domains=~.
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use DNSCrypt Proxy in it
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

Set up and use DNSCrypt Proxy. We are using Mullvad DNS here.

```shell
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
listen_addresses = ['127.0.0.1:5300']

server_names = ['mullvad-base-doh', 'mullvad-doh']

[sources]
  [sources.'public-resolvers']
  url = 'https://raw.github.com/DNSCrypt/dnscrypt-resolvers/master/v3/public-resolvers.md'
  minisign_key = 'RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3'
  cache_file = '/var/cache/dnscrypt-proxy/public-resolvers-v3.md'
EOF

# Restart the DNSCrypt Proxy for everything to work properly
sudo systemctl restart dnscrypt-proxy

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use DNSCrypt Proxy in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=194.242.2.4#base.dns.mullvad.net
DNS=2a07:e340::4#base.dns.mullvad.net
DNS=194.242.2.2#dns.mullvad.net
DNS=2a07:e340::2#dns.mullvad.net
DNSOverTLS=yes
Domains=~.
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use DNSCrypt Proxy in it
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

Set up and use DNSCrypt Proxy. We are using Google DNS here.

```shell
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
listen_addresses = ['127.0.0.1:5300']

server_names = ['google', 'google-ipv6']

[sources]
  [sources.'public-resolvers']
  url = 'https://raw.github.com/DNSCrypt/dnscrypt-resolvers/master/v3/public-resolvers.md'
  minisign_key = 'RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3'
  cache_file = '/var/cache/dnscrypt-proxy/public-resolvers-v3.md'
EOF

# Restart the DNSCrypt Proxy for everything to work properly
sudo systemctl restart dnscrypt-proxy

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use DNSCrypt Proxy in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=8.8.8.8#dns.google
DNS=2001:4860:4860::8888#dns.google
DNS=8.8.4.4#dns.google
DNS=2001:4860:4860::8844#dns.google
DNSOverTLS=yes
Domains=~.
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use DNSCrypt Proxy in it
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

Set up and use DNSCrypt Proxy. We are using Yandex DNS here.

```shell
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
listen_addresses = ['127.0.0.1:5300']

server_names = ['yandex', 'yandex-ipv6']

[sources]
  [sources.'public-resolvers']
  url = 'https://raw.github.com/DNSCrypt/dnscrypt-resolvers/master/v3/public-resolvers.md'
  minisign_key = 'RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3'
  cache_file = '/var/cache/dnscrypt-proxy/public-resolvers-v3.md'
EOF

# Restart the DNSCrypt Proxy for everything to work properly
sudo systemctl restart dnscrypt-proxy

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use DNSCrypt Proxy in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=127.0.0.1:5300
DNS=77.88.8.8#common.dot.dns.yandex.net
DNS=2a02:6b8::feed:0ff#common.dot.dns.yandex.net
DNS=77.88.8.1#common.dot.dns.yandex.net
DNS=2a02:6b8:0:1::feed:0ff#common.dot.dns.yandex.net
DNSOverTLS=yes
Domains=~.
EOF

# Rewrite the /etc/resolv.conf file and specify that we will use DNSCrypt Proxy in it
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

## TIP: Uninstall DNSCrypt Proxy

You can uninstall it as follows.

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
