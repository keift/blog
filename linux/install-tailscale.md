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

## 3. Advertise exit node (Server)

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

Meanwhile, open the authorization link that appears and continue by selecting your own account or the network to which you were invited.

Finally, open the menu of the machine labeled **"Exit Node"** from [Tailscale dashboard](https://login.tailscale.com/admin/machines) and select the **"Use as exit node"** option in **"Edit route settings..."**.

## 4. Connect to the exit node (Client)

You can successfully use your VPN service by connecting to the exit node from different devices.

```shell
# Choose exit node
sudo tailscale set --exit-node=100.100.10.10
sudo tailscale up
```

You can find out the exit node address via the Tailscale dashboard or on the server with the following command.

```shell
# See Tailscale address
sudo tailscale ip
```

🎉 That's it! You now have your very own VPN!

## TIP: Uninstall Tailscale

This is how you can uninstall Tailscale.

```shell
# Uninstall Tailscale
sudo apt purge -y tailscale
sudo dnf remove -y tailscale
sudo yum remove -y tailscale
sudo pacman -Rns --noconfirm tailscale

# Remove configs
sudo rm -rf /etc/sysctl.d/99-tailscale.conf
sudo sysctl --system
NETDEV=$(ip -o route get 8.8.8.8 | cut -f 5 -d " ")
sudo ethtool -K $NETDEV rx-udp-gro-forwarding off rx-gro-list on
sudo rm -rf /etc/networkd-dispatcher/routable.d/50-tailscale
```

## TIP: Stop being an exit node (Server)

You can stop being an exit node as follows.

```shell
# Stop being an exit node
sudo tailscale set --advertise-exit-node=false
sudo tailscale up
```

## TIP: Stop using exit node (Client)

You can stop using the exit node as follows.

```shell
# Stop using the exit node
sudo tailscale set --exit-node=
sudo tailscale up
```