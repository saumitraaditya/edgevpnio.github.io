---
permalink: /edgevpninabox/
title: "EdgeVPN.io in-a-box"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

This tutorial guides you through a quick demo deployment including a preconfigured OpenFire XMPP server and a couple of EdgeVPN.io nodes using Docker.

**Prerequisite:** This demo assumes you are running Ubuntu 18.04. This has been tested on the Amazon Ubuntu Server 18.04 LTS AMI, and on the [OSboxes Ubuntu Server 18.04.3 Bionic image](https://www.osboxes.org/ubuntu-server/)

### Setup your environment

Clone the evio/tools repository (which has several tools and template configuration file needed for this demo) and set up your dependence pre-requisites as follows:

```
git clone http://github.com/edgevpnio/tools
cd tools
./setup testbed
./setup venv
```

### Setup a Docker network

**Log out and log back in** to ensure your user is able to use Docker, then run the following command to create a Docker network:

```
docker network create dkrnet
```

### Deploy demo XMPP server

This will download and run a pre-configured XMPP server Docker container - note that it may take a while to download.

```
cd tools
./setup xmpp
```

### Add IP address of XMPP server to template file

Edit the template-config.json file in the testbed directory, replacing _*.*.*.*_ in _HostAddress_ with the IP address of your host (the XMPP container maps port 5222 of your host)

```
cd testbed
vi template-config.json
```

### Deploy evio nodes

The following commands enter a Python venv and configure and run two containers named evio-dkr001 and evio-dkr002 (note again that it may take a while to download):

```
source venv/bin/activate
python testbed.py -v --configure --range=1,3 --run
```

### Test

You may now open a shell in the first container, and ping the second:

```
docker exec -it evio-dkr001 /bin/bash
ping 10.10.100.2
```

Or vice-versa:

```
docker exec -it evio-dkr002 /bin/bash
ping 10.10.100.1
```


