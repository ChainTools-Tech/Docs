---
description: >-
  Guide describing how to deploy Dyson Protocol node containers using rootless
  environment and Podman as containerization platform.
---

# Dyson Protocol Node Deployment with Podman

{% embed url="https://odysee.com/@Drafts:9/ct_Academy_DysonNode_Podman:4" %}

### Docker vs Podman

Docker and Podman are both containerization platforms that enable developers to build, package, and distribute applications in a consistent and reproducible manner. While they share many similarities, there are also notable differences between the two:

1. **Daemon vs. Daemonless**: Docker uses a client-server architecture, where a Docker daemon runs in the background and manages containers. In contrast, Podman is daemonless and runs containers directly, which can reduce complexity and security risks.
2. **Root vs. Rootless**: Docker typically requires root privileges for most operations, which can introduce security concerns. Podman, on the other hand, supports running containers without root privileges (rootless), which enhances security and allows non-privileged users to manage containers.
3. **CLI Compatibility**: Podman aims to be a drop-in replacement for Docker, and its CLI commands closely mirror Docker's. However, there might be some minor differences, so it is essential to consult the respective documentation when transitioning from one platform to another.
4. **Container Orchestration**: Docker has native support for Docker Swarm, a container orchestration platform. Podman, on the other hand, does not have built-in orchestration support but can work with Kubernetes through the CRI-O runtime.
5. **Image Storage**: Docker uses a centralized image storage mechanism, while Podman employs a distributed approach. This allows Podman to isolate image storage for different users, enhancing security and preventing potential conflicts.
6. **API Compatibility**: Docker has a well-documented REST API for interacting with the Docker daemon. Podman, being daemonless, does not provide a native REST API. However, Podman can be configured to support the Docker API through the podman-docker package or the Podman API service.
7. **Community and Support**: Docker has a larger community and more extensive commercial support, owing to its longer history and widespread adoption. Podman is a newer project with a growing community, primarily supported by Red Hat.

In summary, Docker and Podman are both powerful containerization tools, but they differ in their architecture, security features, and some aspects of their functionality. Podman is an appealing alternative for those who prioritize rootless container management and a daemonless architecture, while Docker remains popular for its extensive community, support, and integration with Docker Swarm.

### Update system

```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

### Required packages

```bash
sudo apt install entr jq make git ufw podman podman-docker docker-compose
```

### Create Dyson node user

```bash
sudo adduser dyson
```

Once dedicated user is created access system using `dyson` user to continue deployment.

### Enable and start podman.socket as a user

```bash
systemctl --user enable podman.socket
systemctl --user start podman.socket
systemctl --user status podman.socket
export DOCKER_HOST=unix:///run/user/$UID/podman/podman.sock
```

**NOTE:** Commands `su - <user>` or `sudo -u <user>` do not preserve environment variables like `$DBUS_SESSION_BUS_ADDRESS` and `$XDG_RUNTIME_DIR`. It is required to use ssh to login as **dyson** user to have environmental variables set correctly for Podman: `ssh <user>@localhost`

#### Prevent container termination at logoff

By default container started in user environment will be terminated once user will log off. To avoid that enable linger state `sudo loginctl enable-linger`.

### Node installation

```
git clone --recurse-submodules https://gitlab.com/dysonproject/dyson-deploy.git
cd dyson-deploy
```

### Configurartion adjustments

To build and run containers we will use `docker-compose`. As Podman does not support `docker compose` syntax it is required to adju **Changes to `Makefile`**

* replace `docker compose` with `docker-compose`

**Changes to `scripts/start.sh`**

* replace `docker compose` with `docker-compose`

**Changes to `docker-compose.yml`**

* replace `links` with `depends_on`
* change `443:443` to `8443:443`
* change `80:80` to `8080:80`

```
$ make reset # if you had already joined the testnet
$ make testnet # prepare .env file for testnet deployment
```

### Run containers

```bash
TAG=v0.1.1 docker-compose up -d
```
