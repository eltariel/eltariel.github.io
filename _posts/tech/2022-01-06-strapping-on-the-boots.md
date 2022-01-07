---
layout: post
title: Strapping On the Boots
tags: tech pi k8s clu bash ansible k3s
---

Part two of my journey into a tasty cluster of Pi is to actually get the software up and running. It's pretty easy to
flash an SD card, but that means you end up with a bunch of devices on the network, all of which are going to hit your
DHCP server with the same hostname. Then you have to go and do a bunch of configuration, setting hostnames and passwords
and connecting to wifi etc etc.

There's a little hidden feature in the Raspberry Pi Imager which lets you customise an image after you write it. Pressing
`ctrl-shift-X` (or `cmd-shift-X` on a mac) will pop up a little dialog that lets you set some configuration for the OS.
Once the image has been written:

- the `/boot` partition on the SD card is mounted
- a script called `firstrun.sh` is generated from the variables and written to the disk
- the kernel commandline is updated in `cmdline.txt`, telling the pi to run the script

This is pretty neat, it means I can get a slightly more secure setup for the pi, complete with a wifi connection. However
it's still tied to the GUI and you can't automate it particularly easily. So... I built my own version of it.


# Hooray, yet another Raspberry Pi bootstrapper...

Ok, so it's probably a bit of overkill. But every time I write an image, I have to update the hostname, change the username
from `pi` to something else, set passwords, connect to wifi, etc etc etc. And I do this often enough that it's worth automating,
but the 'easy' options either aren't particularly flexible, or boil down to "use Ubuntu instead because it's got `cloud-init`".
Doing things this way essentially lets me run whatever I want on first boot, in a language I'm familiar with, with no dependencies
beyond what's already in the image.

It's on my github here: [pi-firstrun](https://github.com/eltariel/pi-firstrun).

I've spent a bit of time getting this all working, and it's still a little rough around the edges, but it currently does the
following:

- Sets the hostname based on the pi's ethernet MAC address
- Sets up the wifi to connect to my home network
- Fixes up the `pi` user:
  - renames it and fixes the `sudo` entry
  - sets a password
  - writes an `authorized_keys` file
- Makes the SSH server only accept pubkey auth

And now, we're ready to do stuff with it.

_*phew.*_


# Making it Kube-shaped

It wasn't a hard decision to pick k3s for this project. Realistically I could have gone with just about any k8s flavour, but
this one is pretty lightweight and it's well suited to running on a Raspberry Pi. Or lots of them. The setup instructions in the
official documentation are pretty heavy on the bash commands though, and I wanted this to be _easy_ so I could throw it away and
start again when I invariably mess up...

Turns out that there's a semi-official ansible playbook on github at [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible)
which works pretty well, and even handles some of the quirks of running it on a Raspberry Pi - at least if you're using the old
Buster version. There's a few little things about it that don't quite work on the current Bullseye release though, which is a
minor annoyance at best, and not particularly difficult to fix. The biggest issue is that Raspberry Pi OS has been moving towards
using the new and shiny `nftables` in preference to the old `iptables`, but k3s (and docker for that matter) don't support it.
Buster used to install both by default, and it was simple to switch between them, but it's missing on Bullseye. One minor patch
later, and my local copy of the ansible playbook now does a full system update and then installs `iptables` before moving on to
the rest of the setup. I've also added a task to copy `.kube/config` from the control node as well.

So now that I've got my lovely little fleet of pre-customised Raspberry Pi boards, all I need to do is add them to the ansible
inventory and run this:

```bash
$ ansible-playbook site.yml
```

Now we wait. It took my little cluster about three minutes to finish doing the system updates and the other little tasks needed on 
Raspberry Pi OS, and then another three to install k3s on the cluster. And now we can just put the kube config somewhere useful and
check if everything worked:

```bash
$ cp -r k3s-cfg/clu01/home/ellie/.kube ~
$ sed -i 's/127.0.0.1/clu01/' ~/.kube/config
$ kubectl cluster-info
```

```
Kubernetes control plane is running at https://clu01:6443
CoreDNS is running at https://clu01:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://clu01:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Success!

Next time... I might do something useful with this!
