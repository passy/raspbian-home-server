# Passy's Raspbian Home Server

<img src="https://www.raspberrypi.org/wp-content/uploads/2015/08/raspberry-pi-logo.png" width=150 align=left>

## Plan

This is going to do a few things. Among them is hosting some dashboards with
data about my flat, controlling "smart" switches and uploading my backups for
me.

**Current status:** ~tumbleweeds~

## Preparing the SD Card

- [Get Raspbian Jessie Lite](https://downloads.raspberrypi.org/raspbian_lite_latest.torrent).
  I used `2017-11-01` for this. Also, don't be a jerk, and use the Torrent
  option.
- I use Lite because I don't even want to connect a monitor to the Pi and
  updating Xorg takes ages.
- Copy it onto an SD card. I'll leave it to you to figure out which device
  you're writing to, but keep in mind that this image is intended to partition
  the entire card, so don't specify a partition (i.e. `sdb` not `sdb1`):
  `dd bs=4M if=2017-11-01-raspbian-jessie.img of=/dev/sdb`
- Follow [this guide](https://medium.com/@zw3rk/quick-headless-raspberry-pi-setup-52ad6dd312c4)
  to get SSH enabled on first boot. TL;DR: `touch /media/$USER/boot/ssh`.

## Initial setup

- Plug the Pi in and connect it to a DHCP-enabled router via ethernet.
- My old DD-WRT had the `.lan` domain, so I could directly connect to
  `raspberrypi.lan`. Obviously, take care of conflicts if you have more than
  one. `nmap` is your friend.
- `ssh pi@raspberrypi.lan`, password is `raspberry`.
- This could potentially be part of the ansible script, but I prefer pushing the
  SSH key over manually to minimize the chance of locking myself out.
- `ssh pi@raspberrypi.lan "mkdir -p ~/.ssh/"; scp ~/.ssh/id_rsa.pub pi@raspberrypi.lan:/home/pi/.ssh/authorized_keys`
- *Dont forget this one:* Resize your partition or you're gonna have a bad time.
  Raspbian only leaves you with a few hundred megabytes left, so run
  `sudo raspi-config` and select option 1: "Expand Filesystem".

## Ansible

Copy `playbook.yml.example` to `playbook.yml` and make the necessary adjustments,
notably add your premiumize.me credentials to it. Afterwards, you can run it
like:

```
ansible-playbook -i hosts playbook.yml
```

This assumes that the hostname in `hosts` can be resolved and you can log in
password-less via the `pi` user. It also expects the Pi to already have a
working internet connection.
