---
permalink: /build/
title: "Build EdgeVPN.io"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

## Introduction

This document guides you through the process of building EdgeVPN.io. The instructions below work are for x86/64 Linux.

First, clone the EVio tools repository:

```
git clone http://github.com/edgevpnio/tools
```

Now install build tools:

```
sudo apt-get update -y 
sudo apt-get install -y build-essential
```

Use the _setup_ script under the _tools_ directory to set up the source code, download third-party libraries (WebRTC) for the tincan module, and build:

```
cd tools
./setup src
./setup tincan
```

The tincan and controller code can be found a level up in the directory hierarchy under the evio directory:

```
cd ../evio
ls -l tincan/trunk/out/release/x86_64/tincan
ls controller
```

Optionally, you can also build a Debian package and a Docker image with:

```
./setup debpak
./setup dkrimg
```




