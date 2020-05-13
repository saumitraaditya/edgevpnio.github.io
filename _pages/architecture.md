---
permalink: /architecture/
title: "EdgeVPN architecture"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

This document describes the architecture of EdgeVPN, at a high level - not diving deep into the code, but overviewing the major modules and their interactions. The target audience is developers who are interested in contributing to the code base.

# Background

EdgeVPN is the evolution of IP-over-P2P (IPOP) codebase. IPOP has gone through significant architecture and implementation changes over time - for reference, an overview of these changes appears in the [2019 IEICE paper](https://search.ieice.org/bin/pdf_link.php?category=B&lang=E&year=2020&fname=e103-b_1_2&abst=) This architecture overview reflects the 4th generation of the architecture, which is the basis for EdgeVPN.

# Overview and terminology

The following diagram provides an overview of the various EdgeVPN modules

![EdgeVPN architecture](/assets/images/edgevpn_architecture.png)

* NAT: A [Network Address Translator](https://en.wikipedia.org/wiki/Network_address_translation)

* EdgeVPN node: a (virtual) machine that runs the EdgeVPN software. An EdgeVPN deployment consists of a set of EdgeVPN nodes, whereas nodes may have private addresses and subject to NAT(s).

* XMPP server: a (virtual) machine that runs a server compliant to the [XMPP messaging protocol](https://xmpp.org/). Typically, an EdgeVPN deployment uses a single XMPP server with a public Internet address

* STUN server: a (virtual) machine that runs a server compliant to the [STUN protocol](https://en.wikipedia.org/wiki/STUN). An EdgeVPN deployment requires at least one STUN server (possibly more, for fault-tolerance) with a public Internet address

* TURN server: a (virtual) machine that runs a server compliant to the [TURN protocol](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT). An EdgeVPN deployment may use one or more TURN servers (for fault tolerance and load balancing) with a public Internet address

* WebRTC: [an open framework for the web that enables Real-Time Communications (RTC) capabilities](https://webrtc.org/). WebRTC is implemented in C++

* TinCan link: an abstraction of a virtual link that carries encapsulated, encrypted Ethernet frames between two EdgeVPN nodes. A TinCan link builds upon a WebRTC connection; the EdgeVPN tincan process implements this abstraction. The tincan code is implemented in C++

* SDN: [Software-Defined Networking](https://www.opennetworking.org/sdn-definition/). The SDN architecture decouples the network control and forwarding functions, enabling the network control to become directly programmable and the underlying infrastructure to be abstracted for applications and network services

* OpenFlow: [a standard protocol for SDN solutions](https://en.wikipedia.org/wiki/OpenFlow)

* OVS: short for [Open vSwitch](https://www.openvswitch.org/); an open-source software implementation of an SDN-programmable Ethernet switch

* tap: a [virtual network interface](https://en.wikipedia.org/wiki/TUN/TAP) that allows a user-level process (tincan) to read/write packets from the kernel. In EdgeVPN, one tap device is created for each TinCan link, and each tap device is bound to an OVS port

* EdgeVPN controller: a Python-based program responsible for the control of various aspects of EdgeVPN, including: tincan link management, topology management, and signaling through XMPP

* Ryu SDN controller: [Ryu](https://github.com/faucetsdn/ryu) is a Python-based framework for SDN controllers. EdgeVPN implements a Ryu-based controller that is responsible for programming flow rules that govern packet switching in an EdgeVPN OVS switch

* Topology: EdgeVPN nodes belonging to the same overlay are logically organized according to a graph topology, where each node is a vertex, and each TinCan link is an edge

* node ID: a unique 128-bit ID associated with each EdgeVPN node

* Symphony: the current topology implemented in EdgeVPN is a structured peer-to-peer overlay topology where nodes self-organize into a ring ordered by unique node IDs, and with randomly-assigned “long-distance” links, based on the approach described in [Symphony](http://infolab.stanford.edu/~bawa/Pub/symphony.pdf). This topology is scalable: the average distance between two nodes can scale as a log(N) function, where N is the number of EdgeVPN nodes, while each node has log(N) TinCan links

* Bounded flood: the protocol used among EdgeVPN to support broadcast (e.g. ARP) and form a basis for multicast. Bounded flood refers to the fact that, in a broadcast, nodes forward messages to "flood" their topology neighbors, but the flooding is bound by a range of node IDs to prevent loops
