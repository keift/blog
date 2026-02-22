---
description: Deploy applications quickly.
icon: docker
---

## Install Dokploy

You can install it as follows.

```shell
# Pull and run the specified shell script
curl -sSL https://dokploy.com/install.sh | sh
```

## TIP: Uninstall Dokploy

You can uninstall it as follows.

```shell
# Remove Dokploy services
sudo docker service remove dokploy dokploy-traefik dokploy-postgres dokploy-redis
sudo docker container remove -f dokploy-traefik

# Remove Dokploy volumes
sudo docker volume remove -f dokploy dokploy-postgres dokploy-redis

# Remove Dokploy network
sudo docker network remove -f dokploy-network

# Cleanup Docker
sudo docker container prune --force
sudo docker image prune --all --force
sudo docker volume prune --all --force
sudo docker builder prune --all --force
sudo docker system prune --all --volumes --force

# Remove unused files
sudo rm -rf /etc/dokploy
```

If you want to remove Docker as well, [continue here](https://keift.gitbook.io/guides/linux/install-docker#tip-uninstall-docker).
