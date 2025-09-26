---
description: Create a systemd service and run it at specific time intervals.
icon: timer
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install Neatd

Let's start creating the Systemd service.

```shell
curl -fsSL https://raw.githubusercontent.com/keift/neatd/refs/heads/main/install.sh | sh
```

## TIP: Uninstall Neatd

This is how you can uninstall service.

```shell
curl -fsSL https://raw.githubusercontent.com/keift/neatd/refs/heads/main/uninstall.sh | sh
```
