## 1. Keep Hosts content up to date

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install Stubby

Stubby is a DNS-over-TLS service.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y stubby

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y stubby
sudo yum install -y stubby

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm stubby
```

## 3. Complete the DNS setup

Set up and use Stubby. We are using Yandex DNS here.

```shell
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
    - address_data: 77.88.8.8
      tls_port: 853
      tls_auth_name: "common.dot.dns.yandex.net"
    - address_data: 77.88.8.1
      tls_port: 853
      tls_auth_name: "common.dot.dns.yandex.net"
    - address_data: 2a02:6b8::feed:0ff
      tls_port: 853
      tls_auth_name: "common.dot.dns.yandex.net"
    - address_data: 2a02:6b8:0:1::feed:0ff
      tls_port: 853
      tls_auth_name: "common.dot.dns.yandex.net"
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
  nameserver 77.88.8.8
  nameserver 77.88.8.1
  nameserver 2a02:6b8::feed:0ff
  nameserver 2a02:6b8:0:1::feed:0ff
EOF

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## TIP: Uninstall Stubby

This is how you can uninstall Stubby.

```shell
# Uninstall Stubby
sudo apt purge -y stubby
sudo dnf remove -y stubby
sudo yum remove -y stubby
sudo pacman -Rns --noconfirm stubby

# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Leave the Systemd-Resolved configuration blank
sudo tee /etc/systemd/resolved.conf > /dev/null <<< ""

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```
