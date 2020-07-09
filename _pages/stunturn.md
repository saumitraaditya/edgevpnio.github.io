---
permalink: /stunturn/
title: "STUN and TURN configuration"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

EdgeVPN supports NAT traversal using two mechanisms: [STUN](https://en.wikipedia.org/wiki/STUN) (Session Traversal Utilities for NAT), and [TURN](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT) (Traversal Using Relays around NAT). 

STUN works for the majority of deployed NATs: the [cone-type NATs, including full-cone, port-restricted cone, and address-restricted cone](https://en.wikipedia.org/wiki/Network_address_translation). If your deployment only has cone-type NATs, STUN is sufficient. However, if your nodes are behind symmetric NATs, you will need also TURN.

This document describes a couple of possible ways you can deploy your own STUN and TURN servers - they assume you are using Ubuntu Linux 14.04 or later. You may also use existing Internet services - free STUN servers are available (such as the Google STUN servers EdgeVPN is configured with by default), and pay-per-use TURN services also exist, e.g. [Xirsys](http://www.xirsys.com).

Open-source XMPP servers [eJabberd](https://www.ejabberd.im/) and [Openfire](https://www.igniterealtime.org/projects/openfire/) both offer STUN services, facilitating deployment if you are using either of these as your XMPP servers. eJabberd also offers a TURN service.

# Deploying STUN and TURN in eJabberd

eJabberd allows you to enable STUN and/or TURN through its configuration file. [Please refer to the eJabberd documentation for up-to-date instructions](https://docs.ejabberd.im/admin/configuration/listen/#ejabberd-stun-1)

# Deploying STUN in Openfire

Openfire supports STUN by adding a server plug-in. Currently, we are not aware of a TURN plug-in for Openfire. [Please refer to the Openfire documentation for up-to-date instructions](https://www.igniterealtime.org/projects/openfire/plugins/1.2.2/stunserver/readme.html)

# Deploying your own TURN server

There is an open-source package _turnserver_ you can use to install your own TURN server

1. Install turnserver

	```bash
	sudo apt-get update
	sudo apt-get install turnserver
	```

2. If you are running your TURN server on the cloud (e.g. Amazon EC2), you must use IP aliasing to allow the TURN server to bind your public IP address. Replace <public-ip-of-turnserver> with the IP address of your TURN server (e.g. 1.2.3.4)

	```bash
	sudo ifconfig eth0:0 <public-ip-of-turnserver> up
	```

3. Update the turnserver configuration file with the public IP address. Again, replace <public-ip-of-turnserver> with the IP address of your TURN server (e.g. 1.2.3.4)

	```bash
	TURNIPv4="<public-ip-of-turnserver>"
	sudo sed -i "s/listen_address = .*/listen_address = { \"$TURNIPv4\" }/g" /etc/turnserver/turnserver.conf
	```

4. (Optional) Set the number of sessions per user

	```bash
	sudo nano /etc/turnserver/turnserver.conf
	```

	```data
	## Max relay per username.
	max_relay_per_username = 20000

	## Allocation lifetime.
	allocation_lifetime = 720000
	```

5. (Optional) Set the file descriptor limit to allow for thousands of TURN connections. 

*Notes*: here, ubuntu is the username. You must re-login for these changes to take effect.

	```bash
	sudo nano /etc/security/limits.conf
	```

	```data
	ubuntu    hard    nofile    20000
	ubuntu    soft    nofile    20000
	```

6. To control user accesses to the TURN service, create user credentials (user names and passwords) by modifying the `/etc/turnserver/turnusers.txt` file.

	```bash
	sudo nano /etc/turnserver/turnusers.txt
	```

	```data
	...
	edgevpnuser:password:<public-ip-of-turnserver>:authorized
	...
	```
	Note: entries must be of the form: `<username>:<password>:<public-ip-of-turnserver>:<authorizatization level>`

7. Run turnserver

	```bash
	sudo turnserver -c /etc/turnserver/turnserver.conf
	```

8. Verify that turnserver is running on port 19302

	```bash
	netstat -aupn | grep 19302
	```
  
9. Configure EdgeVPN

You should now be able to configure your EdgeVPN nodes to use the TURN service, by replacing the <public-ip-of-turnserver>, <username> and <password> parameters with your own - see below for an example:

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
      "Address": "<public-ip-of-turnserver>:19302",
      "User": "<username>",
      "Password": "<password>"
     }],
    "Overlays": {
      "101000F": {
        "Type": "TUNNEL",
        "TapName": "tnl-"
      }
    }
  },
```

