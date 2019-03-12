---
layout:            post
title:             "Install Docker on Ubuntu"
date:              2019-03-12
tags:              Docker
category:          Docker
author:            hongyuan

---

## Just a Script

Copy-and-paste the below script to install Docker CE (tested on Ubuntu 14.04 and 16.04):

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

#Add Docker’s official GPG key:
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

#Set up the stable repository:
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update
sudo apt-get install -y docker-ce
```


## References
[https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)
