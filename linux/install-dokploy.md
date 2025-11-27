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

## RECOMMENDED: Docker Cleanup Schedule

It's important to clean up your Docker periodically. You can do this by creating a new schedule from the **Schedules** page. This will clean up unused containers, images, volumes and caches.

> Task Name:
>
> ```
> Hourly Docker Cleanup
> ```
>
> Schedule (Hourly):
>
> ```
> 0 * * * *
> ```
>
> Script:
>
> ```shell
> docker container prune --force
> docker image prune --all --force
> docker volume prune --all --force
> docker builder prune --all --force
> docker system prune --all --volumes --force
> ```

## TIP: Uninstall Dokploy

This is how you can uninstall Dokploy.

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

If you want to remove Docker as well, [continue here](https://keift.gitbook.io/blog/linux/install-docker#tip-uninstall-docker).
