---
permalink: /openfireconfig/
title: "Configuring Openfire XMPP server"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

One of the open-source XMPP servers that can be used to configure EdgeVPN groups is Openfire. An XMPP server is necessary for EdgeVPN to work - nodes in an EdgeVPN virtual network use an XMPP server for the following purposes:

1. Network membership: the XMPP server holds identities to authenticate EdgeVPN nodes, and also groups them together to specify which nodes are allowed to join the EdgeVPN. A single XMPP server can be used to create multiple users (individual EdgeVPN endpoints) and groups (the set of nodes that form an EdgeVPN network).
2. Peer discovery: EdgeVPN nodes use XMPP "presence" messages to advertise themselves to other nodes. These are short messages that are sent periodically to the XMPP server, and forwarded to nodes belonging to a group
3. Signalling to create tunnels: EdgeVPN uses XMPP short messages to exchange information that is needed to create Internet tunnels. These include the set of candidate endpoints (IP:port of the local node, as well as STUN and TURN endpoints), and certificate fingerprints to establish a private, encrypted tunnel

This document explains steps to configure an Openfire XMPP server for EdgeVPN use. As far as configuring an XMPP server, the main configuration steps boil down to the creation of users and groups, and the configuration of passwords or x509 certificates, depending on which authentication method you use

# Creating users and groups

Openfire exposes a Web-based admin interface to configure users and groups. The suggested configuration for an EdgeVPN network is as follows:

* Create a user for each node that joins an EdgeVPN. User names can be made unique by convention - for instance, evpn_XX_user_YY, where XX identifies an EdgeVPN group, and YY identifies an EdgeVPN node
* Create a group for each EdgeVPN, and add users to this group - for instance, a group evpn_XX that has all users evpn_XX_user_YY added to it

Once you authenticate to the Openfire admin interface, creating users and groups in Openfire is fairly straightforward - [please refer to steps 3 and 4 of this document for isntructions](/openfiredocker)

# Setting up certificates

This step is not necessaryif  you use password-based authentication; it is necessary if you use x509-based authentication.



