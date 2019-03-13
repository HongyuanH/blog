---
layout:            post
title:             "Using Docker with X11 via SSH"
date:              2019-03-13
tags:              Docker
category:          Docker
author:            hongyuan

---

## Just another Script

Copy-and-paste the below script, replace `CONTAINER_IMAGE` with your own image name and enjoy (tested on Ubuntu 14.04 and 16.04):

```bash
CONTAINER_IMAGE="ubuntu14-x11"
DISPLAY_DIR=/tmp/docker/display

CONTAINER_HOSTNAME="container-${CONTAINER_IMAGE}"
DISPLAY_NUMBER=$(echo $DISPLAY | cut -d. -f1 | cut -d: -f2)
AUTH_COOKIE=$(xauth list | grep "^$(hostname)/unix:${DISPLAY_NUMBER} " | awk '{print $3}')

# Create a temporary directory for DISPLAY and Xauthority
mkdir -p ${DISPLAY_DIR}/X11-unix
touch ${DISPLAY_DIR}/Xauthority
xauth -f ${DISPLAY_DIR}/Xauthority add ${CONTAINER_HOSTNAME}/unix:0 MIT-MAGIC-COOKIE-1 ${AUTH_COOKIE}

# Proxy DISPLAY & run docker
socat TCP4:localhost:60${DISPLAY_NUMBER} UNIX-LISTEN:${DISPLAY_DIR}/X11-unix/X0 &
sudo docker run -it --rm \
  -e DISPLAY=:0 \
  -v ${DISPLAY_DIR}/X11-unix:/tmp/.X11-unix \
  -v ${DISPLAY_DIR}/Xauthority:/home/${USER}/.Xauthority \
  --hostname ${CONTAINER_HOSTNAME} \
  --user ${USER}:${USER} \
  ${CONTAINER_IMAGE}
```

## References
Running a graphical app in a Docker container, on a remote server([https://blog.yadutaf.fr/2017/09/10/running-a-graphical-app-in-a-docker-container-on-a-remote-server/](https://blog.yadutaf.fr/2017/09/10/running-a-graphical-app-in-a-docker-container-on-a-remote-server/))
