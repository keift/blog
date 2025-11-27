---
description: Set up and use your own VPN server.
icon: braille
---

## 1. Install required tools

Required tools for installation.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y curl

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y curl
sudo yum install -y curl

# OpenSUSE (Zypper)
sudo zypper -n install curl

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm curl
```

## 2. Install Tailscale

Install Tailscale with shell script.

```shell
# Pull and run the specified shell script
curl -fsSL https://tailscale.com/install.sh | sh

# Enable and start Tailscale
sudo systemctl enable tailscale
sudo systemctl start tailscale
```

## 3. Login to the Tailscale

You must be connected to the Tailscale network on both your server and your clients.

```shell
# Login to the Tailscale
sudo tailscale login
```

## 4. Advertise exit node

Select a host machine as the exit node. This represents the device you will use as the VPN.

```shell
# Enable IP forwarding
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo "net.ipv6.conf.all.forwarding = 1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf

# Increase UDP compatibility
NETDEV=$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")
sudo ethtool -K $NETDEV rx-udp-gro-forwarding on rx-gro-list off
printf "#!/bin/sh\n\nethtool -K %s rx-udp-gro-forwarding on rx-gro-list off \n" "$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")" | sudo tee /etc/networkd-dispatcher/routable.d/50-tailscale
sudo chmod 755 /etc/networkd-dispatcher/routable.d/50-tailscale

# Advertise as an exit node
sudo tailscale set --advertise-exit-node=true
sudo tailscale up
```

Finally, open the menu of the machine labeled **"Exit Node"** from [Tailscale dashboard](https://login.tailscale.com/admin/machines) and select the **"Use as exit node"** option in **"Edit route settings..."**.

## 5. Connect to the exit node

You can successfully use your VPN service by connecting to the exit node from different devices.

```shell
# Choose exit node
sudo tailscale set --exit-node=100.100.10.10
sudo tailscale up
```

You can find the exit node address from the [Tailscale dashboard](https://login.tailscale.com/admin/machines) or on the server with the following command.

```shell
# See Tailscale address
sudo tailscale ip
```

## TIP: Stop being an exit node

You can stop being an exit node as follows.

```shell
# Stop being an exit node
sudo tailscale set --advertise-exit-node=false
sudo tailscale up
```

## TIP: Stop using exit node

You can stop using the exit node as follows.

```shell
# Stop using the exit node
sudo tailscale set --exit-node=
sudo tailscale up
```

## TIP: Uninstall Tailscale

This is how you can uninstall Tailscale.

```shell
# Uninstall Tailscale
sudo apt purge -y tailscale
sudo dnf remove -y tailscale
sudo yum remove -y tailscale
sudo zypper -n remove -u tailscale
sudo pacman -Rns --noconfirm tailscale

# Remove configs
sudo rm -rf /etc/sysctl.d/99-tailscale.conf
sudo sysctl --system
NETDEV=$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")
sudo ethtool -K $NETDEV rx-udp-gro-forwarding off rx-gro-list on
sudo rm -rf /etc/networkd-dispatcher/routable.d/50-tailscale
```
