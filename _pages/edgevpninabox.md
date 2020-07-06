---
permalink: /edgevpninabox/
title: "EdgeVPN-in-a-box"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

This tutorial guides you through a quick deployment of an OpenFire XMPP server and a couple of EdgeVPN nodes using Docker.

You need at least one Linux computer with Docker and Open vSwitch installed

## Deploy an XMPP server using Docker

We have created a Docker container pre-configured with two XMPP users test1, test2 for this tutorial, following the [instructions on deploying an XMPP server using Docker](/openfiredocker) (the OpenFire admin password for this container is *edgevpn_demo*). Deploy the XMPP server with this command:

```
docker run --name openfire -d -p 9090:9090 -p 5222:5222 -p 5269:5269 -p 5223:5223 -p 7443:7443 -p 7777:7777 -p 7070:7070 -p 5229:5229 -p 5275:5275 edgevpn/openfire_edgevpn_demo
```

## Deploy two EdgeVPN nodes running Docker

Now deploy two EdgeVPN nodes by following the [instructions on deploying EdgeVPN Docker containers](/dockeredgevpn). 

You may deploy the two EdgeVPN nodes in the same host, or in different hosts. Make sure to replace A.B.C.D with the IP address of the computer where you deployed the XMPP server in the previous step (the XMPP user names and passwords are configured to with with this tutorial).
