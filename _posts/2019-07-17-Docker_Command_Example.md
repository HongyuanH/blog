---
layout:            post
title:             "Docker Command Example"
date:              2019-07-17
tags:              Docker
category:          Docker
author:            hongyuan

---

## Managing Images

| **Command**     | `docker image build` |
| **Description** | Build an image from a Dockerfile |
| **Shortcut**    | `docker build` |
| **Usage**       | `docker build [OPTIONS] PATH | URL | -` |
| **Example**     | `docker build -t hello-world:latest .` |
|||
| **Command**     | `docker image ls` |
| **Description** | List images |
| **Shortcut**    | |
| **Usage**       | `docker image ls [OPTIONS] [REPOSITORY[:TAG]` |
| **Example**     | `docker image ls -a` |
|||
| **Command**     | `docker image rm` |
| **Description** | Remove one or more images |
| **Shortcut**    | |
| **Usage**       | `docker image rm [OPTIONS] IMAGE [IMAGE...]` |
| **Example**     | `docker image rm -f hello-world` |
|||
| **Command**     | `docker image save` |
| **Description** | Save one or more images to a tar archive (streamed to STDOUT by default) |
| **Shortcut**    | `docker save` |
| **Usage**       | `docker save [OPTIONS] IMAGE [IMAGE...]` |
| **Example**     | `docker save  -o hello-world.tar hello-world` |
|||
| **Command**     | `docker image load` |
| **Description** | Load an image from a tar archive or STDIN |
| **Shortcut**    | `docker load` |
| **Usage**       | `docker load [OPTIONS]` |
| **Example**     | `docker load -i hello-world.tar` |
|||
| **Command**     | `docker image tag` |
| **Description** | Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE |
| **Shortcut**    | `docker tag` |
| **Usage**       | `docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]` |
| **Example**     | `docker tag hello-world:latest my-hello:latest` |

## Managing Containers

| **Command**     | `docker container ls` |
| **Description** | List containers |
| **Shortcut**    | `docker ps` |
| **Usage**       | `docker ps [OPTIONS]` |
| **Example**     | `docker ps -a` |
|||
| **Command**     | `docker container run` |
| **Description** | Run a command in a new container |
| **Shortcut**    | `docker run` |
| **Usage**       | `docker run [OPTIONS] IMAGE [COMMAND] [ARG...]` |
| **Example**     | `docker run -it ubuntu:14.04` |
|||
| **Command**     | `docker container exec` |
| **Description** | Run a command in a running container |
| **Shortcut**    | `docker exec` |
| **Usage**       | `docker exec [OPTIONS] CONTAINER COMMAND [ARG...]` |
| **Example**     | `docker exec -it 8b0035f7f961 bash` |
|||
| **Command**     | `docker container create` |
| **Description** | Create a new container |
| **Shortcut**    | `docker create` |
| **Usage**       | `docker create [OPTIONS] IMAGE [COMMAND] [ARG...]` |
| **Example**     | `docker create --net host hello-world` |
|||
| **Command**     | `docker container rm` |
| **Description** | Remove one or more containers |
| **Shortcut**    | `docker rm` |
| **Usage**       | `docker rm [OPTIONS] CONTAINER [CONTAINER...]` |
| **Example**     | `docker rm -f 8b0035f7f961` |
|||
| **Command**     | `docker container start` |
| **Description** | Start one or more stopped containers |
| **Shortcut**    | `docker start` |
| **Usage**       | `docker start [OPTIONS] CONTAINER [CONTAINER...]` |
| **Example**     | `docker start 8b0035f7f961` |
|||
| **Command**     | `docker container attach` |
| **Description** | Attach local standard input, output, and error streams to a running container |
| **Shortcut**    | `docker attach` |
| **Usage**       | `docker attach [OPTIONS] CONTAINER` |
| **Example**     | `docker attach 8b0035f7f961` |
|||
| **Command**     | `docker container stop` |
| **Description** | Stop one or more running containers |
| **Shortcut**    | `docker stop` |
| **Usage**       | `docker stop [OPTIONS] CONTAINER [CONTAINER...]` |
| **Example**     | `docker stop -t 10 8b0035f7f961` |
|||
| **Command**     | `docker container kill` |
| **Description** | Kill one or more running containers |
| **Shortcut**    | `docker kill` |
| **Usage**       | `docker kill [OPTIONS] CONTAINER [CONTAINER...]` |
| **Example**     | `docker kill 8b0035f7f961` |
|||
| **Command**     | `docker container cp` |
| **Description** | Copy files/folders between a container and the local filesystem |
| **Shortcut**    | `docker cp` |
| **Usage**       | `docker cp [OPTIONS] SRC_PATH DEST_PATH` |
| **Example**     | `docker cp -a -L ./src 8b0035f7f961:/dst` |
|||
| **Command**     | `docker container commit` |
| **Description** | Create a new image from a container’s changes |
| **Shortcut**    | `docker commit` |
| **Usage**       | `docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]` |
| **Example**     | `docker commit 8b0035f7f961 my-hello:latest` |

## Tips

To run a image in interactive mode (`bash` can be skipped if default):

```
sudo docker run -it ${IMAGE} bash
```

To open a new terminal inside a container:

```
sudo docker exec -it ${CONTAINER} bash
```

To allow SCHED_FIFO inside a container:

```
sudo docker run --privileged ${IMAGE}
```

To let a container see host network:

```
sudo docker run --net host ${IMAGE}
```

To use hugepages inside a container:

```
sudo docker run -v /mnt/huge:/mnt/huge ${IMAGE}
```

To login as non-root user:

```
sudo docker run -u ${USER}:${USER} ${IMAGE}
```

To clean all existing containers and caches:

```
sudo docker system prune
```

## Real Example

```
sudo docker run -it --privileged --net host --u ${USER}:${USER} -v /opt/intel:/opt/intel -v /mnt/huge:/mnt/huge ${IMAGE}
```

## References
Reference documentation|Docker Documentation([https://docs.docker.com/reference/](https://docs.docker.com/reference/))