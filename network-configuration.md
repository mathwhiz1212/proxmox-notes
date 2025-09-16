# Networking

It is much easier to configure networking before clustering.

## Hardware

You'll need a managed switch to configure VLANs. I'm using this one: https://www.amazon.com/NETGEAR-8-Port-Gigabit-Ethernet-Switch/dp/B0D9W9YNWD/

And this looks to be a good deal for 5 shielded ethernet cables ($20-25 = $4-5 each): https://www.amazon.com/gp/product/B00HEM5IWC/

## VLANs

Understanding VLANs: https://github.com/bnnanet/learn-colocation/issues/24

I'm using VLAN 400 for the management  network (172.18.0.0/16), 500 for the WAN-as-LAN interface (192.168.0.0/24), and 1001 for the guest network (10.0.0.0/8).

An example production configuration: https://github.com/bnnanet/learn-colocation/issues/18

If an access port (PVID) is configured, you generally cannot specify a VLAN ID on the device itself or it will not pass the traffic, or it will pass it as a nested VLAN ID, which would also cause problems.

It is best to give MacOS devices a PVID because they tend not to play nice with VLANs otherwise.

The Proxmox nodes themselves should be given trunk ports.

# Proxmox Node Network Interface Configuration

Create these interfaces in Proxmox under `Node > Network` (do not apply until we're done):

vmbr0 > Edit > Remove gateway and IP.
```
Create > mgmt > Linux VLAN > 172.18.0.230/24 (the Proxmox node IP).
Gateway 172.18.0.1
Vlan raw device (the Linux bridge): vmbr0
VLAN tag: 400
```

Leave the physical interface alone.

Simultaneously in an SSH connection to the Proxmox node and in the interface, do the following:

In SSH: `sleep 30; reboot`

In Proxmox: Click apply configuration.

# PfSense Configuration

See [PfSense Setup](https://github.com/mathwhiz1212/proxmox-notes/blob/main/PfSense.md)

# Advanced Configuration

## VNets

* Create VNet.
* Add network to PfSense.
* Add Proxmox VNet and Node firewall rules.
* Add to any VMs you want to use the VNet.

## Creating a Linux Bond

"Bonding" 2 or more physical interfaces allows for redundancy and load balancing between interfaces. 

After plugging in the NICs you want to use to your switch(es), follow these instructions:

Create the bond (example config below).

Use `balance-tlb` if you don't know what link aggregation config your switch supports because it will work without any switch configuration and has faster failover than `balance-alb`.

`enp7s0` (internal) and `enp4s0f1` (PCIe NIC) are the interfaces I am using for this.

```
iface enp7s0 inet manual
  bond-master bond0
  bond-mode balance-tlb
iface enp4s0f1 inet manual
  bond-master bond0
  bond-mode balance-tlb  

# This is copy and paste from Server World's config and may not be accurate/good for a specific config.
  auto bond0
  iface bond0 inet static
    bond-slaves enp7s0 enp4s0f1
    bond-mode balance-tlb
    bond-miimon 100
    bond-downdelay 200
    bond-updelay 200
```

Then edit your `vmbr0` bridge interface to use `bond0` instead of a physical interface like `enp7s0`:

```
auto vmbr0
iface vmbr0 inet manual
	bridge-ports bond0
	bridge-stp off
	bridge-fd 0
	bridge-vlan-aware yes
	bridge-vids 2-4094
```

Then reboot using `reboot` for the changes to take effect.

`ifreload -a` reloads all `auto` interfaces. It might work, but I haven't tested that approach.

More resources:

Server World: http://server-world.info/en/note?os=Debian_12&p=bonding&f=1

Proxmox: https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysadmin_network_bond

Red Hat docs that say which bonding tech requires which switch config: https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/configuring-network-bonding_configuring-and-managing-networking#upstream-switch-configuration-depending-on-the-bonding-modes_configuring-network-bonding

alb vs tlb: https://serverfault.com/a/739550

Ubuntu config in case you want to bond interface on a non-Proxmox system: https://www.server-world.info/en/note?os=Ubuntu_22.04&p=bonding

# Troubleshooting network interfaces.

`reboot` of the node is your friend.

`ip a` lets you see all the network interfaces and their status `LOWER_UP` is what you want for interfaces that are plugged in and/or virtual interfaces that are supposed to be in use.

`ping -I mgmt [IP]` allows you to ping an IP address using the `mgmt` interface specifically.

`ifup [Interface Name]` brings an interface up (assuming a valid configuration exists). This does not persist after a reboot.

`ifdown [Interface Name]` brings the interface down. This does not persist after a reboot.

`ifreload -a` reloads all `auto` interfaces.

`ifreload -c` reloads all currently UP interfaces.

Adding the `-u` flag to `ifreload` commands forces the use of the current `/etc/network/interfaces` file instead of the saved state.

# 1 physical NIC with 2 IPs (not the sensible VNet/VLAN way, but on the node itself, don't do this).

If you want to have 2 IP addresses on the same physical network interface on your Proxmox nodes, it is considered suboptimal, but there is a way to do it.

If you just want a different subnet, create a subnet in PfSense, or a VNet in Proxmox (or both), don't do this.

It is preferable to buy a NIC rather than do this. You can get cheap 1-port NICs, or used 4-port server NICs.

This is NOT how to use different networks on VMs. On VMs, you can just specify a different IP address and VLAN ID (or the VNet instead of the VLAN ID if the VNet has a VLAN configured) and that should work fine.

This is NOT how to give PfSense different IP addresses, those IP addresses are created within the PfSense mangamenet interface (probably under Interfaces and Interfaces > VLANs on PfSense). You **will** have to do that **before** you configure the nodes.

If you want the node itself to have more than one IP address on a single NIC, you can do the following:

WARNING: If you do this, you can never edit any network interfaces on that node in the Proxmox GUI again. Failing to remember that fact will lead to all but one (probably the last listed) IP to be deleted, and VLAN configurations to be broken.

In `/etc/network/interfaces`:

```
# Original normal configuration.

auto mgmt
iface mgmt inet static
        address 172.18.0.210/24
        gateway 172.18.0.1
        vlan-id 400
        vlan-raw-device vmbr0

# Second IP address.
iface mgmt inet static
        address 172.19.0.210/24
        vlan-id 600
```

Save, then run: `ifdown mgmt && ifup mgmt` to restart the interface and test it by pinging it with another computer on the same subnet. This may not work as expected until you have 2 proxmox nodes or other non-router devices running on the second subnet.

My router is configured to always accept ICMP pings so the ping behavior is the same regardless of whether things are working right or not.

These instructions were taken and modified from: https://wiki.debian.org/NetworkConfiguration#Multiple_IP_addresses_on_one_Interface
