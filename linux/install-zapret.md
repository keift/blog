---
description: Install Zapret to bypass DPI barriers.
icon: lock-keyhole-open
---

## 1. Install required tools

Required tools for installation.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y curl dnsutils unzip nftables

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y curl bind-utils unzip nftables
sudo yum install -y curl bind-utils unzip nftables

# OpenSUSE (Zypper)
sudo zypper -n install curl bind-utils unzip nftables

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm curl bind-tools unzip nftables
```

## 2. Change DNS rules

Zapret only bypasses DPI restrictions. But it does not set up a DNS for us. We need to do that ourselves.

We've used Yandex DNS here with Russian users in mind. However, other provider alternatives are also available if you prefer.

- [Mullvad DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-mullvad-dns-recommended) (Recommended)
- [Cloudflare DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-cloudflare-dns)
- [Google DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-google-dns)
- [Yandex DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-yandex-dns)

If your distribution does not include Systemd, you will need to do this using [Stubby](https://keift.gitbook.io/blog/linux/install-stubby).

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Rewrite the /etc/systemd/resolved.conf file and specify that we will use Yandex DNS in it
sudo tee /etc/systemd/resolved.conf &>/dev/null << EOF
[Resolve]
DNS=77.88.8.8#common.dot.dns.yandex.net
DNS=2a02:6b8::feed:0ff#common.dot.dns.yandex.net
DNS=77.88.8.1#common.dot.dns.yandex.net
DNS=2a02:6b8:0:1::feed:0ff#common.dot.dns.yandex.net
DNSOverTLS=yes
EOF

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```

## 3. Download Zapret

Download the compiled zip file as release on GitHub.

```shell
# Delete if present
sudo rm -rf ~/zapret-v72.2.zip
sudo rm -rf ~/zapret-v72.2

# Go to the home directory
cd ~/

# Download the compiled zip file from GitHub
sudo wget https://github.com/bol-van/zapret/releases/download/v72.2/zapret-v72.2.zip
```

## 4. Unzip the zip file

Extract the zip file and then delete it.

```shell
# Unzip the zip file
sudo unzip ~/zapret-v72.2.zip

# Delete the zip file that we no longer need
sudo rm -rf ~/zapret-v72.2.zip
```

## 5. Prepare for installation

Install the requirements and prepare to perform a clean install.

```shell
# For a clean installation, remove any installation files that may be present in case an installation has been made before
sudo ~/zapret-v72.2/uninstall_easy.sh
sudo /opt/zapret/uninstall_easy.sh
sudo rm -rf /opt/zapret

# Install requirements
sudo ~/zapret-v72.2/install_prereq.sh
sudo ~/zapret-v72.2/install_bin.sh
```

Here are the answers you need to give to the questions you may encounter during this time.

```
select firewall type :
1 : iptables
2 : nftables
your choice (default : nftables) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

## 6. Do Blockcheck

Find the DPI methods implemented by the ISP.

```shell
# Run the test
sudo ~/zapret-v72.2/blockcheck.sh
```

Here are the answers you need to give to the questions you may encounter during this time.

```
specify domain(s) to test. multiple domains are space separated.
domain(s) (default: rutracker.org) : 🟥 [ENTER A WEBSITE DOMAIN NAME BLOCKED IN YOUR COUNTRY HERE - EXAMPLE: discord.com] 🟥
```

```
ip protocol version(s) - 4, 6 or 46 for both (default: 4) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
check http (default : Y) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
check https tls 1.2 (default : Y) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
check https tls 1.3 (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
how many times to repeat each test (default: 1) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
quick - scan as fast as possible to reveal any working strategy
standard - do investigation what works on your DPI
force - scan maximum despite of result
1 : quick
2 : standard
3 : force
your choice (default : standard) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

Wait for the test to finish. This may take a few minutes.

After the process is finished, the test results will appear.

Copy the latest setting from these results. Example:

```
curl_test_https_tls12 ipv4 discord.com : nfqws --dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=-5 --dpi-desync-split-pos=1
                                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                                                     MAKE A NOTE FOR IT
```

This is an example settings for **NFQWS**. It may be different for each person. Make a note of it.

```shell
--dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=-5 --dpi-desync-split-pos=1
```

## 7. Install Zapret

We can start installing Zapret.

```shell
# Start the installation
sudo ~/zapret-v72.2/install_easy.sh
```

Here are the answers you need to give to the questions you may encounter during this time.

```
do you want the installer to copy it for you (default : N) (Y/N) ? 🟥 [TYPE "Y"] 🟥
```

```
select firewall type :
1 : iptables
2 : nftables
your choice (default : nftables) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
enable ipv6 support (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
select flow offloading :
1 : none
2 : software
3 : hardware
your choice (default : none) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
select filtering :
1 : none
2 : ipset
3 : hostlist
4 : autohostlist
your choice (default : none) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
enable tpws socks mode on port 987 ? (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
enable tpws transparent mode ? (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
enable nfqws ? (default : N) (Y/N) ? 🟥 [TYPE "Y"] 🟥
```

```
do you want to edit the options (default : N) (Y/N) ? 🟥 [TYPE "Y"] 🟥
```

Then we write the **NFQWS** settings that we just copied to `NFQWS_OPT`. Example:

```ini
NFQWS_PORTS_TCP=80,443
NFQWS_PORTS_UDP=443
NFQWS_TCP_PKT_OUT=9
NFQWS_TCP_PKT_IN=3
NFQWS_UDP_PKT_OUT=9
NFQWS_UDP_PKT_IN=0
NFQWS_PORTS_TCP_KEEPALIVE=
NFQWS_PORTS_UDP_KEEPALIVE=
NFQWS_OPT="--dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=-5 --dpi-desync-split-pos=1"
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                YOUR SETTINGS HERE
```

Then save with **CTRL + S** and close with **CTRL + X**.

Let's continue with the questions.

```
do you want to edit the options (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
LAN interface :
1 : NONE
2 : lo
3 : wlp0s20f3
your choice (default : NONE) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
WAN interface :
1 : ANY
2 : lo
3 : wlp0s20f3
your choice (default : ANY) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

## 8. Finish the installation

All done! 🎉 We are done with this folder of Zapret anymore. We can delete it.

```shell
# Delete the folder
sudo rm -rf ~/zapret-v72.2
```

## TIP: Uninstall Zapret

If you ever regain your freedom, you can undo all of these actions in the following way.

```shell
# Uninstall Zapret
sudo /opt/zapret/uninstall_easy.sh

# Remove unused files
sudo rm -rf ~/zapret-v72.2
sudo rm -rf /opt/zapret
```

## TIP: Remove DNS settings

If you want to remove the DNS settings, you can do the following.

```shell
# Enable and start Systemd-Resolved
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved

# Leave the Systemd-Resolved configuration blank
sudo tee /etc/systemd/resolved.conf &>/dev/null <<< ""

# Make /etc/resolv.conf a symlink to Systemd-Resolved file
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

# Restart Systemd-Resolved for the changes to take effect
sudo systemctl restart systemd-resolved
```
