# Install Zapret

Install Zapret to bypass DPI barriers

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
  - "::1@53"

upstream_recursive_servers:
  - address_data: 77.88.8.8
    tls_port: 853
    tls_auth_name: common.dot.dns.yandex.net
  - address_data: 77.88.8.1
    tls_port: 853
    tls_auth_name: common.dot.dns.yandex.net
  - address_data: 2a02:6b8::feed:0ff
    tls_port: 853
    tls_auth_name: common.dot.dns.yandex.net
  - address_data: 2a02:6b8:0:1::feed:0ff
    tls_port: 853
    tls_auth_name: common.dot.dns.yandex.net
EOF

# Restart Stubby
sudo systemctl restart stubby

# Unlock /etc/resolv.conf file if it is already locked
sudo chattr -i /etc/resolv.conf

# Delete the /etc/resolv.conf file as it may be set as a symlink
sudo rm -rf /etc/resolv.conf

# Rewrite the /etc/resolv.conf file and specify that we will use Stubby in it
echo -e "nameserver 127.0.0.1\nnameserver 77.88.8.8\nnameserver 77.88.8.1" | sudo tee /etc/resolv.conf

# Make the file read-only so that the system cannot change it
sudo chattr +i /etc/resolv.conf

# Restart NetworkManager for the changes to take effect
sudo systemctl restart NetworkManager
```

## 4. Download Zapret

Download the compiled zip file as release on GitHub.

```shell
# Delete if present
rm -rf ./zapret-v70.5.zip
rm -rf ./zapret-v70.5

# Go to the home directory
cd ~/

# Download the compiled zip file from GitHub
wget https://github.com/bol-van/zapret/releases/download/v70.5/zapret-v70.5.zip
```

## 5. Unzip the zip file

Extract the zip file and then delete it.

```shell
# Unzip the zip file
unzip ./zapret-v70.5.zip

# Delete the zip file that we no longer need
rm -rf ./zapret-v70.5.zip
```

## 6. Prepare for installation

Install the requirements and prepare to perform a clean install.

```shell
# Enter the folder
cd ./zapret-v70.5

# For a clean installation, remove any installation files that may be present in case an installation has been made before
./uninstall_easy.sh
/opt/zapret/uninstall_easy.sh
sudo rm -rf /opt/zapret

# Install requirements
./install_prereq.sh
./install_bin.sh
```

Questions that may arise at this time:

```shell
# 1 - FIRST QUESTION
select firewall type :
1 : iptables
2 : nftables
your choice (default : nftables) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

## 7. Do Blockcheck

Find the DPI methods implemented by the ISP.

```shell
# Run the test
./blockcheck.sh
```

Questions that may arise at this time:

```shell
# 1 - FIRST QUESTION
specify domain(s) to test. multiple domains are space separated.
domain(s) (default: rutracker.org) : 🟥 [ENTER A WEBSITE DOMAIN NAME BLOCKED IN YOUR COUNTRY HERE - EXAMPLE: discord.com] 🟥
```

```shell
# 2 - SECOND QUESTION
ip protocol version(s) - 4, 6 or 46 for both (default: 4) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 3 - THIRD QUESTION
check http (default : Y) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 4 - FOURTH QUESTION
check https tls 1.2 (default : Y) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 5 - FIFTH QUESTION
check https tls 1.3 (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 6 - SIXTH QUESTION
how many times to repeat each test (default: 1) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 7 - SEVENTH QUESTION
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

```shell
ipv4 discord.com curl_test_https_tls12 : nfqws --dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=5 --dpi-desync-split-pos=1
                                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                                                     MAKE A NOTE FOR IT
```

This is an example settings for **NFQWS**. It may be different for each person. Make a note of it.

```shell
--dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=5 --dpi-desync-split-pos=1
```


## 8. Install Zapret

Once everything is complete, we can start installing Zapret.

```shell
# Start the installation
./install_easy.sh
```

Questions that may arise at this time:

```shell
# 1 - FIRST QUESTION
do you want the installer to copy it for you (default : N) (Y/N) ? 🟥 [TYPE "Y"] 🟥
```

```shell
# 2 - SECOND QUESTION
select firewall type :
1 : iptables
2 : nftables
your choice (default : nftables) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 3 - THIRD QUESTION
enable ipv6 support (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 4 - FOURTH QUESTION
select flow offloading :
1 : none
2 : software
3 : hardware
your choice (default : none) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 5 - FIFTH QUESTION
enable tpws socks mode on port 987 ? (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 6 - SIXTH QUESTION
enable tpws transparent mode ? (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 7 - SEVENTH QUESTION
enable nfqws ? (default : N) (Y/N) ? 🟥 [TYPE "Y"] 🟥
```

```shell
# 8 - EIGHTH QUESTION
do you want to edit the options (default : N) (Y/N) ? 🟥 [TYPE "Y"] 🟥
```

Then we write the **NFQWS** settings that we just copied to `NFQWS_OPT`. Example:

```shell
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

Let's continue with the questions:

```shell
# 8 - EIGHTH QUESTION
do you want to edit the options (default : N) (Y/N) ? 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 9 - NINTH QUESTION
LAN interface :
1 : NONE
2 : docker0
3 : lo
4 : wlp0s20f3
your choice (default : NONE) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 10 - TENTH QUESTION
WAN interface :
1 : ANY
2 : docker0
3 : lo
4 : wlp0s20f3
your choice (default : ANY) : 🟩 [LEAVE THIS QUESTION BLANK] 🟩
```

```shell
# 11 - ELEVENTH QUESTION
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
# Come back
cd ../

# Delete the folder
rm -rf ./zapret-v70.5
```

🎉 That's it! You have now overcome all access barriers. Long live freedom!

## TIP: Uninstall Zapret

If you ever regain your freedom, you can undo all of these actions in the following way.

```shell
# Uninstall Zapret and delete unnecessary files
/opt/zapret/uninstall_easy.sh
sudo rm -rf /opt/zapret
```

## TIP: Remove DNS settings

To remove DNS settings, you can do the following.

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
