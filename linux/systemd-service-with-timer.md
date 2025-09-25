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

## 2. Create a service

Let's start creating the Systemd service.

```shell
# 🟥 Environment variables
SERVICE_NAME="neat-deps"

# Create the script in the local binaries folder
sudo tee /usr/local/bin/$SERVICE_NAME.sh > /dev/null << EOF
  #!/bin/bash

  set -e

  apt update -y || true
  apt upgrade -y || true
  apt autoremove -y || true

  dnf check-update -y || true
  dnf upgrade -y || true
  dnf autoremove -y || true

  yum check-update -y || true
  yum update -y || true
  yum autoremove -y || true

  pacman -Syu --noconfirm || true
  pacman -Rns --noconfirm $(pacman -Qdtq) || true
EOF

# Make the script executable
sudo chmod +x /usr/local/bin/$SERVICE_NAME.sh

# Create the service in the Systemd folder
sudo tee /etc/systemd/system/$SERVICE_NAME.service > /dev/null << EOF
  [Service]
  Type=oneshot
  ExecStart=/usr/local/bin/$SERVICE_NAME.sh
EOF


# Create the timer in the Systemd folder
sudo tee /etc/systemd/system/$SERVICE_NAME.timer > /dev/null << EOF
  [Timer]
  OnCalendar=daily
  Persistent=true

  [Install]
  WantedBy=timers.target
EOF

# Restart Systemd daemon
sudo systemctl daemon-reload

# Enable and start Timer
sudo systemctl enable $SERVICE_NAME.timer
sudo systemctl start $SERVICE_NAME.timer
```

## TIP: Uninstall Service

This is how you can uninstall service.

```shell
# 🟥 Environment variables
SERVICE_NAME="neat-deps"

# Delete script
sudo rm -rf /usr/local/bin/$SERVICE_NAME.sh

# Delete service
sudo rm -rf /etc/systemd/system/$SERVICE_NAME.*

# Restart Systemd daemon
sudo systemctl daemon-reload
```
