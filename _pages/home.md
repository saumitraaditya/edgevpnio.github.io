---
permalink: /
title: "Open-source VPN for Edge Computing"
header:
  overlay_color: "#5e616c"
excerpt: "Seamlessly connect edge resources with a scalable virtual Ethernet"  
---

### EdgeVPN is an open-source software for deploying scalable VPNs across distributed edge resources 

It groups distributed nodes into a logical Ethernet. EdgeVPN has built-in support for packet capture, encryption, tunneling, forwarding, and NAT traversal, EdgeVPN builds on standard protocols, flexible software-defined networking, and a scalable overlay network architecture suitable for containerized applications


<img src="/assets/images/network-1614045_160.png"
     alt="text"
     style="float: left;" />
**Scalable and self-configuring** Designed based on principles used in scalable, ring-structured key/value stores and peer-to-peer overlays, there are no central VPN traffic bottlenecks, and the network scales and configures itself as nodes are added/removed

<img src="/assets/images/network-cables-494650_160.jpg"
     alt="text"
     style="float: left;" />
**Run existing software** EdgeVPN exposes virtual network interfaces and private IP addresses allowing existing and future Ethernet/IP based software stacks to run, unmodified. EdgeVPN can provide a virtual cluster of Docker containers across edge/cloud resources

<img src="/assets/images/it-4072549_100.png"
     alt="text"
     style="float: left;" />
**Deploy anywhere** EdgeVPN transparently connects edge devices with private addresses behind Network Address Translators (NATs) and firewalls and cloud computing resources, encrypting and tunneling traffic peer-to-peer across the Internet

<br/><br/>

### Key EdgeVPN features

**Structured topology:** 
EdgeVPN implements a structured peer-to-peer overlay topology where nodes self-organize into a ring ordered by unique node IDs, and with randomly-assigned “long-distance” links, based on the approach described in [Symphony](http://infolab.stanford.edu/~bawa/Pub/symphony.pdf). *This topology is scalable:* the average distance between two nodes can scale as a log(N) function, where N is the number of nodes. Topology handling is modular, such that other topologies can be implemented.

**Encrypted links:**
EdgeVPN links are encrypted and authenticated with standard SSL-based transport-layer security implemented by the open-source [WebRTC framework](https://webrtc.org/). *Communication among EdgeVPN nodes is private:* links use UDP-based Datagram TLS (DTLS) over NAT hole-punched tunnels

**Easy grouping:**
EdgeVPN uses the standard [XMPP protocol](https://xmpp.org/) with short messages to discover and exchange connection information with peers. While packet switching and routing is decentralized and based on a scalable P2P overlay, *membership can be managed centrally:*  EdgeVPN groups can be easily configured for networks small to large in an XMPP server, such as the open-source [OpenFire](https://www.igniterealtime.org/projects/openfire/) and [eJabberd](https://www.ejabberd.im/) servers

**Layer-2 virtual network:**
EdgeVPN exposes a virtual Ethernet to its endpoints, and supports the ARP protocol, and unicast and IGMP-based multicast IP applications. *You can run existing IP-based applications over EdgeVPN without modifications*

**Programmable and extensible:**
The core packet-switching in EdgeVPN is programmable, using the [OpenFlow protocol for Software-Defined Networking](https://www.opennetworking.org/). EdgeVPN integrates with [Open vSwitch](https://www.openvswitch.org/) virtual switches, and endpoint interfaces are exposed via a virtual tap device. EdgeVPN can be deployed on physical and virtual machines, and in [Docker](https://www.docker.com/) containers

**Built on standards:**
EdgeVPN leverages standards for NAT traversal (STUN, TURN, ICE), transport-layer security (TLS, DTLS), software-defined networking (OpenFlow), and short messaging (XMPP), and reuses the WebRTC open-source framework 
