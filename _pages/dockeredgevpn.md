---
permalink: /dockeredgevpn/
title: "Deploy EdgeVPN.io with Docker"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

This tutorial guides you through the process of deploying two nodes using Docker - it is intended to be the first-time guide for users getting started with EdgeVPN.io.

## Dependences

The tutorial assumes you have installed Docker, and you an XMPP server up and running, with user accounts and a group setup, [as described in the tutorial on deploying an XMPP server using Docker](/openfiredocker)

## Configure host network and directories

First, you need to install Open vSwitch and create a network namespace in the Docker host:

```
sudo apt-get install -y openvswitch-switch
sudo docker network create dkrnet
```

Now, create directories in the host to hold configuration files and logs for your containers:

```
cd
mkdir edgevpnio
mkdir edgevpnio/config
mkdir edgevpnio/logs
mkdir edgevpnio/logs/001
mkdir edgevpnio/logs/002
```

## Setup configuration files

First, let's create a configuration file for a container for XMPP user test1.

Copy and save this as /home/$USER/edgevpn/config/config-001.json (the directory you created in the previous step) - *make sure you replace A.B.C.D with the IP address of your XMPP host:*

```json
{
  "CFx": {
    "Model": "Default",
    "Overlays": [
      "1234567"
    ]
  },
  "Logger": {
    "LogLevel": "DEBUG",
    "Device": "File",
    "Directory": "/var/log/edge-vpnio/",
    "CtrlLogFileName": "ctrl.log",
    "TincanLogFileName": "tincan_log",
    "MaxFileSize": 10000000,
    "MaxArchives": 1
  },
  "Signal": {
    "Enabled": true,
    "Overlays": {
      "1234567": {
        "HostAddress": "A.B.C.D",
        "Port": "5222",
        "Username": "test1@openfire.local",
        "Password": "password_test1",
        "AuthenticationMethod": "PASSWORD"
      }
    }
  },
  "Topology": {
    "PeerDiscoveryCoalesce": 1,
    "Overlays": {
      "1234567": {
        "Name": "SymphonyRing",
        "Description": "Scalable Symphony Ring Overlay for Bounded Flooding.",
        "MaxSuccessors": 2,
        "MaxOnDemandEdges": 1,
        "MaxConcurrentEdgeSetup": 5,
        "Role": "Switch"
     }
    }
  },
  "LinkManager": {
    "Dependencies": [
      "Logger",
      "TincanInterface",
      "Signal"
    ],
    "Stun": [
      "stun.l.google.com:19302",
      "stun1.l.google.com:19302"
    ],
    "Overlays": {
      "1234567": {
        "Type": "TUNNEL",
        "TapName": "tnl-"
      }
    }
  },
  "OverlayVisualizer": {
    "Enabled": false,
    "TimerInterval": 25,
    "WebServiceAddress": "",
    "NodeName": "n1"
  },
  "BridgeController": {
    "Dependencies": [
      "Logger",
      "LinkManager"
    ],
    "BoundedFlood": {
      "OverlayId": "1234567",
      "LogDir": "/var/log/edge-vpn/",
      "LogFilename": "bf.log",
      "LogLevel": "INFO",
      "BridgeName": "edgbr",
      "DemandThreshold": "100M",
      "FlowIdleTimeout": 60,
      "FlowHardTimeout": 60,
      "MulticastBroadcastInterval": 60,
      "MaxBytes": 10000000,
      "BackupCount": 0,
      "ProxyListenAddress": "",
      "ProxyListenPort": 5802,
      "MonitorInterval": 60,
      "MaxOnDemandEdges": 0
    },
    "Overlays": {
      "1234567": {
        "NetDevice": {
          "AutoDelete": true,
          "Type": "OVS",
          "SwitchProtocol": "BF",
          "NamePrefix": "edgbr",
          "MTU": 1410,
          "AppBridge": {
            "AutoDelete": true,
            "Type": "OVS",
            "NamePrefix": "brl",
            "IP4": "10.10.10.21",
            "PrefixLen": 24,
            "MTU": 1410
          }
        },
        "SDNController": {
          "ConnectionType": "tcp",
          "HostName": "127.0.0.1",
          "Port": "6633"
        }
      }
    }
  }
}      
```

To configure the second container, copy config-001.json to config-002.json, **and replace the following entries in the json file**. These entries reflect the different user ID, password, and EdgeVPN.io IP address:

```
        "Username": "test2@openfire.local",
        "Password": "password_test2",
...
        "IP4": "10.10.10.22",
```

## Start the containers

Now you will run two containers, named edgevpn001 and edgevpn002, mapping the different configuration file and the log directories to different mount points:

```
docker run -d -v /home/$USER/edgevpn/config/config-001.json:/etc/opt/edge-vpnio/config.json -v /home/$USER/edgevpn/logs/001:/var/log/edge-vpnio/ --rm --privileged --name edgevpnio001 --network dkrnet edgevpnio/evio-node:20.7 /sbin/init

docker run -d -v /home/$USER/edgevpn/config/config-002.json:/etc/opt/edge-vpnio/config.json -v /home/$USER/edgevpn/logs/002:/var/log/edge-vpnio/ --rm --privileged --name edgevpnio002 --network dkrnet edgevpnio/evio-node:20.7 /sbin/init
```

## Test your connection

You can open a shell into the container edgevpn001 (virtual IP address 10.10.10.21), and ping the edgevpn002 node (virtual IP 10.10.10.22):

```
docker exec -it edgevpnio001 /bin/bash
# ping 10.10.10.22
```

Or, the other way around:

```
docker exec -it edgevpnio002 /bin/bash
# ping 10.10.10.21
```

