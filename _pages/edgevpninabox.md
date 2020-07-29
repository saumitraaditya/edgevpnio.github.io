---
permalink: /edgevpninabox/
title: "EdgeVPN.io in-a-box"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

This tutorial guides you through a quick deployment of an OpenFire XMPP server and a couple of nodes using Docker.

You need at least one Linux computer with Docker and Open vSwitch installed

## Install dependences

Make sure you have Docker installed in your system. 

You may [follow a guide on how to install Docker for your system](https://docs.docker.com/engine/install/ubuntu/), or you may follow the instructions below to install Docker (and other dependences) if you are using Ubuntu 18.04:

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

## Deploy pre-configured demo XMPP server

We have created a Docker container pre-configured with the Openfire XMPP server and user test1, password password_test1 for this tutorial. The OpenFire admin password for this container is *edgevpn_demo*. Deploy the XMPP server with this command:

```
docker run --name openfire -d -p 9090:9090 -p 5222:5222 -p 5269:5269 -p 5223:5223 -p 7443:7443 -p 7777:7777 -p 7070:7070 -p 5229:5229 -p 5275:5275 edgevpnio/openfire_edgevpn_demo
```

## Deploy two EdgeVPN.io nodes 

Now deploy two nodes by following the [instructions on deploying EdgeVPN.io Docker containers](/dockeredgevpn). 

You may deploy the two nodes in the same host, or in different hosts. Make sure to replace A.B.C.D with the IP address of the computer where you deployed the XMPP server in the previous step (the XMPP user names and passwords are configured to with with this tutorial).
