---
permalink: /configfile/
title: "EdgeVPN configuration file"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

The EdgeVPN.io configuration file uses the JSON format, and specifies the parameterized options for the various  modules that make up the controller. A valid config.json file is required to run the software, with contents as described below. Each section describes one of the main configuration file's modules, with snippets of JSON. At the end of this document, a complete configuration file example is shown.

## CFx module

This module is used to configure the overall controller framework. It configures unique identifiers for the overlay and the node. Currently, a node can be bound to a single overlay ID

* _Model_ specifies a mnemonic to describe customized IPOP controllers. You can develop and add your own modules, or remove or replace existing modules. For the vast majority of uses, set it to "Default" 

* _Overlays_ specifies the overlay ID, a 28-bit number encoded in hexadecimal format. *Note: currently, EdgeVPN.io only supports a single overlay*

* _NodeId_ specifies the node's unique ID. NodeId is a 128-bit number also in hexadecimal format. You may specify a unique ID, or, if left blank, the framework will generate a random ID. The example below shows an overlay with ID "101000F" and NodeID "a100001feb6040628e5fb7e70b04f001"

* _NidFileName_ specifies a fully qualified file name for storing a framework-generated NodeId

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

The controller logger module. Used by all other modules for logging. Supports disk file and console streams.

* _LogLevel_ specifies the desired level of logging. Must be one of (in order of verbosity): NONE, ERROR, WARNING, INFO, or DEBUG

* _ConsoleLevel_ specifies a separate logging level to be used for the console; applies when *Device* is set to All

* _Device_ specifies the output stream for logging. Supported values: File, Console, All

* _Directory_ specifies the directory where logs are to be stored

* _CtrlLogFileName_ specifies the name of the log file for the controller

* _TincanLogFileName_ specifies the name of the log file for the TinCan WebRTC tunnel datapath

* _MaxFileSize_ specifies the maximum size for individual log files

* _MaxArchives_ specifies the number of log files to archive before overwriting

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

## TincanInterface module

This module configures parameters relevant to the connection between the controller and the TinCan module. These are not needed to be modified from defaults for the vast majority of use cases.

* _MaxReadSize_ specifies the maximum buffer size for Tincan Messages

* _SocketReadWaitTime_ specifies the socket read wait time for Tincan Messages

* _RcvServiceAddress_ specifies the controller server IPv4 address

* _SndServiceAddress_ specifies the Tincan server IPv4 address

* _RcvServiceAddress6_ specifies the controller server IPv6 address

* _SndServiceAddress6_ specifies the Tincan server IPv6 address

* _CtrlRecvPort_ specifies the controller listening port

* _CtrlSendPort_ specifies the Tincan listening port

## Signal module

This model specifies how to connect to XMPP services to establish a signaling channel for bootstrapping the creation of tunnels

* _Enabled_ should be set to true

* _CacheExpiry_ specifies the minimum duration (in seconds) that an entry remains in the NodeID -> JID mapping cache

* _Overlays_ specifies each overlay to be configured. This needs to match overlays described in the CFx module (in the example below, "101000F")

* _HostAddress_ specifies the IP address or host name of the XMPP server

* _Port_ specifies the port to connect to the MXPP server. This is usually 5222 for password-based authentication, and 5223 for certificate-based authentication

* _AuthenticationMethod_ specifies the method for authenticating the user with the XMPP server. Possible values: PASSWORD, x509

* _Username_ specifies the name of the XMPP user

### For password-based authentication: _AuthenticationMethod_ is PASSWORD

* _Password_ specifies the user's password

Example:

```
  "Signal": {
    "Enabled": true,
    "Overlays": {
      "101000F": {
        "HostAddress": "A.B.C.D",
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

* _KeyFile_ specifies the name of the file storing the user's private key

**Note: the port typically used for x509-based auth is 5223, not 5222**

Example:

```
  "Signal": {
    "Enabled": true,
    "Overlays": {
      "101000F": {
        "HostAddress": "A.B.C.D",
        "Port": "5223",
        "AuthenticationMethod": "x509",
        "CertDirectory": "/home/user/edgevpn/cacerts/",
        "CertFile": "edgevpn.crt",
        "KeyFile": "edgevpn.key"
      }
    }
  },
