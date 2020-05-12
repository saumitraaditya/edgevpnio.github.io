---
permalink: /openfiredocker/
title: "Deploy a test XMPP server with Docker"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

EdgeVPN relies on XMPP services to bootstrap nodes into the VPN. This how-to guides you through the process of deploying an Openfire XMPP server using Docker. 

**Note**: this how-to deploys Openfire with an embedded database in a Docker container and is meant to be used as a starting point for testing - not to host a persistent MySQL and XMPP server. There are several options to host persistent Openfire services on cloud providers, such as https://bitnami.com/stack/openfire

### Step 1: Install Docker

Make sure you have Docker installed in your system. 

You may [follow a guide on how to install Docker for your system](https://docs.docker.com/engine/install/ubuntu/), or you may follow the instructions below to install Docker (and other EdgeVPN dependences) if you are using Ubuntu 18.04:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt-get update -y
sudo apt-get install -y openvswitch-switch \
                        python3 python3-pip python3-venv \
                        apt-transport-https \
                        ca-certificates \
                        curl git \
                        software-properties-common \
                        containerd.io \
                        docker-ce-cli \
                        docker-ce 
groupadd -f docker
sudo usermod -a -G docker $USER
```

### Step 2: Run Openfire Docker container

We will use the Docker container from quantumobject:

```
docker run --name openfire -d -p 9090:9090 -p 5222:5222 -p 5269:5269 -p 5223:5223 -p 7443:7443 -p 7777:7777 -p 7070:7070 -p 5229:5229 -p 5275:5275 quantumobject/docker-openfire
```

### Step 3: Configure Openfire

1. Point your browser to A.B.C.D:9090, where A.B.C.D is the IP address of the Docker host

2. Select your language, continue

3. For both XMPP domain and server host name, use openfire.local

4. Select “Embedded database” and continue

5. Select “default” for profile settings

6. Enter your email address, and a password for the admin

7. Login with user “admin”, and the password you just created

### Step 4: Let’s add test users and group them

You will now use the Web admin interface to create two test users and assign them to a group (i.e. they will join the same EdgeVPN) 

Start by creating the two users:

1. Navigate the user interface to Users/Groups -> Users -> Create New User
2. Select username and password (We’ll use password_test1 and password_test2 for the user passwords in the examples below; you may leave name and email blank)
3. Repeat for test2

Then, create a group:

1. Navigate the user interface to Users/Groups -> Groups -> Create New Group, and create grouptest1
2. Select “Enable contact list group sharing”, and share group with “Users of the same group”
3. Scroll down the interface to add users test1 and test2 to this group

### Step 5: Configure EdgeVPN

[Follow the instructions on how to install EdgeVPN using Docker](Deploy-EdgeVPN-with-Docker) to setup two containers for users test1 and test2
