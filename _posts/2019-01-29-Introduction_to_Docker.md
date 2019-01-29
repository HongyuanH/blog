---
layout:            post
title:             "Introduction to Docker"
date:              2019-01-29
tags:              Docker
category:          Docker
author:            hongyuan

---

## What is Docker?

From Wikipedia:

> Docker is a computer program that performs operating-system-level virtualization, also known as "containerization". It was first released in 2013 and is developed by Docker, Inc. Docker is used to run software packages called "containers". Containers are isolated from each other and bundle their own application, tools, libraries and configuration files; they can communicate with each other through well-defined channels.

<figure markdown="1">
  ![]({{ site.github.url }}/res/2019-01-29-Introduction_to_Docker/docker.png#middle)
  <figcaption markdown="1"> source: [https://www.docker.com/](https://www.docker.com/)
  </figcaption>
</figure>

## Container vs. Virtual Machine (VM)

Docker container and VM are both virtualisation technologies. While VM provides an entire isolated OS with its own allocated physical resoureces (e.g. CPU and memories), container shares the same OS/kernel and physical resources as host and provides only process-level isolation (e.g. binaries/libraries) for applications:

<figure markdown="1">
  ![]({{ site.github.url }}/res/2019-01-29-Introduction_to_Docker/container_vs_vm.png#middle)
  <figcaption markdown="1"> source: [https://docs.docker.com/](https://docs.docker.com/)
  </figcaption>
</figure>

Pros and cons for container and VM:

| Container | VM |
|--------|--------|
| Lightweight (MB/KB) | Heavyweight (GB) |
| Native performance | Limited performance |
| Fast to start (milliseconds) | Slow to start (minutes/seconds) |
| Process-level isolation only | Fully isolated |

## Docker Architecture

Major components of Docker:

* **Client**: command line tool to interact with Docker daemon
* **Daemon**: runs on the host machine, executes commands from Client and manages objects like images and containers
* **Registry**: stores Docker images (e.g. [Docker Hub](https://hub.docker.com/)) 

<figure markdown="1">
  ![]({{ site.github.url }}/res/2019-01-29-Introduction_to_Docker/docker_arch.png#middle)
  <figcaption markdown="1"> source: [https://docs.docker.com/](https://docs.docker.com/)
  </figcaption>
</figure>

## Docker Image & Container

* **Image**:  a read-only template with instructions for creating a Docker container, can be saved as a .tar file

* **Container**: a runnable instance of an image

<figure markdown="1">
  ![]({{ site.github.url }}/res/2019-01-29-Introduction_to_Docker/image_and_container.png#middle)
</figure>

## References
[1] Docker (software) - Wikipedia ([https://en.wikipedia.org/wiki/Docker_(software)](https://en.wikipedia.org/wiki/Docker_(software)))  
[2] Docker Docs - Get started with Docker ([https://docs.docker.com/get-started/](https://docs.docker.com/get-started/))  
[3] Docker Docs - Docker overview ([https://docs.docker.com/engine/docker-overview/](https://docs.docker.com/engine/docker-overview/))