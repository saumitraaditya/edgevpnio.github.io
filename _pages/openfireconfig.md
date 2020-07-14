---
permalink: /openfireconfig/
title: "Configuring Openfire XMPP server"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Introduction

One of the open-source XMPP servers that can be used to configure EdgeVPN.io groups is [Openfire](https://www.igniterealtime.org/projects/openfire/). You may deploy Openfire in manhy different ways, such as installing on a physical host, [using a Docker container](https://hub.docker.com/r/quantumobject/docker-openfire), or [deploying on a cloud provider](https://bitnami.com/stack/openfire/cloud/aws). An XMPP server is necessary for the system to work - nodes in a virtual network use an XMPP server for the following purposes:

1. Network membership: the XMPP server holds identities to authenticate nodes, and also groups them together to specify which nodes are allowed to join the virtual network. A single XMPP server can be used to create multiple users (individual endpoints) and groups (the set of nodes that form a network).
2. Peer discovery: nodes use XMPP "presence" messages to advertise themselves to other nodes. These are short messages that are sent periodically to the XMPP server, and forwarded to nodes belonging to a group
3. Signalling to create tunnels: EdgeVPN.io uses XMPP short messages to exchange information that is needed to create Internet tunnels. These include the set of candidate endpoints (IP:port of the local node, as well as STUN and TURN endpoints), and certificate fingerprints to establish a private, encrypted tunnel

This document explains steps to configure an Openfire XMPP server for use with the software. As far as configuring an XMPP server, the main configuration steps boil down to the creation of users and groups, and the configuration of passwords or x509 certificates, depending on which authentication method you use

# Creating users and groups

Openfire exposes a Web-based admin interface to configure users and groups. The suggested configuration for a network is as follows:

* Create a user for each node that joins a network. User names can be made unique by convention - for instance, evpn_XX_user_YY, where XX identifies a group, and YY identifies a node
* Create a group for each virtual network, and add users to this group - for instance, a group evpn_XX that has all users evpn_XX_user_YY added to it

Once you authenticate to the Openfire admin interface, creating users and groups in Openfire is fairly straightforward - [please refer to steps 3 and 4 of this document for isntructions](/openfiredocker)

# Setting up certificates

This step is not necessary if you use password-based authentication; it is necessary if you use x509-based authentication.

## Certificate Authority

You will need a Certificate Authority (CA) in order to issue certificates for your setup. There are many different ways and several documents you can find on how to configure a CA, depending on your use case. For instance, [you may configure a simple CA using openssl tools in Linux](https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/):

Step 1: Create a directory to hold the certificates and keys:

```
mkdir cacerts ; cd cacerts/ 
```

Step 2: Create a certificate signing authority and key pair:

```
openssl genrsa -des3 -out myCA.key 2048
openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem
```

Step 3: Create private key for own certificate:

```
openssl genrsa -out myusername.key 2048
```

Step 4: Prepare a certificate signing request

Here, the common name **must** be your username on the XMPP server. This should be just the user name part of the full XMPP name@domain string. For instance, for "myusername@local", where "local" is the domain portion, the common name in the certificate request should be just "myusername"

```
openssl req -new -key myusername.key -out myusername.csr
```

Step 5: Sign and create a certificate using the CA credentials

```
openssl x509 -req -in myusername.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial -out myusername.crt -days 1825 -sha256
```

## Server-side configuration

Once you have a CA and have issued certificates for your nodes (each node should have its own unique XMPP user name and certificate as explained above), you need to 1) configure the XMPP server to support certificate auth, and 2) upload the CA's certificate.

Step 1: Your Openfire XMPP server needs to be configured for certificate-based authentication, by ensuring the following properties are set. 

You can set properties by navigating, in the admin Web interface, to: Server->Server Manager->System properties

```
Property                                       Value
xmpp.client.cert.policy                     -> needed
xmpp.client.certificate.accept-selfsigned   -> true
xmpp.client.certificate.verify              -> true
xmpp.client.certificate.verify.chain        -> true
xmpp.client.certificate.verify.root         -> true
sasl.mechs                                  -> EXTERNAL
```

Step 2: Upload CA certificate:

In the Openfire admin Web interface, navigate to Server -> TLS/SSL certificates

In the section "Trust store used for connections from clients", navigate to "Manage store contents", then click on "import form"

Then, copy over the CA's certificate (the myCA.pem file as described above). Set the CA's common name as the alias, and save

## EdgeVPN.io configuration

Each node needs to be configured, in the JSON file, with its certificate and private key. In summary, *AuthenticationMethod* needs to be set to x509, *CertDirectory* with the name of a local directory with the user's certificate and key, *CertFile* with the file name of the certificate (e.g. myusername.crt), and *KeyFile* with the file name of the private key (e.g. myusername.key)

Please refer to the [configuration file documentation](/configfile) for more detailed information.


