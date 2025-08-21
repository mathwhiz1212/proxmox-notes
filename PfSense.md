# PfSense

## Install

### Create VM

Download the PfSense ISO into your local storage.

ISO images > Download from URL > `https://atxfiles.netgate.com/mirror/downloads/pfSense-CE-2.7.2-RELEASE-amd64.iso.gz`

Click query URL > Download./

Leave the defaults and let it use gzip to decompress the ISO.

`# This (2.7.2) is the latest CE release that doesn't require an account. 2.8.x requires an account and introduces breaking changes. You can view 2.7.2 and earlier here: https://atxfiles.netgate.com/mirror/downloads/`

Right-click on your node and click "Create VM".

Node: The main one you want to use.
VM ID (something at the beginning of a range you're not using for anything but networking): 
e.g. 4000
Name: pfsense1
Advanced > Start at Boot (check the box)
Tag: mgmt

Select your ISO storage and the `pfSense-CE-2.7.2-RELEASE-amd64.iso` file we downloaded earlier. Click next.

Click next on the system tab to accept the defaults.

On Disks, select 8GB unless you plan to do more intense logging.

NOTE: It is easier to expand than shrink virtual disks.

Check `Discard` and `SSD emulation`

You may need to check `Advanced` to see this. 

Leave the other defaults. Don't mess with backups or replication unless you are 100% sure you know what you are doing and that it won't break backups or replication.

Click next.

Under CPU, give it 2 cores.

You can adjust the type to the lowest version your oldest CPU in the cluster supports for best performance: https://qemu.readthedocs.io/en/master/system/qemu-cpu-models.html

It will probably be leaving performance on the table if your hardware is made in the last decade. I have an 11 year old computer that supports v3, but my 12 year old computer only supports v2 so I'll leave it at default v2-AES. 

If you have identical CPUs through the entire cluster, and any backups you might deploy in an emergency have the same CPU, you can use `host` for best performance.

If you choose the wrong `type` it will break moving VMs between nodes, and replication. If you're unsure, you can just leave the default `x86-64-v2-AES` value.

Click next.

PfSense doesn't need much RAM if you aren't expecting a lot of traffic and you're not using an IDS or IPS.

Memory: 1024MB
Uncheck ballooning device. This is normally a good thing, but for this use case, we're leaving it off.

Network

We're using our `vmbr0` Linux bridge and our WAN network VLAN (500 in this case).

Uncheck firewall, or you might issues receiving traffic.

Click next. Then finish.

It will show a ? then a green arrow when it is done creating itself.

Click on the `pfsense1` VM and the options tab. Scroll down and double-left click on `protection`, check enabled, and click ok.

This helps prevent against accidental deletion because you *do not* want to accidentally delete your router.

# Creating Virtual Network Interfaces

Click on pfsense 1 > hardware > add > network device.
Bridge: vmbr0
VLAN tag: 400 (this is the maangement network for PfSense and Proxmox).
Uncheck firewall.

add > network device.
Bridge: vmbr0
VLAN tag: (leave empty for VNets)
Uncheck firewall.

Repeat as needed. Remember to uncheck the firewall on each interface you create. PfSense IS a firewall and we want traffic to be able to get to it.

I like to have:

1 interface for the WAN like we created in the install
1 managment interface
1 interface that I can use for all the Proxmox VNets I may add to PfSense later. This interface should **not** have a VLAN tag.

#### Why Create multiple interfaces?
Good practice. In a production enviroment, you may have a bridge for each physical interface, and you can tie each virtual interface to a physical interface, avoiding the need to share the physical interface with other traffic.

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

DNS servers `1.1.1.1`, `9.9.9.9`. It will default to PfSense's default DNS and fall back on remote DNS as needed.

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

# Enable DHCP server.

Services > DHCP server. Click on the notice to switch to Kea DHCP. Scroll down and click save.

Go back to Services > DHCP server. Click on the LAN interface.

Enable DHCP on the LAN Interface.

Input the DHCP range .100-.199

DNS Servers: Leave the first line 172.20.0.1, 1.1.1.1, 9.9.9.9, and 8.8.8.8.

Scroll down and click save. Apply changes at the top.

### VLAN Interface (Guest Network)

Interface > assignments > VLANs

Add > Enter VLAN tag.

Go back to assignments and select the interface that has that VLAN tag under VLAN [Tag number] on [Physical interface name].

Add the interface with the VLAN, click on the new interface that created.

Enable interface.

Description: `GUEST1`

Static IPv4.

IPv4: `10.0.0.0/16`

Save.

### Enable DHCP

# Enable DHCP server.

Services > DHCP server.

Click on the GUEST1 interface.

Enable DHCP on the GUEST1 Interface.

Input the DHCP range .100-.199

DNS Servers: Leave the first line 10.0.0.1, 1.1.1.1, 9.9.9.9, and 8.8.8.8.

### Firewall Rules

Interface rules and floating rules with the Quick (apply action immediately on match) are applied top to bottom.
Floating rules apply before interface rules.

Floating > Add >

Allow ping from any subnet: 

Pass, check apply the action immediately on match, any interface, protocol (ICMP echo request), source, destination - This Firewall (self). Description: `Allow ping from any inteface to this firewall.`

Allow access to this firewall for DNS from anything but my home network (WAN):

Pass, check apply the action immediately on match: Source: Invert match `WAN Subnets`, destination `This Firewall (self)`, protocol: TCP/UDP, port 53 (DNS). Description: `Allow DNS from any interface except the WAN (home) network.`

Block inter-guest traffic:

Block, Apply action immediately, any protocol, source `10.0.0.0/16`, destination `10.0.0.0/16`. Description: Block Inter-Guest Traffic.

Save (at bottom) and Apply (at top).

### A better way (edit this into the other part).

Test that it blocks inter-guest traffic too.

New Alias:

PRIVATEIPs	Network(s)	10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16	RFC1918 Private Networks 

New rule on GUEST1 allow to all destination: (invert) PRIVATEIPs

Can create rules above this for exceptioms.

enable vm firewall to block inter-guest traffic that doesn't go through the router. Anytime you have a physical or virtual switch.

### LAN/Mgmt

By default, the anti-Lockout Rule should be enabled, as well as default to any IPv4 and IPv6 rules.

### GUEST1

Click on GUEST1. We want to deny access to other subnets so the guests can't access them.

Block, Source: GUEST1 Subnets, Destination: LAN subnets. Destination port range: any.

Description: `Block traffic to LAN (172.20.0.0/16).`

Block, Source: GUEST1 Subnets, Destination: WAN subnets, Destination Port Range: any.

Description: `Block traffic to WAN (192.168.1.0/24).`

And repeat for any other subnets you don't want the guest network to access.

Disable allow any to IPv6 if it exists.

Leave the allow to any IPv4 at the bottom.

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
