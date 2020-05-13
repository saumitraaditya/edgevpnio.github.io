---
permalink: /install/
title: "Installing EdgeVPN"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# Install and Run EdgeVPN on Ubuntu Linux

Tested on Ubuntu 18.04 x64

## Get deb Package
Download the latest release of EdgeVPN from [GitHub](https://github.com/EdgeVPN/Downloads/releases).

## Install deb Package

```shell
sudo apt install -y <path/to>/edgevpn_*.deb
```
On Desktop Environments, you can install the deb package by double click.

## Edit EdgeVPN Configuration File
After installation, but before starting EdgeVPN, configure your node by editing `/etc/opt/edgevpn/config.json` [by adding the XMPP credentials, setting the IP address, and applying other configurations as needed](/configbasics) 

## Run EdgeVPN Service
```shell
sudo systemctl start edgevpn
``` 

Additionally, use `systemctl` to `start`/`stop`/`restart`/`status` `edgevpn`.

## Dependencies
The installer has dependencies on, and will install `python3 (>=3.6)`, `python3-dev (>=3.6)`,  `python3-pip`, `iproute2`, `bridge-utils`.


## Related Files and Directories
By default, the following files and directories are created:
1. `/opt/edgevpn/tincan`
2. `/opt/edgevpn/controller/`
3. `/etc/opt/edgevpn/config.json`
4. `/etc/systemd/system`
5. `/var/logs/edgevpn/tincan_log`
6. `/var/logs/edgevpn/ctrl.log`

## Disabling or removing EdgeVPN

### To disable Start on Boot
```shell
sudo systemctl disable edgevpn
```

### To remove the package
```shell
sudo apt remove -y edgevpn
```
