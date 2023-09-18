# Containerization
Building fresh images of a device and then doing something with it and then tearing it down is what we're all about over here. You need an install script to get Docker working on an Ubuntu host: 
```
#!/bin/bash

# Preparation
sudo apt update -y
sudo apt install ca-certificates curl gnupg lsb-release -y
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt update -y
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Add user htb-student to the Docker group
sudo usermod -aG docker htb-student
echo '[!] You need to log out and log back in for the group changes to take effect.'

# Test Docker installation
docker run hello-world
```

Then you need an image file so Docker knows how to build your default images. You can build your own images or pull them from the Docker Hub. Here's an example dockerfile that creates an image, runs a HTTP server and lets you SSH into it: 

```
# Use the latest Ubuntu 22.04 LTS as the base image
FROM ubuntu:22.04

# Update the package repository and install the required packages
RUN apt-get update && \
    apt-get install -y \
        apache2 \
        openssh-server \
        && \
    rm -rf /var/lib/apt/lists/*

# Create a new user called "student"
RUN useradd -m docker-user && \
    echo "docker-user:password" | chpasswd

# Give the htb-student user full access to the Apache and SSH services
RUN chown -R docker-user:docker-user /var/www/html && \
    chown -R docker-user:docker-user /var/run/apache2 && \
    chown -R docker-user:docker-user /var/log/apache2 && \
    chown -R docker-user:docker-user /var/lock/apache2 && \
    usermod -aG sudo docker-user && \
    echo "docker-user ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Expose the required ports
EXPOSE 22 80

# Start the SSH and Apache services
CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```
Then run `docker build -t LABEL` and give it a good name so you know what you're doing. Since the above image is only going to utilize ports 80/22, we have to forward them when we run the image:
`docker run -p 8022:22 -p 8080:80 -d LABEL`

## Commands
| Command | Usage |
| :----: | :----: |
| `docker ps` | Lists all running containers |
| `docker stop` | stops a container |
| `docker start` | starts a container |
| `docker restart` | you get it |
| `docker rm/rmi` | remove container/image |
| `docker logs` | view container logs |

You can also use a utility known as `lxc` to containerize things. 
