---
permalink: /dockeredgevpn/
title: "Deploy EdgeVPN with Docker"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

This tutorial guides you through the process of deploying two EdgeVPN nodes using Docker - it is intended to be the first-time guide for users getting started with EdgeVPN. 

## Dependences

The tutorial assumes you have installed Docker, and you an XMPP server up and running, with user accounts and a group setup, [as described in the tutorial on deploying an XMPP server using Docker](Deploy-a-test-XMPP-server-with-Docker)

## Configure host network and directories

First, you need to create a network namespace in the host:

```
sudo docker network create dkrnet
```

Now, create directories in the host to hold configuration files and logs for your containers:

```
cd
mkdir edgevpn
mkdir edgevpn/config
mkdir edgevpn/logs
mkdir edgevpn/logs/edgevpn001
mkdir edgevpn/logs/edgevpn002
```

## Setup configuration files

First, let's create a configuration file for a container for XMPP user test1.

Copy and save this as /home/$USER/edgevpn/config/config-001.json (the directory you created in the previous step) - make sure you replace A.B.C.D with the IP address of your host:

```json
{
  "CFx": {
    "Model": "Default",
    "Overlays": [ "1010001" ]
  },
  "Logger": {
    "LogLevel": "INFO",
    "Device": "File",
    "Directory": "/var/log/ipop-vpn/",
    "CtrlLogFileName": "ctrl.log",
    "TincanLogFileName": "tincan_log",
    "MaxFileSize": 5000000,
    "MaxArchives": 5
  
  },
  "Signal": {
    "Enabled": true,
    "Overlays": {
      "1010001": {
        "HostAddress": "A.B.C.D",
        "Port": "5222",
        "Username": "test1@openfire.local",
        "Password": "password_test1",
        "AuthenticationMethod": "PASSWORD"
      }
    }
  },
  "Topology": {
    "PeerDiscoveryCoalesce": 3,
    "Overlays": {
      "1010001": {
        "Name": "Overlay1",
        "Description": "Test overlay",
        "MaxSuccessors": 2,
        "MaxOnDemandEdges": 1,
        "MaxConcurrentEdgeSetup": 5,
        "Role": "Switch"
      }
    }
  },
  "LinkManager": {
    "Dependencies": [ "Logger", "TincanInterface", "Signal" ],
    "Stun": ["stun.l.google.com:19302", "stun1.l.google.com:19302"],
    "Overlays": {
      "1010001": {
        "Type": "TUNNEL",
        "TapName": "tnl"
      }
    }
  },
  "BridgeController": {
    "Overlays": {
      "1010001": {
        "Type": "LXBR",
        "BridgeName": "ipopbr",
        "IP4": "10.10.10.21",
        "PrefixLen": 24,
        "MTU": 1410,
        "STP": true,
        "AutoDelete": true
      }
    }
  }
}
```

To configure the second container, copy config-001.json to config-002.json, **and replace the following entries in the json file**. These entries reflect the different user ID, password, and EdgeVPN IP address:

```
        "Username": "test2@openfire.local",
        "Password": "password_test2",
...
        "IP4": "10.10.10.22",
```

## Start the EdgeVPN containers

Now you will run two containers, named edgevpn001 and edgevpn002, mapping the different configuration file and the log directories to different mount points:

```
docker run -d -v /home/$USER/edgevpn/config/config-001.json:/etc/opt/ipop-vpn/config.json -v /home/$USER/edgevpn/logs/edgevpn001:/var/log/ipop-vpn/ -v --rm --privileged --name edgevpn001 --network dkrnet ipopproject/ipop-vpn:1.0 /sbin/init

docker run -d -v /home/$USER/edgevpn/config/config-002.json:/etc/opt/ipop-vpn/config.json -v /home/$USER/edgevpn/logs/edgevpn002:/var/log/ipop-vpn/ -v --rm --privileged --name edgevpn002 --network dkrnet ipopproject/ipop-vpn:1.0 /sbin/init
```

## Test your connection

Log into the container edgevpn001 (virtual IP address 10.10.10.21), and ping the edgevpn002 node (virtual IP 10.10.10.22):

```
docker exec -it /bin/bash
# ping 10.10.10.22
```


