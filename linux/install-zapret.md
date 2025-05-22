## 1. Keep Hosts content up to date

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install required tools

Required tools for installation.

```shell
# Debian, Ubuntu, Kali, Linux Mint (APT)
sudo apt install -y curl dnsutils unzip

# Red Hat, CentOS, Fedora, AlmaLinux, Rocky (DNF / YUM)
sudo dnf install -y curl bind-utils unzip
sudo yum install -y curl bind-utils unzip

# Arch, Manjaro (Pacman)
sudo pacman -S --noconfirm curl bind-tools unzip
```

## 3. Change DNS rules

Zapret only bypasses DPI restrictions. But it does not set up a DNS for us. We need to do that ourselves. We are using Stubby here.

```shell
# Install Stubby
sudo apt install -y stubby
sudo dnf install -y stubby
sudo yum install -y stubby
sudo pacman -S --noconfirm stubby

# Enable and start Stubby
sudo systemctl enable stubby
sudo systemctl start stubby

# Configure Stubby
sudo tee /etc/stubby/stubby.yml > /dev/null << EOF
  resolution_type: GETDNS_RESOLUTION_STUB
  dns_transport_list:
    - GETDNS_TRANSPORT_TLS

  tls_authentication: GETDNS_AUTHENTICATION_REQUIRED

  round_robin_upstreams: 1

  idle_timeout: 10000

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

# Unlock /etc/resolv.conf file if it is already locked
sudo chattr -i /etc/resolv.conf

# Delete the /etc/resolv.conf file as it may be set as a symlink
sudo rm -rf /etc/resolv.conf

# Rewrite the /etc/resolv.conf file and specify that we will use Stubby in it
sudo tee /etc/resolv.conf > /dev/null << EOF
  nameserver 127.0.0.1
  nameserver 77.88.8.8
  nameserver 77.88.8.1
  nameserver 2a02:6b8::feed:0ff
  nameserver 2a02:6b8:0:1::feed:0ff
EOF

# Make the file read-only so that the system cannot change it
sudo chattr +i /etc/resolv.conf

# Restart NetworkManager for the changes to take effect
sudo systemctl restart NetworkManager
```

## 4. Download Zapret

Download the compiled zip file as release on GitHub.

```shell
# Delete if present
rm -rf ~/zapret-v70.6.zip
rm -rf ~/zapret-v70.6

# Go to the home directory
cd ~/

# Download the compiled zip file from GitHub
wget https://github.com/bol-van/zapret/releases/download/v70.6/zapret-v70.6.zip
```

## 5. Unzip the zip file

Extract the zip file and then delete it.

```shell
# Unzip the zip file
unzip ~/zapret-v70.6.zip

# Delete the zip file that we no longer need
rm -rf ~/zapret-v70.6.zip
```

## 6. Prepare for installation

Install the requirements and prepare to perform a clean install.

```shell
# For a clean installation, remove any installation files that may be present in case an installation has been made before
~/zapret-v70.6/uninstall_easy.sh
/opt/zapret/uninstall_easy.sh
sudo rm -rf /opt/zapret

# Install requirements
~/zapret-v70.6/install_prereq.sh
~/zapret-v70.6/install_bin.sh
```

Here are the answers you need to give to the questions you may encounter during this time.

```
select firewall type :
1 : iptables
2 : nftables
your choice (default : nftables) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

## 7. Do Blockcheck

Find the DPI methods implemented by the ISP.

```shell
# Run the test
~/zapret-v70.6/blockcheck.sh
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
ipv4 discord.com curl_test_https_tls12 : nfqws --dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=5 --dpi-desync-split-pos=1
                                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                                                     MAKE A NOTE FOR IT
```

This is an example settings for **NFQWS**. It may be different for each person. Make a note of it.

```shell
--dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=5 --dpi-desync-split-pos=1
```

## 8. Install Zapret

We can start installing Zapret.

```shell
# Start the installation
~/zapret-v70.6/install_easy.sh
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
NFQWS_OPT="--dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=5 --dpi-desync-split-pos=1"
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
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
2 : docker0
3 : lo
4 : wlp0s20f3
your choice (default : NONE) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
WAN interface :
1 : ANY
2 : docker0
3 : lo
4 : wlp0s20f3
your choice (default : ANY) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```
select filtering :
1 : none
2 : ipset
3 : hostlist
4 : autohostlist
your choice (default : none) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

## 9. Finish the installation

All done! We are done with this folder of Zapret anymore. We can delete it.

```shell
# Delete the folder
rm -rf ~/zapret-v70.6
```

🎉 That's it! You have now overcome all access barriers. Long live freedom!

## TIP: Uninstall Zapret

If you ever regain your freedom, you can undo all of these actions in the following way.

```shell
# Uninstall Zapret
/opt/zapret/uninstall_easy.sh

# Delete unnecessary files
sudo rm -rf ~/zapret-v70.6
sudo rm -rf /opt/zapret
```

## TIP: Remove DNS settings

If you want to remove the DNS settings, you can do the following.

```shell
# Uninstall Stubby
sudo apt purge -y stubby
sudo dnf remove -y stubby
sudo yum remove -y stubby
sudo pacman -Rns --noconfirm stubby

# Unlock /etc/resolv.conf file if it is already locked
sudo chattr -i /etc/resolv.conf

# Delete /etc/resolv.conf file to reset it to default
sudo rm -rf /etc/resolv.conf

# Restart the system for everything to work properly
sudo reboot
```