```

## Topology module

Module that defines and enforces the overlay's topology. Currently, EdgeVPN.io supports a Symphony-based structured peer-to-peer topology for its tunnels

* _Overlays_ specifies a configuration for each overlay being managed by this controller. Each overlay is specified by its UUID - a hexadecimal value matching one in the CFx Overlays list (see above). *Note*: currently, the system supports only a single overlay. 

* _Name_ a mnemonic string to name the overlay

* _Description_ a description string of the overlay

* _EnforcedEdges_ specifies a list of NodeIds for which that a tunnel should alwayes be created to. Optional for most deployments, as the topology manages links according to its own policies, but can be used if you would like to manually create links. Example: [ “1234..5”, 1234..6”, 1234..7” ]

* _ManualTopology_ specifies if the topology module should only create EnforcedEdges. Values: true or false (default)

* _PeerDiscoveryCoalesce_ specifies the number of new peer notifications to wait on before attempting to update the overlay edges. If this threshold is not reached, the overlay will be refreshed on its periodic TimerInterval

* _MaxSuccessors_ specifies the maximum number of successors links to create in the Symphony topology. These are the outgoing links that connect to the neighbors "to the right" in the overlay’s ring. Typically, values in the 2-4 range are sufficient. Larger values improve fault tolerance, but also generate more tunnel maintenance overhead

* _MaxLongDistEdges_ specifies the maximum number of outgoing long distance Symphony edges to initiate

* _MaxConcurrentEdgeSetup_ specifies the maximum number of edges to be created concurrently. This can help prevent a "stampede" effect when joining an existing large overlay

* _Role_ specifies whether this one acts as a full virtual switch node in an overlay (*Switch*), or as a *Leaf* node that joins the overlay by connecting to a Switch node. In *Switch* role, a node will perform switching operations and accept incoming connection requests to create edges. In *Leaf* role, a node is a client/edge device that must connect to a Switch node. *Leaf* nodes reject any incoming requests for an edge.

Example:

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

This module creates and manages the WebRTC based tunnels, which are the overlay edges between peers

* _Stun_ specifies a list of one or more STUN servers for NAT traversal. A deployment needs at least one STUN server configured in order to support NAT traversal. STUN server endpoints are specifies in the format address:port

* _Turn_ specifies a list of dictionaries, which specify the TURN server(s) and corresponding credentials. TURN servers are needed to allow nodes behind symmetric NATs to communicate, when STUN-based NAT traversal fails.

_Address_ specifies the TURN server endpoint IP address and port, in format address:port

_User_ specifies the TURN user name

_Password_ specifies a corresponding TURN password for _User_

* _Overlays_ specifies a configuration for each overlay being managed by this controller, starting with the UUID (a hexadecimal value matching one in the CFx Overlays list (see above) *Note*: currently, the system only supports a single overlay.

* _Type_ currently, the only value allowed is TUNNEL

* _TapName_ specifies the prefix used for creating the tap virtual network interface devices. Each tunnel created by the link manager is bound to a tap device of the operating system; the full name of the tap device consists of this prefix, appended with the first 7 characters of the link ID (e.g. *tnl-1234567*)

* _IgnoredNetInterfaces_ specifies a list of TAP device names that should not be used for tunneling. No tunnel endpoints points to these network interfaces will be generated

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

## OverlayVisualizer module

This module configures information for an (optional) overlay visualizer service

* _Enabled_ specifies whether the visualizer is enabled (true) or not (false)

* _TimerInterval_ specifies the interval, in seconds, to send updates

* _WebServiceAddress_ specifies the IP:port endpoint of the visualizer's Web service

* _GeoCoordinate_ specifies the geographical coordinates (lat,lon) of this node for display in the user interface

* _NodeName_ specifies the name of this node for display in the user interface

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

This module manages the network bridge interaction with the tap devices

* _Dependencies_ lists controller modules the BridgeController depends on. Currently, it depends on "Logger" and "LinkManager"

### BoundedFlood

Under BoundedFlood, you configure parameters related to the implementation of broadcast/multicast over the P2P topology.

* _OverlayID_ is the 7-digit hexadecimal identifier for your overlay (e.g. 101000F)

* _LogDir_ is the directory where bounded flood logs go to (typically the same as the other logs, e.g. /var/log/edge-vpn/)

* _LogFilename_ is the file name for the bounded flood log (e.g. bf.log)

* _LogLevel_ is the logging level for this module (e.g. INFO, DEBUG)

* _MaxBytes_ maximum size of a log file archive

* _BackupCount_ number of archives for the log file

* _BridgeName_ specifies a mnemonic used for naming the bridge instance.

* _DemandThreshold_ specifies the amount of data transferred over a link that triggers the creation of an on-demand link (e.g. 100M for 100MBytes). Units supported included: K (Kilo), M (Mega) and G (Giga)

* _MonitorInterval_ Interval in which on-demand thresholds are monitored for 

* _MaxOnDemandEdges_ maximum number of on-demand edges that this node may request; setting this to 0 disables creation of on-demand links

* _FlowIdleTimeout_ if an SDN flow rule is not being used (no packet maches the rule) by the SDN switch within this interval, it is removed

* _FlowHardTimeout_ a hard timeout for removing SDN flow rules (regardless of whether they are used or not)

* _MulticastBroadcastInterval_ interval in which a multicast sender will send broadcasts to program multicast trees

* _ProxyListenAddress_ listening address of the bridge module in the controller - the SDN controller uses this to send requests to the EdgeVPN.io overlay controller

* _ProxyListenPort_ listening TCP port of the bridge module in the controller - the SDN controller uses this to send requests to the EdgeVPN overlay controller



### Overlays

* _Overlays_ specifies a configuration for each overlay being managed by this controller, starting with the UUID (a hexadecimal value matching one in the CFx Overlays list (see above). It holds configurations for the network interface device (NetDevice) and the SDN controller (SDNController) *Note*: currently, the system only supports a single overlay.

#### NetDevice

* _AutoDelete_ specifies whether to remove the bridge device that was specified when the controller shuts down. Possible values: True, False. Setting this to False is useful if you want the software to attach to an existing bridge device.

* _Type_ specifies the type of network bridge to instantiate. Supported values are OVS (for Open vSwitch), VNIC (virtual NIC), and LXBR (Linux bridge). For most use cases, OVS is used - a node connected in Switch role structured overlay requires OVS. You should only use VNIC if Topology’s Role is set to _Leaf_. You should onlye use LXBR when you have an overlay that is "hardwired" with _ManualTopology_ (see Topology module section)

* _SwitchProtocol_ Use BF for Bounded Flood (default for Type OVS), STP for Spanning Tree Protocol (default for Type LXBR)

* _NamePrefix_ is the prefix used to name the primary bridge/switch; the prefix is appended with the Overlay ID string

* _IP4_ specifies the IPv4 address to assign to the bridge. This is an optional parameter; if your deployment does not require assigning an IP configuration to the bridge, it can be omitted

* _PrefixLen_ specifies the network prefix length to apply to the bridge. (Optional parameter)

* _MTU_ specifies the maximum transmission unit size to be applied to the bridge

#### AppBridge

This optional section configures a secondary "patch through" bridge that is connected to the primary bridge in a configuration where the node runs an endpoint with its own IP address

* _AutoDelete_ specifies whether to remove the bridge device that was specified when the controller shuts down. Possible values: True, False (default)

* _NamePrefix_ is the prefix used to name this bridge; the prefix is appended with the Overlay ID string

* _IP4_ specifies the IPv4 address to assign to the bridge. This is an optional parameter; if your deployment does not require assigning an IP configuration to the bridge, it can be omitted

* _PrefixLen_ specifies the network prefix length to apply to the bridge. (Optional parameter)

* _MTU_ specifies the maximum transmission unit size to be applied to the bridge. Optional parameter; by default, it is set to 1410

#### SDNController

This section configures the SDN controller endpoint

* _ConnectionType_ configures the transport used; set this to tcp

* _HostName_ configures the host where the SDN controller runs; set this to 127.0.0.1 (localhost)

* _Port_ configures the port where the SDN controller listens to; set this to 6333

Example:

```
  "BridgeController": {
    "Dependencies": [
      "Logger",
      "LinkManager"
    ],
    "BoundedFlood": {
      "OverlayId": "101000F",
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
      "101000F": {
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
            "PrefixLen": 16,
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
```

## Additional information

The configuration file tells the controller framework which modules to load and with what parameters. If a module is not specified in the configuration, it is not loaded. Certain keys occur in multiple modules with the same meaning and effect. Each module has an Enabled key that, when set to false, will cause the controller framework to skip loading it. TimerInterval specifies how often a module’s timer event fires in seconds. Dependencies specify which modules are used a module and must be loaded before hand. In your installed EdgeVPN.io package, you will find in controller/framework/fxlib.py a file that contains the full set of default values in the CONFIG dictionary. This dictionary is loaded first, and any values specified in config file will override them. Therefore, changes should always be made to the config.json file and not the fxlib.py file. 
