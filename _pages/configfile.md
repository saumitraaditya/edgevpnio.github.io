---
permalink: /configfile/
title: "EdgeVPN configuration file"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

Each EdgeVPN node uses a JSON configuration file, with contents as described below. Each section describes one of the main configuration file's modules, with snippets of JSON. At the end of this document, a complete configuration file example is shown.

## CFx module

This module configures unique identifiers for the overlay and the node. Currently, only an EdgeVPN node can be bound to a single overlay ID.

*TODO* What does "Model" mean? Are the ID formats correct below?

* _Overlays_ specifies the overlay ID, a 28-bit number encoded in hexadecimal format

* _NodeId_ specifies the node's unique ID. NodeId is a 128-bit number also in hexadecimal format. The example below shows an overlay with ID "101000F" and NodeID "a100001feb6040628e5fb7e70b04f001"

```
  "CFx": {
    "Model": "Default",
    "Overlays": [
      "101000F"
    ],
    "NodeId": "a100001feb6040628e5fb7e70b04f001"
  },
```

## Logger module

This module specifies logging configuration.

*TODO* any other logging levels? 
*TODO* What is MaxArchives?

* _LogLevel_ specifies the desired level of logging: DEBUG (most verbose logging), WARN (less verbose, logs warnings), INFO (least verbose)

* _Device_ specifies the output device for logging. Currently, "File" is supported

* _Directory_ specifies the directory where logs are to be stored

* _CtrlLogFileName_ specifies the name of the log file for the EdgeVPN controller

* _TincanLogFileName_ specifies the name of the log file for the tincan WebRTC tunnel datapath

* _MaxFileSize_ specifies the maximum size for log files

* _MaxArchives_ specifies

```
  "Logger": {
    "LogLevel": "DEBUG",
    "Device": "File",
    "Directory": "/var/log/edgevpn/",
    "CtrlLogFileName": "ctrl.log",
    "TincanLogFileName": "tincan_log",
    "MaxFileSize": 10000000,
    "MaxArchives": 1
  },
```

## Signal module

This model specifies how to connect to XMPP services to establish a signaling channel for creating tunnels

*TODO* x509 configuration options

* _Enabled_ should be set to true

* _Overlays_ specifies each overlay to be configured. This needs to match overlays described in the CFx module (in the example below, "101000F")

* _HostAddress_ specifies the IP address or host name of the XMPP server

* _Port_ specifies the port to connect to the MXPP server. This is usually 5222 for password-based authentication, and 5223 for certificate-based authentication

* _AuthenticationMethod_ specifies the method for authenticating the user with the XMPP server

* _Username_ specifies the name of the XMPP user

### For password-based authentication: _AuthenticationMethod_ is PASSWORD

* _Password_ specifies the user's password

Example:

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

### For certificate-based authentication: _AuthenticationMethod_ is x509

* _CertDirectory_ specifies the directory where certificate and key are stored

* _CertFile_ specifies the name of the file storing the user's certificate

* _Keyfile_ specifies the name of the file storing the user's private key

Example:

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
        "Keyfile": "edgevpn.key"
      }
    }
  },
```

## Topology module

*TODO* Need Ken's input

```
  "Topology": {
    "PeerDiscoveryCoalesce": 4,
    "Overlays": {
      "101000F": {
        "Name": "SymphonyRing",
        "Description": "Scalable Symphony Ring Overlay for Bounded Flooding.",
        "MaxSuccessors": 2,
        "MaxConcurrentEdgeSetup": 5,
        "Role": "Switch"
      }
    }
  },
```

## LinkManager module

*TODO* Need Ken's input

*TODO* add TURN

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

## OverlayVisualizer module

This module configures information for an (optional) overlay visualizer service

* _Enabled_ specifies whether the visualizer is enabled (true) or not (false)

* _TimerInterval_ specifies the interval, in seconds, to send updates

* _WebServiceAddress_ specifies the IP:port endpoint of the visualizer's Web service

* _GeoCoordinate_ specifies the geographical coordinates (lat,lon) of this EdgeVPN node for display in the user interface

* _NodeName_ specifies the name of this EdgeVPN node for display in the user interface

```
  "OverlayVisualizer": {
    "Enabled": false,
    "TimerInterval": 25,
    "WebServiceAddress": "192.168.0.42:5000",
    "GeoCoordinate": "14.073791,100.606308",
    "NodeName": "nd-001"
  },
```
## BridgeController module

This module configures the software switch/bridge used by EdgeVPN

*TODO* Why/what are dependencies?

*TODO* need Ken's input

Example:

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
        "BridgeName": "ipopbr",
        "IP4": "10.10.10.1",
        "PrefixLen": 16,
        "MTU": 1410,
        "STP": false,
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
