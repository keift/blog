## 1. Keep Hosts content up to date

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install Tailscale

Install Tailscale via shell script.

```shell
# Pull and run the specified script
curl -fsSL https://tailscale.com/install.sh | sh
```

## 3. Advertise Exit Node

Select a host machine as the exit node. This represents the device you will use as the VPN.

```shell
# Enabling IP forwarding
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo "net.ipv6.conf.all.forwarding = 1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf

# Increase UDP compatibility
NETDEV=$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")
sudo ethtool -K $NETDEV rx-udp-gro-forwarding on rx-gro-list off
printf "#!/bin/sh\n\nethtool -K %s rx-udp-gro-forwarding on rx-gro-list off \n" "$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")" | sudo tee /etc/networkd-dispatcher/routable.d/50-tailscale
sudo chmod 755 /etc/networkd-dispatcher/routable.d/50-tailscale

# Advertise as an exit node
sudo tailscale up --advertise-exit-node=true
```

Meanwhile, open the authorization link that appears and continue by selecting your own account or the network to which you were invited.
