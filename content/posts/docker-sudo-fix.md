+++
date = '2025-02-01T16:01:19+05:30'
draft = false
title = 'Fix: Access `docker` without sudo'
tags = [ "docs", "docker", "script" ]

+++

# Why this occurs?
I don't whether it's happening to everyone, but whenever I try to install docker I always end up facing an issue, which won't let me use docker unless I use `sudo` while executing the `docker` commands. Why it's occurs? Well, it's because the permission denied when you are trying to connect with the `docker daemon socket` at unix://var/run/docker.sock. (you will also see an error similar to this.

The simple fix would be adding docker into a group which will let you use it without using `sudo` command everytime.

# Let's fix it!
1. Verify Docker group exists:
```sh
getent group | grep docker
```
>  If not, create a group called `docker` then.
2. Assign the `docker` group to the user:
```sh
sudo usermod -aG docker $USER
```
> $USER defines the current user.
3. Apply group changes:
```sh
newgrp docker
```
4. Restart Docker Engine to make sure the changes takes effect globally:
```sh
sudo systemctl restart docker.service
```
5. Let's verify the group memebership:
```sh
getent group | grep docker
```

## Alternative method, which requires simple fixes!
Sometimes, there's no major problems will occurs but something so simple will need to be done to fix the problem, now let's look at the common troubleshooting methods:
1. Check if the Docker is running:
```sh
sudo systemctl status docker
```
```sh
sudo systemctl enable docker
```
```sh
sudo systemctl start docker
```

2. Verify Docker Socket:
Ensure the Docker is usng the correct socket file. The typical socket file for Docker is `var/run/docker.sock`. Let's check:
```sh
ls -la /var/run/docker.sock
```
> if not present or points to different location, you might need to adjust manually.

3. Use the correct socket path:
```bash
DOCKER_HOST=unix:///var/run/docker.sock docker images  
```
> If this works, your Docker installation may be misconfigured, and you can update the Docker socket location in your environment variables or Docker settings.
4. Let's `DOCKET_HOST` to `.bashrc` or `.bash_profile`
```bash
echo 'export DOCKER_HOST=unix:///var/run/docker.sock' >> ~/.bashrc  
source ~/.bashrc  
```
> This will set the `DOCKER_HOST` environment variable automatically in all your terminal sessions.
