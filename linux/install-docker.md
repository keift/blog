## 1. Keep Hosts content up to date

If you have changed the hostname before, it may not have been updated in `/etc/hosts`. Correct this to avoid problems during installation.

```shell
# Specify the current hostname in /etc/hosts
sudo sed -i "s/^\(127\.0\.1\.1\s\+\)\S\+/\1$(hostname)/" /etc/hosts
```

## 2. Install Docker

Docker is a containerization service.

```shell
# Pull and run the specified shell script
curl -fsSL https://get.docker.com | sh
```

## TIP: Uninstall Docker

This is how you can uninstall Docker.

```shell
# Uninstall Docker
sudo apt purge -y docker docker-engine docker.io docker-ce docker-ce-cli
sudo dnf remove -y docker docker-ce docker-ce-cli containerd.io
sudo yum remove -y docker docker-ce docker-ce-cli containerd.io
sudo pacman -Rns --noconfirm docker

# Remove leftovers
sudo rm -rf /var/lib/docker /etc/docker /var/run/docker.sock
```
