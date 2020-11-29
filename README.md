[![docker-ICEcoder](https://i.griefed.de/images/2020/11/18/docker-ICEcoder_header.png)](https://github.com/Griefed/docker-ICEcoder)

---

[![Docker Pulls](https://img.shields.io/docker/pulls/griefed/icecoder?style=flat-square)](https://hub.docker.com/repository/docker/griefed/icecoder)
[![Docker Image Size (latest by date)](https://img.shields.io/docker/image-size/griefed/icecoder?label=Image%20size&sort=date&style=flat-square)](https://hub.docker.com/repository/docker/griefed/icecoder)
[![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/griefed/icecoder?label=Docker%20build&style=flat-square)](https://hub.docker.com/repository/docker/griefed/icecoder)
[![Docker Cloud Automated build](https://img.shields.io/docker/cloud/automated/griefed/icecoder?label=Docker%20build&style=flat-square)](https://hub.docker.com/repository/docker/griefed/icecoder)
[![GitHub Repo stars](https://img.shields.io/github/stars/Griefed/docker-ICEcoder?label=GitHub%20Stars&style=social)](https://github.com/Griefed/docker-ICEcoder)
[![GitHub forks](https://img.shields.io/github/forks/Griefed/docker-ICEcoder?label=GitHub%20Forks&style=social)](https://github.com/Griefed/docker-ICEcoder)

# docker-ICEcoder

ICEcoder is a browser based code editor, which provides a modern approach to building websites. By allowing you to code directly within the web browser, online or offline, it means you only need one program (your browser) to develop sites, plus can test on actual web servers. After development, you can also maintain the website easily, all of which make for speedy and smart development.

[![ICEcoder](https://i.griefed.de/images/2020/11/18/docker-ICEcoder_screenshot.png)](https://github.com/icecoder/ICEcoder)

---

Creates a Container which runs [icecoder's](https://github.com/icecoder) [ICEcoder](https://github.com/icecoder/ICEcoder), with [lsiobase/alpine](https://hub.docker.com/r/lsiobase/alpine) as the base image, as seen on http://demo.icecoder.net/ICEcoder/.

The [lsiobase/alpine](https://hub.docker.com/r/lsiobase/alpine) image is a custom base image built with [Alpine linux](https://alpinelinux.org/) and [S6 overlay](https://github.com/just-containers/s6-overlay).
Using this image allows us to use the same user/group ids in the container as on the host, making file transfers much easier

# Deployment

## Pre-built images

Using docker-compose:

```docker-compose.yml
version: "2"
services:
  icecoder:
    image: griefed/icecoder:latest
    container_name: icecoder
    restart: unless-stopped
    environment:
      - TZ=Europe/Berlin # Timezone
      - PUID=1000 # User ID
      - PGID=1000 # Group ID
      - GITURL=https://github.com/icecoder/ICEcoder.git # GitHub Repository to clone during container creation
    volumes:
      - /host/path/to/plugins:/plugins # Contains all installed plugins.
      - /host/path/to/data:/data # Contains ICEcoder data like backups.
      - /host/path/to/config:/config # Contains config files and logs.
      - /host/path/to/code:/code # Contains GITURL repository.
    ports:
      - 8080:8080/tcp # WebIDE
```

Using CLI:

```bash
docker create \
  --name=icecoder \
  -e TZ=Europe/Berlin \
  -e PUID=1000 \
  -e PGID=1000 \
  -e GITURL=https://github.com/icecoder/ICEcoder.git \
  -v /host/path/to/plugins:/plugins \
  -v /host/path/to/data:/data \
  -v /host/path/to/config:/config \
  -v /host/path/to/code:/code \
  -p 8080:8080 \
  --restart unless-stopped \
  griefed/icecoder:latest
```

## Raspberry Pi

To run this container on a Raspberry Pi, use the `arm`-tag. I've tested it on a Raspberry Pi 3B.

`griefed/icecoder:arm`

# Configuration

Configuration | Explanation
------------ | -------------
[Restart policy](https://docs.docker.com/compose/compose-file/#restart) | "no", always, on-failure, unless-stopped
code volume | Contains GITURL repository.
config volume | Contains config files and logs.
data volume | Contains ICEcoder data like backups.
plugins volume | Contains all installed plugins.
TZ | Timezone
PUID | for UserID
PGID | for GroupID
ports | The port where the service will be available at.

## User / Group Identifiers

When using volumes, permissions issues can arise between the host OS and the container. [Linuxserver.io](https://www.linuxserver.io/) avoids this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id user` as below:

```
  $ id username
    uid=1000(dockeruser) gid=1000(dockergroup) groups=1000(dockergroup)
```

# Building the image yourself

Use the [Dockerfile](https://github.com/Griefed/docker-ICEcoder/Dockerfile) to build the image yourself, in case you want to make any changes to it

docker-compose.yml:

```docker-compose.yml
version: '3.6'
services:
  icecoder:
    build: ./docker-ICEcoder/
    container_name: icecoder
    restart: unless-stopped
    environment:
      - TZ=Europe/Berlin # Timezone
      - PUID=1000 # User ID
      - PGID=1000 # Group ID
      - GITURL=https://github.com/icecoder/ICEcoder.git # GitHub Repository to clone during container creation
    volumes:
      - /host/path/to/plugins:/plugins # Contains all installed plugins.
      - /host/path/to/data:/data # Contains ICEcoder data like backups.
      - /host/path/to/config:/config # Contains config files and logs.
      - /host/path/to/code:/code # Contains GITURL repository.
    ports:
      - 8080:8080/tcp # WebIDE
```

1. Clone the repository: `git clone https://github.com/Griefed/docker-ICEcoder.git ./docker-ICEcoder`
1. Prepare docker-compose.yml file as seen above
1. `docker-compose up -d --build icecoder`
1. Visit IP.ADDRESS.OF.HOST:8080
1. ???
1. Profit!
