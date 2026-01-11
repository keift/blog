---
description: Install Zapret to bypass DPI barriers.
icon: lock-keyhole-open
---

**✨ Install Zapret in one step**

We've created an installation wizard that lets you install Zapret in a single step.

**Installation**

You can install it as follows.

```shell
curl -fsSL https://is.gd/install_zapret | bash
```

**Uninstall**

You can uninstall it as follows.

```shell
curl -fsSL https://is.gd/uninstall_zapret | bash
```

<details>
<summary>Archived: Step-by-step installation</summary>

## 1. Install required tools

Required tools for installation.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y curl dnsutils nftables systemd-resolved unzip

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y bind-utils curl nftables systemd-resolved unzip
sudo yum install -y bind-utils curl nftables systemd-resolved unzip

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm bind-tools curl nftables systemd-resolved unzip

# OpenSUSE (Zypper)
sudo zypper -n install bind-utils curl nftables systemd-resolved unzip
```

## 2. Change DNS rules

Zapret only bypasses DPI restrictions. But it does not set up a DNS for us. We need to do that ourselves.

- [Cloudflare DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-cloudflare-dns-recommended) (Recommended)
- [Mullvad DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-mullvad-dns)
- [Google DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-google-dns)
- [Yandex DNS](https://keift.gitbook.io/blog/linux/use-dns-over-tls#alternative-yandex-dns)

If your distribution does not include Systemd, you will need to do this using [Stubby](https://keift.gitbook.io/blog/linux/install-stubby).

## 3. Download Zapret

Download the compiled zip file as release on GitHub.

```shell
# Delete if present
sudo rm -rf /tmp/zapret-v72.7
sudo rm -rf /tmp/zapret-v72.7.zip

# Download the compiled zip file from GitHub
sudo wget -P /tmp https://github.com/bol-van/zapret/releases/download/v72.7/zapret-v72.7.zip

# Unzip the zip file
sudo unzip -d /tmp /tmp/zapret-v72.7.zip

# Delete the zip file that we no longer need
sudo rm -rf /tmp/zapret-v72.7.zip
```

## 4. Prepare for installation

Install the requirements and prepare to perform a clean install.

```shell
# For a clean installation, remove any installation files that may be present in case an installation has been made before
sudo /opt/zapret/uninstall_easy.sh
sudo rm -rf /opt/zapret

# Install requirements
sudo /tmp/zapret-v72.7/install_prereq.sh
sudo /tmp/zapret-v72.7/install_bin.sh
```

Here are the answers you need to give to the questions you may encounter during this time.

```
select firewall type :
1 : iptables
2 : nftables
your choice (default : nftables) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

## 5. Do Blockcheck

Find the DPI methods implemented by the ISP.

```shell
# Run the test
sudo /tmp/zapret-v72.7/blockcheck.sh
```

Here are the answers you need to give to the questions you may encounter during this time.

```
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

## 6. Install Zapret

We can start installing Zapret.

```shell
# Start the installation
sudo /tmp/zapret-v72.7/install_easy.sh
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

## 7. Finish the installation

All done! 🎉 We are done with this folder of Zapret anymore. We can delete it.

```shell
# Delete the folder
sudo rm -rf /tmp/zapret-v72.7
```

## TIP: Uninstall Zapret

You can uninstall it as follows.

```shell
# Uninstall Zapret
sudo /opt/zapret/uninstall_easy.sh

# Remove unused files
sudo rm -rf /opt/zapret
sudo rm -rf /tmp/zapret-v72.7
```

## TIP: Remove DNS settings

You can remove it as follows.

```shell
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

</details>
