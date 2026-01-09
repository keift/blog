---
description: Install Zapret to bypass DPI barriers.
icon: lock-keyhole-open
---

## 1. Install required tools

Required tools for installation.

```shell
# FreeBSD, DragonFlyBSD, TrueNAS, MidnightBSD, GhostBSD (pkg)
sudo pkg install -y curl bind-tools unzip unbound

# NetBSD (pkgsrc)
sudo pkgin install curl unzip bind unbound

# OpenBSD (pkgsrc)
sudo pkg_add curl unzip bind unbound

# Others bsd's can install from their package managers, source or cargo (rust implement, not recommended).
```

## 2. Change DNS rules

Zapret only bypasses dpi, its not going to add dns for yourself. Get cool and add a dns over tls with unbound :3
Be sure pf rules flushed and firewall state is open.
Im gonna use cloudflare's dns for myself... so u can use any dns what is suitable for yourself (eg yandex dns if youre in russia)

```shell
# Before ipfw fucks your internet, you need configure your firewall state to open before enabling
sudo sysrc firewall_type="open"
sudo sysrc firewall_enable="YES"

# Backup your old unbound config and replace it with this (or if you advanced merge it)
sudo cp /usr/local/etc/unbound/unbound.conf /usr/local/etc/unbound/unbound.conf.bak
sudo tee /usr/local/etc/unbound/unbound.conf &>/dev/null <<'EOF'
server:
    interface: 127.0.0.1
    do-ip4: yes
    do-ip6: no
    do-udp: yes
    do-tcp: yes
    qname-minimisation: no

forward-zone:
    name: "."
    forward-tls-upstream: yes
    forward-addr: 1.1.1.1@853
    forward-addr: 1.0.0.1@853
EOF

# Enable and start unbound
sudo service enable unbound
sudo service start unbound

# add 127.0.0.1 on your resolv.conf and lock
sudo mv /etc/resolv.conf /etc/resolv.conf.bak
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
sudo chflags schg /etc/resolv.conf
```

## 3. Download Zapret

Download the compiled zip file as release on GitHub.

```shell
# Go to /usr/local and become root
cd /usr/local

# Download the compiled zip file from GitHub
git clone https://github.com/bol-van/zapret.git
```

## 4. Prepare for installation

build and prepare to perform installation.

```shell
# so you need build binaries
cd /usr/local/zapret
make

# and link the binaries
./install_bin.sh
```

## 5. Do Blockcheck

Find the DPI methods implemented by the ISP.

```shell
# Run the test
/usr/local/zapret/blockcheck.sh
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
ipv4 discord.com curl_test_https_tls12 : dvtws --dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=-5 --dpi-desync-split-pos=1
                                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                                                     MAKE A NOTE FOR IT
```

This is an example settings for **DVTWS**. It may be different for each person. Make a note of it.

```shell
--dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=-5 --dpi-desync-split-pos=1
```

## 6. Install Zapret

We can start installing Zapret.
Also there's dvtws implement for nfqws because bsd systems doesnt use network filter libs.

```shell
# add this ipfw rule for dvtws
sudo sh -c 'echo "ipfw add 100 divert 60000 ip from any to any" > /etc/ipfw.rules'
sudo sysrc firewall_script="/etc/ipfw.rules"

# first lets find out which init system the system is using
sudo ps -p 1 -o comm=

# if runit continue from here
# else jump to 8.2
```

## 6.1 Zapret service on Runit BSD Systems

```shell
mkdir -p /usr/local/etc/sv/nfqws
mkdir /usr/local/etc/sv/nfqws/log

cat > /usr/local/etc/sv/nfqws/run << 'EOF'
#!/bin/sh
exec /usr/local/zapret/binaries/my/dvtws \
  --port=60000 \
  --dpi-desync=fakeddisorder \
  --dpi-desync-ttl=1 \
  --dpi-desync-autottl=-5 \
  --dpi-desync-split-pos=1
EOF
chmod +x /usr/local/etc/sv/nfqws/run

cat > /usr/local/etc/sv/nfqws/log/run << 'EOF'
#!/bin/sh
exec svlogd -tt /var/log/nfqws
EOF
chmod +x /usr/local/etc/sv/nfqws/log/run

ln -s /usr/local/etc/sv/nfqws /usr/local/etc/runit/runsvdir/default/
```

## 6.2 Zapret service on rc.d BSD Systems

```shell
cat > /usr/local/etc/rc.d/nfqws << 'EOF'
#!/bin/sh
# PROVIDE: nfqws
# REQUIRE: NETWORKING
# KEYWORD: shutdown

. /etc/rc.subr

name="nfqws"
rcvar="${name}_enable"
command="/usr/local/zapret/binaries/my/dvtws"
command_args="--port=60000 --dpi-desync=fakeddisorder --dpi-desync-ttl=1 --dpi-desync-autottl=-5 --dpi-desync-split-pos=1"

load_rc_config $name
run_rc_command "$1"
EOF
chmod +x /usr/local/etc/rc.d/nfqws
service nfqws enable
service nfqws start
```

## TIP: Uninstall Zapret

If you ever regain your freedom, you can undo all of these actions in the following way.

```shell
# Unlock resolve.conf and replace it with backup one
sudo chflags noschg /etc/resolv.conf
sudo mv /etc/resolv.conf.bak /etc/resolv.conf

# Uninstall Zapret
sudo rm -rf /usr/local/zapret

# Disable services

# For rc.d
sudo sysrc nfqws_enable="NO"
sudo service nfqws stop

# For runit
sudo rm /usr/local/etc/runit/runsvdir/default/nfqws
sudo sv stop nfqws
```

## TIP: Remove DNS settings

If you want to remove the DNS settings, you can do the following.

```shell
# disable unbound
sudo sysrc unbound_enable="NO"
sudo service unbount stop

# disable firewall
sudo sysrc firewall_enable="NO"
```
