---
description: Encrypt your DNS queries with Systemd-Resolved.
icon: notebook
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "/^127\.0\.1\.1\s\+/s/\S\+$/$(hostname)/" /etc/hosts
```

## ALTERNATIVE: Cloudflare DNS (Recommended)

We are using Cloudflare DNS here.

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Cloudflare DNS in it
sudo tee /etc/systemd/resolved.conf > /dev/null << EOF
  [Resolve]
  DNS=1.1.1.1 1.0.0.1 2606:4700:4700::1111 2606:4700:4700::1001
  DNSOverTLS=yes
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## ALTERNATIVE: Google DNS

We are using Google DNS here.

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Google DNS in it
sudo tee /etc/systemd/resolved.conf > /dev/null << EOF
  [Resolve]
  DNS=8.8.8.8 8.8.4.4 2001:4860:4860::8888 2001:4860:4860::8844
  DNSOverTLS=yes
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## ALTERNATIVE: Yandex DNS

We are using Yandex DNS here.

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Yandex DNS in it
sudo tee /etc/systemd/resolved.conf > /dev/null << EOF
  [Resolve]
  DNS=77.88.8.8 77.88.8.1 2a02:6b8::feed:0ff 2a02:6b8:0:1::feed:0ff
  DNSOverTLS=yes
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## ALTERNATIVE: Quad9

We are using Quad9 here.

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Quad9 in it
sudo tee /etc/systemd/resolved.conf > /dev/null << EOF
  [Resolve]
  DNS=9.9.9.9 149.112.112.112 2620:fe::fe 2620:fe::9
  DNSOverTLS=yes
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## TIP: Remove DNS settings

If you want to remove the DNS settings, you can do the following.

```shell
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
