---
description: Deploy applications quickly.
icon: docker
---

## 1. Update Hosts content

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "/^127\.0\.1\.1\s\+/s/\S\+$/$(hostname)/" /etc/hosts
```

## 2. Install Dokploy

Dokploy is a deployment service.

```shell
# Pull and run the specified shell script
curl -fsSL https://get.docker.com | sh

# Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker

# Pull and run the specified shell script
curl -sSL https://dokploy.com/install.sh | sh
```

## TIP: Uninstall Dokploy

This is how you can uninstall Dokploy.

```shell
# Remove the Docker swarm services
sudo docker service rm dokploy dokploy-traefik dokploy-postgres dokploy-redis

# Remove the Docker volumes
sudo docker volume rm -f dokploy-postgres-database redis-data-volume

# Remove the Docker network
sudo docker network rm -f dokploy-network

# Remove unused files
sudo rm -rf /etc/dokploy
```

If you want to remove Docker as well, [continue here](https://keift.gitbook.io/blog/linux/install-docker#tip-uninstall-docker).
