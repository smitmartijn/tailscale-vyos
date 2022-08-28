# Installing Tailscale onto VyOS

Tailscale: https://tailscale.com
Zero config VPN. Installs on any device in minutes, manages firewall rules for you, and works from anywhere.

VyOS: https://vyos.net
The Universal Router.


There's another repository at https://github.com/DMarby/vyos-tailscale that explains how to embed tailscale into a new VyOS ISO file which can then be used to install VyOS with tailscale included. But what if you already have a running VyOS instance?

## Installing Tailscale on VyOS

VyOS 1.2 and 1.3 are based on Debian Buster, so we can use the same instructions as installing on Debian. You can find those instructions here: https://pkgs.tailscale.com/stable/#debian-buster

```
# Add Tailscale's GPG key
curl -fsSL https://pkgs.tailscale.com/stable/debian/buster.asc | sudo apt-key add -
# Add the tailscale repository
curl -fsSL https://pkgs.tailscale.com/stable/debian/buster.list | sudo tee /etc/apt/sources.list.d/tailscale.list
# Install Tailscale
sudo apt-get update && sudo apt-get install tailscale
# Start Tailscale!
sudo tailscale up
```

## Example

Here's an example of a VyOS  1.3.0-rc6 instance that hosts 10.10.0.0/24 behind its LAN interface.

```
vyos@vyos:~$ sudo bash
root@vyos:/home/vyos# curl -fsSL https://pkgs.tailscale.com/stable/debian/buster.asc | sudo apt-key add -
OK
root@vyos:/home/vyos# cat /etc/debian_version
10.10
root@vyos:/home/vyos# curl -fsSL https://pkgs.tailscale.com/stable/debian/buster.list | sudo tee /etc/apt/sources.list.d/tailscale.list
# Tailscale packages for debian buster
deb https://pkgs.tailscale.com/stable/debian buster main
root@vyos:/home/vyos# sudo apt-get update && sudo apt-get install tailscale
Get:1 https://pkgs.tailscale.com/stable/debian buster InRelease
Get:2 https://pkgs.tailscale.com/stable/debian buster/main amd64 Packages [6,129 B]
Fetched 11.7 kB in 1s (11.5 kB/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  tailscale
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 19.6 MB of archives.
After this operation, 36.4 MB of additional disk space will be used.
Get:1 https://pkgs.tailscale.com/stable/debian buster/main amd64 tailscale amd64 1.28.0 [19.6 MB]
Fetched 19.6 MB in 3s (7,074 kB/s)
Selecting previously unselected package tailscale.
(Reading database ... 59473 files and directories currently installed.)
Preparing to unpack .../tailscale_1.28.0_amd64.deb ...
Unpacking tailscale (1.28.0) ...
Setting up tailscale (1.28.0) ...
Created symlink /etc/systemd/system/multi-user.target.wants/tailscaled.service → /lib/systemd/system/tailscaled.service.
localepurge: Disk space freed in /usr/share/locale: 0 KiB
localepurge: Disk space freed in /usr/share/man: 0 KiB

Total disk space freed by localepurge: 0 KiB

root@vyos:/home/vyos# tailscale up --advertise-routes 10.10.0.0/24 --accept-routes

To authenticate, visit:

        https://login.tailscale.com/a/xxxxxxx

Success.
root@vyos:/home/vyos# 
```

After marking this host as a subnet host in Tailscale, devices in 10.10.0.0/24 were available.
