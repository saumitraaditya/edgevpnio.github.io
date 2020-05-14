---
permalink: /configbasics/
title: "EdgeVPN basic configuration"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

The EdgeVPN package is released with a sample configuration file that serves as a good starting point for many use cases. This document describes basic configuration parameters that you need to configure for your deployment, as well as the typical parameters you might want to tweak for your deployment. [Please refer to this document for a full description of configuration parameteres](/configfile)

# Configure your XMPP server endpoint and user credentials

This is a required configuration for your deployment - you must setup every EdgeVPN node to connect to an XMPP server. This is part of the _Signal_ module, and includes the IP address and port (typically 5222) of the server. The simplest approach uses password-based authentication, where you must add the username and password:


```
  "Signal": {
    "Enabled": true,
    "Overlays": {
      "101000F": {
        "HostAddress": "A.B.C.B",
        "Port": "5222",
        "Username": "user@xmppsite.com",
        "Password": "password",
        "AuthenticationMethod": "PASSWORD"
      }
    }
  },
```

Certificate-based authentication requires additional steps to [create a CA, sign certificates, and configure the server](/openfireconfig). As far as EdgeVPN configuration goes, in the _Signal_ module you must specify the user's certificate and private key files. Note that the typical port for certificate-based authentication is 5223, rathar than 5222:

```
  "Signal": {
    "Enabled": true,
    "Overlays": {
      "101000F": {
        "HostAddress": "A.B.C.B",
        "Port": "5223",
        "AuthenticationMethod": "x509",
        "Username": "user@xmppsite.com",
        "CertDirectory": "/home/user/edgevpn/cacerts/",
        "CertFile": "edgevpn.crt",
        "KeyFile": "edgevpn.key"
      }
    }
  },
```

# Configure your bridge 

When you deploy an EdgeVPN node, it creates an SDN Open vSwitch (OVS) in your computer; ports of this OVS are like ports of an Ethernet switch, and WebRTC TinCan links are like virtual cables that terminate on these ports through a tap interface. There are two types of deployment you may consider. 

## Bridge with IP address

The first type of deployment "patches" the OVS switch to a Linux bridge that is set up with its own IPv4 address. This is useful, for instance, when the EdgeVPN node is a single node (physical machine, VM, container) that is added to the network. The configuration is as follows:

An example of the console output of _ip address_ of how such a deployment would look like in a node is pasted below. In this example:

* lo is the loopback interface
* eth0 is the main network interface
* ovs-system is the Open vSwitch
* edgebr101000F is the EdgeVPN bridge (the name is a concatenation of the prefix and overlay ID from the configuration)
* brl10100F is the bridge configured with an IP address
* tnl-* are the tap devices from which EdgeVPN packets are picked/injected from/into the virtual network

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a2:a0:50:bf:79:d0 brd ff:ff:ff:ff:ff:ff
3: edgebr101000F: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether d2:35:42:50:e9:42 brd ff:ff:ff:ff:ff:ff
4: brl101000F: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether de:40:a2:aa:aa:4a brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.1/16 scope global brl101000F
       valid_lft forever preferred_lft forever
5: tnl-a100001: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc fq_codel master ovs-system state UNKNOWN group default qlen 1000
    link/ether b6:39:18:2e:60:87 brd ff:ff:ff:ff:ff:ff
8: tnl-a100008: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1410 qdisc fq_codel master ovs-system state UNKNOWN group default qlen 1000
    link/ether 9e:68:ea:74:8a:ec brd ff:ff:ff:ff:ff:ff
22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.2/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

For this deployment, you need to configure the IP4 address of the node, and PrefixLen to match the subnet your EdgeVPN virtual network uses. In the example below, we configure a 16-bit subnet, and IP4 address 10.10.10.1 for Linux bridge _edgebr_ :

```
  "BridgeController": {
    "Dependencies": [
      "Logger",
      "LinkManager"
    ],
    "SdnListenAddress": "",
    "SdnListenPort": 5802,
    "Overlays": {
      "101000F": {
        "Type": "OVS",
        "BridgeName": "edgebr",
        "IP4": "10.10.10.1",
        "PrefixLen": 16,
        "MTU": 1410,
        "AutoDelete": true,
        "SDNController": {
          "ConnectionType": "tcp",
          "HostName": "127.0.0.1",
          "Port": "6633"
        }
      }
    }
  }
```

## Bridge with no IP address

This configuration is currently not supported, and will be added in a later release

# Configure NAT traversal

EdgerVPN requires at least one STUN server in order to traverse the most common types of [NATs - the "cone" type (full, address-, or port-restricted)](https://en.wikipedia.org/wiki/Network_address_translation). If EdgeVPN nodes are behind symmetric NATs, you will also need a TURN server.

If you use STUN only, you will be able to use existing, freely-available STUN servers on the Internet (see example below), or deploy your own STUN server(s). If you plan to use TURN as well, you need to either deploy and manage your own TURN server, or use a TURN service - commercial TURN-as-a-service options exist, e.g. [Xirsys](http://www.xirsys.com). [This document provides information on how to deploy STUN/TURN services](/stunturn)

The setup in the configuration file is simple. An example with STUN only, configured with a list of two freely-available Google STUN servers (you may add your own STUN servers to this list if you wish):

```
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
      "101000F": {
        "Type": "TUNNEL",
        "TapName": "tnl-"
      }
    }
  },
```

An example with a TURN server added (in this example, a TURN server hosted by Xirsys - substitute username and password as appropriate):

```
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
    "Turn": [{
      "Address": "w2.xirsys.com:80",
      "User": "your-user-id-string",
      "Password": "your-password-string"
     }],
    "Overlays": {
      "101000F": {
        "Type": "TUNNEL",
        "TapName": "tnl-"
      }
    }
  },
```


