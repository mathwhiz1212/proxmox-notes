# PfSense

## Install

### Create VM
```
Download the PfSense ISO into your local storage.

ISO images > Download from URL > https://atxfiles.netgate.com/mirror/downloads/pfSense-CE-2.7.2-RELEASE-amd64.iso.gz

Leave the defaults and let it use gzip to decompress the ISO.

# This (2.7.2) is the latest CE release that doesn't require an account. 2.8.x requires an account and introduces breaking changes. You can view 2.7.2 and earlier here: https://atxfiles.netgate.com/mirror/downloads/

Right-click on your node and click "Create VM".

Node: The main one you want to use.
VM ID (something at the beginning of a range you're not using for anything but networking): 4000
Name: pfsense1
Start at Boot (check the box)
Tag: mgmt
```

Select your ISO storage and the `pfSense-CE-2.7.2-RELEASE-amd64.iso` file we downloaded earlier. Click next.

Click next on the system tab to accept the defaults.

On Disks, select 8GB unless you plan to do more intense logging. NOTE: It is easier to expand than shrink virtual disks.

Check `Discard` and `SSD emulation` (you may need to check `Advanced` to see this. Leave the other defaults, and especially leave backups checked and DO NOT check skip replication. It is needed for ZFS replication-backed high-availability setups.

Under CPU, give it 2 cores.

You can adjust the type to the lowest version your oldest CPU in the cluster supports for best performance: https://qemu.readthedocs.io/en/master/system/qemu-cpu-models.html

In absolutely identical CPUs through the entire cluster, you can use `host`.

Choose the wrong `type` will break moving VMs between nodes, and replication, so if you're new, you can just leave the default `x86-64-v2-AES` value.

Memory: 1024MB
Uncheck ballooning device. This is normally a good thing, but for this use case, we're leaving it off.

Network

We're using our `vmbr0` Linux bridge and our WAN network VLAN (500 in my case).

Uncheck firewall.

Click next. Check "Start on creation".

Click on the `pfsense1` VM and the options tab. Double-left click on `protection`, check enabled, and click ok.

This helps prevent against accidental deletion.

# Creating Virtual Network Interfaces
Click on pfsense 1 > hardware > add > network device.
Bridge: vmbr0
VLAN tag: 400 (this is my maangement network for PfSense and Proxmox).
Firewall: Uncheck this.

Repeat as needed. I like to have 1 interface for the WAN like we created in the install, 1 managment interface, and 1 interface that I can use for all the Proxmox VNets I put in PfSense later. That last interface should **not** have a VLAN tag.

Remember to uncheck the firewall on each interface you create. PfSense IS a firewall and we want traffic to be able to get to it.

# Installing PfSense
Double-click on the VM name `pfsense1` to open a new VNC window.

Accept the aggreement by hitting enter.
Select Install and hit enter.
guided-ufs disk setup. GPT partition table.

Do manual install, or disable swap afterward in `/etc/fstab` by commenting out #. Can use `nano` `pkg install nano`

`# We're not doing ZFS because ZFS on ZFS is not good.`
Finish
Commit.
Reboot.

## Initial Interface Configuration

When you first boot up, it will ask you to configure your network interfaces. You can skip VLAN configuration for now. We'll do that in the web interface later, you can acces the internet (WAN) and the management network and that's all we need to start.

Say no to VLANs - type `no` and hit enter.

Enter the WAN interface name, probably `vtnet0` if you followed my instructions above.

Then the LAN interface name, probably `vtnet1` if you followed my instructions above.

If you added an optional interface for other VNets, you will be prompted to input that interface name, likely `vtnet2` if you followed how I normally do things, but check for which interface doesn't have a VLAN tag to be sure.

Type y and hit enter to proceed.

# IP Address Config

When you see `Welcome to PfSense` and a list of interfaces, hit 2 to set IP addresses.

I'll do 1 to select the WAN interface. n to DHCP.

I'll do `192.168.1.204/24` because this sits on my home internet network. Make sure this won't conflict with an other IP addresses on your network.

`192.168.1.1` is the default upstream gateway for this network.

I will hit `y` and enter to add this as the default gateway.

y to IPv6 DHCP.

n to DHCP on WAN. I don't want PfSense giving out IPs on my home network.

n to reverting to HTTP.

### GUI setup.

Get on the same WAN subnet as PfSense and go to the IP you just set for the WAN interface.

It will give you a security warning because PfSense is using a self-signed certificate.

Click advanced > Proceed unsafe.

Enter:

username: admin
password: pfsense

Next > Next

hostname: pfSense1
domain: homelab.lan

DNS servers `1.1.1.1`, `9.9.9.9`.

Enter timezone and leave NTP server as-is.

You may have to put the upstream gateway again.

Leave the other settings as default and hit next.

Input 172.20.0.1 and 16 for the subnet mask.

Enter the admin password for PfSense, then do so again.

Reload.

Finish.

# Create LAN interface

Use the LAN CLI instructions below if you can't access PfSense on the WAN network.

Interfaces > Assignments > LAN

Enable interface.

LAN - mgmt

Static IPv4.

IPv4 address 172.20.0.1.

Save.

### Enable DHCP server.

Services > DHCP server. Click on the notice to switch to Kea DHCP. Scroll down and click save.

Enable DHCP on the LAN interface.

Input the DHCP range .100-.199

Scroll down and click save. Apply changes.

`172.20.0.1`, `1.1.1.1`, and `9.9.9.9` on DNS server in setup. It will default to PfSense and fall back on remote DNS.

On DHCP you can use DNS Servers: `1.1.1.1`, `9.9.9.9`, and `8.8.8.8`.

### CLI LAN setup.

Hit 2 and 2 to setup the LAN interface.

`n` to DHCP.

We'll use `172.20.0.1/16` for the LAN interface IP.

Hit enter without entering a gateway because this is a LAN interface.

y to IPv6 DHCP.

y to DHCP on LAN.

Start: `172.20.0.100`
End: `172.20.0.199`

n to HTTP

## Troubleshooting

Reboot the PfSense VM. If your network is broken, you may need to do this using the network switch that PfSense is plugged into. Depending on your VLAN configuration on that switch, you may need to figure out which port is a managmenet port (you documented that, right?)

If you can ping an IP, but cannot otherwise access it, try checking your firewall rules in PfSense and your subnet mask on EVERYTHING. I've had misconfigured subnets on something in the chain mess up communication a few times recently.

## Dynamic DNS

Go to pfsense > Services > Dynamic DNS

Select Add

Service type `Namecheap`

Interface `WAN`

hostname: `lab` `joshmudge.com` (`lab` in the first box, `joshmudge.com` in the second, for `lab.joshmudge.com`).

If you want to do a sub-sub-domain, do: hostname: `pve1.lab` `joshmudge.com` (`pve1.lab` in the first box for `pve1.lab.joshmudge.com`)

Grab your dynamic DNS password from your Advanced DNS page for your domain name on Namecheap and input it in the password field.

Add a description and hit save.

Use the duplicate icon to duplicate the Dynamic DNS updater for other domains and change the domain name and description as needed.

You will need to make sure the record you want to update is `A + Dynamic DNS` and not just an `A` record.

**Backup your PfSense configuration to a secure location.**

## Backup

Go to `PfSense > Diagnostics > Backup and Restore`

Check the box for `Do not backup package information.` and make sure `Do not backup RRD data` is checked. You probably want SSH keys to be checked.

You can, and probably should, encrypt the file if the storage you are saving it to is not encrypted itself.

Save the `.xml` backup file somewhere safe, perferably an encrypted archive you have easy access to in an emergency.

NOTE TO SELF: Update with my docs.

# Extra FYIs

Use consistent subnets on client and router config until you have a known good working configuration.

Proxmox DNS settings affect VMs default DNS.

Don't change the MAC address for any PfSense interface while it is running. Learned that the hard way.

When you create a new network interface or VM Proxmox, it defaults to the `BC:24:11` MAC  address prefix, the "vender prefix" for Proxmox: https://www.macvendorlookup.com/search/BC:24:11:00:00:00

To isolate Guest VMs from each other, you have to turn on the VM firewall because they are not accessing each other through the PfSense router, but through a virtual switch.
https://docs.google.com/document/d/14MXJ6jj2wq-zlGDdJentC4fKeSjKSJmE7h0zzJzOzVA/edit?tab=t.0
