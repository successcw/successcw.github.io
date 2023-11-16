---
layout: post
title: How to use docker on ubuntu
categories: misc
tags: [misc]
---

Docker is a virtual environment that I can use it to test my software isolated from host OS. It's a popular container technology.

# Install docker
```Bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release

#Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
If something is error, please use following script to install it automatically.
```
wget https://get.docker.com get_docker.sh
./get_docker.sh
```

# Create container ubuntu20.04 in docker
Create a ubuntu20.04 container with:
* Timezone: Beijing
* name: bruce_test
* mount: /path/to/host to /path/to/container
* interactive terminal
* bash shell  

```Bash
sudo docker create -e TZ=Asia/Beijing --name bruce_test --mount type=bind,source=/path/to/host,target=/path/to/container -it --entrypoint "/bin/bash" ubuntu:20.04  
```
If it's the first time to create ubuntu:20.04, will download ubuntu:20.04 from docker hub  
Check if mount correctly: sudo docker inspect bruce_test
```Bash
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/path/to/host",
                "Destination": "/path/to/container",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ]
```

# Start exist container
1. Check exist container  
sudo docker ps -a
2. Start container bruce_test  
sudo docker start bruce_test
3. Attach to bruce_test   
sudo docker exec -it bruce_test bash

# Delete container
sudo docker rm bruce_test  
If container is running, need stop first  
sudo docker stop bruce_test

# Commit changes
If you want to publish your docker container, use following:  
```Bash
sudo docker commit -m "test change" bruce_test ubuntu_bbc
```
Show images: sudo docker images
```Bash
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
ubuntu_bbc    latest    ae44cac598e5   14 seconds ago   72.8MB
ubuntu        20.04     ba6acccedd29   6 days ago       72.8MB
hello-world   latest    feb5d9fea6a5   4 weeks ago      13.3kB
```

# Reference
1. [how-to-install-and-use-docker-on-ubuntu-20-04][1]

    [1]: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04

