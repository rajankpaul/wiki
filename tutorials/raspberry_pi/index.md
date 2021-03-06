---
layout: default
title: Raspberry Pi
---

# Raspberry Pi

> The OG SBC.
> I honestly think everyone should own one.
> This guide assumes physical security, as the rpi is not powerful enought to support disk cryptography.

## Preparation
*   For a 32-bit operating systems, use Raspbian. For 64-bit, use Ubuntu Server for the Raspberry Pi.
*   Use Win32DiskImager or Raspberry Pi Imager to flash your desired OS image onto a microSD card

### Raspbian
*   Remount the microSD card and add an empty file named *ssh* to the boot partition
*   If using wifi, add the following *wpa_supplicant.conf* file to the boot partition:

```
country=us
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 scan_ssid=1
 ssid="MyNetworkSSID"
 psk="Pa55w0rd1234"
}
```

*   default username=pi, password=raspberry

### Ubuntu Server
*   Remount the microSD card and edit config.txt on the boot partition to reflect the following changes to implement USB boot:

```
kernel=vmlinuz
initramfs initrd.img followkernel
#device_tree_address=0x03000000
root=LABEL=writable
```

*   default username=ubuntu, password=ubuntu

## Initial Setup
*   Insert the flashed microSD card into the rpi, and power it on
*   Look for the rpi's IP Address using a tool like Wireless Network Watcher on Windows or *arp-scan* on cli
*   ssh into the pi using default username and password
*   immediately run *passwd* command to change default password to something more secure
*   run the following commands to configure localisation and timezone:
```bash
sudo dpkg-reconfigure locales
sudo dpkg-reconfigure tzdata
```

*   Enable swap:

```
sudo swapoff -a
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

*   in /etc/fstab -> /swapfile swap swap defaults 0 0
*   sudo swapon \-\-show

*   run the following commands to update:

```bash
sudo apt update
sudo apt dist-upgrade -y
sudo apt autoremove
```

## New User
*   In order to creater a new user, run the following command and replace \<username\> with your actual desired username. It will ask you to enter a new password:

```bash
sudo adduser --gecos "" <username>
```
*   Run the following command to add your new user to the same groups as pi, except for the pi group. You must once again replace \<username\> with your desired username:

```bash
for GROUP in adm dialout cdrom sudo audio video plugdev games users input netdev spi i2c gpio; do sudo adduser <username> $GROUP; done
for GROUP in adm dialout cdrom floppy sudo audio dip video plugdev lxd netdev; do sudo adduser <username> $GROUP; done
```

*   Use the following commands to switch to your new user and disable logging in directly to the pi user:

```bash
sudo su <username>
sudo passwd --lock pi
```

## Software Recommendations
\*the ones marked with an asterix are *in my opinion*, essential
### Utility
*   [vim\*](.)
### Security
*   [ufw\*](.)
*   [fail2ban\*](.)
*   [bitwarden_rs](.)
### Networking
*   [samba](.)
*   [nginx](.)
*   [ddclient](.)
*   [openvpn](.)
### Services
*   [jellyfin](.)
*   [tranmission](.)

## Optional Configuration
```
# turn off LEDs
sudo vim /boot/config.txt

# Disable Ethernet LEDs
dtparam=eth_led0=14
dtparam=eth_led1=14

# Disable the PWR LED
dtparam=pwr_led_trigger=none
dtparam=pwr_led_activelow=off

# Disable the Activity LED
dtparam=act_led_trigger=none
dtparam=act_led_activelow=off
```

```
# Disable the ACT LED on the Pi Zero.
dtparam=act_led_trigger=none
dtparam=act_led_activelow=on
```

[back](../)
