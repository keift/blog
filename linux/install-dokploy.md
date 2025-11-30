---
description: Deploy applications quickly.
icon: docker
---

## Install Dokploy

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
> CHECK_INTERVAL=10
>
> echo "Starting Docker cleanup..."
>
> while true; do
>     PROCESSES=$(ps aux | grep -E "docker build|docker pull" | grep -v grep)
>
>     if [ -z "$PROCESSES" ]; then
>         echo "Docker is idle. Starting cleanup..."
>         break
>     else
>         echo "Docker is busy. Will check again in $CHECK_INTERVAL seconds..."
>         sleep $CHECK_INTERVAL
>     fi
> done
>
> docker container prune --force
> docker image prune --all --force
> docker volume prune --all --force
> docker builder prune --all --force
> docker system prune --all --volumes --force
>
> echo "Docker cleanup completed."
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
