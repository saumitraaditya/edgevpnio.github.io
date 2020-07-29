---
permalink: /install/
title: "Installing EdgeVPN.io"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Install and Run on Ubuntu Linux

Tested on Ubuntu 18.04 x64

## Get deb Package
Download the latest release from [the GitHub repository](https://github.com/EdgeVPNio/evio/releases/).

## Install deb Package

```shell
sudo apt install -y <path/to>/edge-vpnio_*.deb
```

## Edit Configuration File
After installation, but before starting, configure your node by editing `/etc/opt/edge-vpnio/config.json`. [You can use the template from this page and add XMPP credentials, setting the IP address, and applying other configurations as needed](/configbasics) 

## Run Service
```shell
sudo systemctl start evio
``` 

Additionally, use `systemctl` to `start`/`stop`/`restart`/`status` `evio`.

## Dependencies
The installer has dependencies on, and will install `python3 (>=3.6)`, `python3-dev (>=3.6)`,  `python3-pip`, `iproute2`, `bridge-utils`.


## Related Files and Directories
By default, the following files and directories are created:
1. `/opt/edge-vpnio/tincan`
2. `/opt/edge-vpnio/controller/`
3. `/etc/opt/edge-vpnio/config.json`
4. `/etc/systemd/system`
5. `/var/logs/edge-vpn/tincan_log`
6. `/var/logs/edge-vpn/ctrl.log`

## Disabling or removing the software

### To disable Start on Boot
```shell
sudo systemctl disable evio
```

### To remove the package
```shell
sudo apt remove -y evio
```
