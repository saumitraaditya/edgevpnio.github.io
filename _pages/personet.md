---
permalink: /personet/
title: "PerSoNet"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## PerSoNet

This page contains information about PerSoNet, a project that has built a prototype of a social network-based overlay VPN that 
has contributed to the conceptual development of EdgeVPN.io.

### What is PerSoNet?

Progress in mobile and cloud computing leads to an ever-increasing number of devices with increasing diversity in capacity,
capabilities and usage modalities. Furthermore, the wide adoption of Online Social Networks (OSNs) for user interaction points
to a future where applications increasingly demand the ability to communicate among distributed personal devices, and across
those of social peers. It is crucial for these devices to communicate with each other not only seamlessly, but also securely. 
However, the increased number, mobility and diversity of devices pose ever-increasing challenges. Outside of an enterprise environment, 
users remain typically responsible for managing their own devices which is complex and error-prone. 
To address these challenges, this project develops PerSoNet, a novel approach based on overlay peer-to-peer (P2P) networks 
allowing Internet users to seamlessly establish virtual private networks connecting their own personal devices and extending to 
devices of their trusted peers by leveraging OSNs as a basis for user and device discovery and for the setup of private communication 
channels.

PerSoNet is an unstructured P2P overlay whose topology is derived from personal device links and social networking relationships. 
It supports private end-to-end communication among various personal devices, leverages social relationships for the discovery and 
setup of private messaging channels among social peers, and applies network virtualization techniques to support unmodified software. 
Thus, existing and future applications can communicate privately within personal device networks and across social peers, abstracting 
friend connections as network links, without forfeiting the use of existing OSNs for seamless discovery and management of relationships, 
and exposing using well-known network abstractions and programming primitives to developers. Specifically, PerSoNet exposes a 
layer-2 virtual network abstraction among the set of personal devices, and a layer-3 abstraction across devices of social network users, 
providing a basis to support existing and future applications. The project investigates novel overlay techniques with quantitative 
studies, using simulation techniques and also by advancing the state-of-the-art in systems design and implementation through a software 
prototype and experiments with realistic applications and systems. This project has the potential to impact a large number of OSN 
users in applications including private data sharing, private communication among peers, and disaster- and censorship-resistant 
communication systems.

![PersoNet](/assets/images/personet.png)

The PerSoNet design consists of three major layers of abstraction:

_Trust layer (top)_: users establish trust relationships derived/managed from Online social networks, or created via ad-hoc introduction. 

_Personal network layer (bottom)_: devices associated with each user are connected in a OSI layer-2 like network. 
A subset of devices act as border switches/routers for connecting islands of personal devices. 

_Community layer (middle)_: OSI layer-3 like interconnection between personal networks of users; social gateways are logically aggregated 
routers responsible for routing and forwarding at this layer. Dotted lines: Alice’s AD1 is a device at Alice’s home connected via a 
virtual switch (AR2) to virtual switch (AR1) on a VM in a cloud service which relays message to Alice’s device AD3 via virtual routers 
AR2 and AR1; Carol communicates with Maya via a common friend (John) via his social gateway.

### Key publications produced as part of the project

[1]. Saumitra Aditya, "Software Defined Overlay Network and Workload Orchestration in Social Network Based Edge Infrastructure for Smart Communities", Ph.D. Dissertation, ECE Department, University of Florida, July 2020.

[2]. Saumitra Aditya, Kensworth Subratie, and Renato J. Figueiredo, [“PerSoNet: Software-defined Overlay Virtual Networks Spanning Personal Devices Across Social Network Users”](https://ieeexplore.ieee.org/abstract/document/8591012), 2018 IEEE International Conference on Cloud Computing Technology and Science (CloudCom)

[3]. Saumitra Aditya, Renato Figueiredo, ["SocialEdge: Enabling Trusted Data Processing Workflow in Smart Communities"](https://ieeexplore.ieee.org/abstract/document/8968844), 2019 IEEE International Conference on Cloud Computing Technology and Science (CloudCom)

[4]. Saumitra Aditya, Renato Figueiredo. ["Frugal: Building degree-constrained overlay topology from social graphs"](https://ieeexplore.ieee.org/abstract/document/8014355), Proceedings of IEEE International Conference on Fog and Edge Computing (IEEE ICFEC), 2017.

### Acknowledgments

PerSoNet is based upon work supported in part by the National Science Foundation under Grant No. 1527415. Any opinions, findings, and conclusions or recommendations expressed in this material are those of the author and do not necessarily reflect the views of the National Science Foundation.
