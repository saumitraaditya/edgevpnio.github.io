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

# Preliminaries and useful terminology

* NAT: A Network Address Translator

* EdgeVPN node: a (virtual) machine that runs the EdgeVPN software. An EdgeVPN deployment consists of a set of EdgeVPN nodes, whereas nodes may have private addresses and subject to NAT(s).

* XMPP server: a (virtual) machine that runs an XMPP-compliant server. Typically, an EdgeVPN deployment uses a single XMPP server with a public Internet address

* STUN server: a (virtual) machine that runs a STUN-compliant server. An EdgeVPN deployment requires at least one STUN server (possibly more, for fault-tolerance) with a public Internet address

* TURN server: a (virtual) machine that runs a TURN-compliant server. An EdgeVPN deployment may use one or more TURN servers (for fault tolerance and load balancing) with a public Internet address

* WebRTC: an open framework for the web that enables Real-Time Communications (RTC) capabilities, implemented in C++
TinCan link: an abstraction of a virtual link that carries encapsulated, encrypted Ethernet frames between two EdgeVPN nodes. A TinCan link builds upon a WebRTC connection; the EdgeVPN tincan process implements this abstraction. The tincan code is implemented in C++

* SDN: Software-Defined Networking. The SDN architecture decouples the network control and forwarding functions, enabling the network control to become directly programmable and the underlying infrastructure to be abstracted for applications and network services
OpenFlow: a standard protocol for SDN solutions

* OVS: short for Open vSwitch; an open-source software implementation of an SDN-programmable Ethernet switch

* EdgeVPN controller: a Python-based program responsible for the control of various aspects of EdgeVPN, including: tincan link management, topology management, and signaling through XMPP

* Ryu SDN controller: a Python-based SDN controller that is responsible for programming flow rules that govern packet switching in an EdgeVPN OVS switch
