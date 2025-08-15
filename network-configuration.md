## Networking

It is much easier to configure networking before clustering.

### VLANs

Understanding VLANs: https://github.com/bnnanet/learn-colocation/issues/24

You'll need a managed switch to configure VLANs. AJ recommends this switch: https://www.amazon.com/NETGEAR-Gigabit-Lifetime-Protection-GS105Ev2/dp/B00HGLVZLY

I'm using VLAN 400 for the management  network (172.18.0.0/16), 500 for the WAN-as-LAN interface (192.168.0.0/24), and 1001 for the guest network (10.0.0.0/8).

An example production configuration: https://github.com/bnnanet/learn-colocation/issues/18

If an access port (PVID) is configured in this model of Netgear switch, you cannot specify the VLAN ID on the device itself or it will not pass the traffic.

AJ: Or it may pass the VLAN ID through as a nested VLAN ID, which would also cause problems.

It is best to give MacOS devices a PVID because they tend not to play nice with VLANs otherwise.

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

Run in the SSH connection: `sleep 30; reboot`
In Proxmox: Click apply configuration.

# Advanced Configuration

If you want to have 2 IP addresses on the same physical network interface on your Proxmox nodes, it is considered suboptimal, but there is a way to do it.

This is NOT how to use different networks on VMs. On VMs, you can just specify a different IP address and VLAN ID (or the VNet instead of the VLAN ID if the VNet has a VLAN configured) and that should work fine.

This is NOT how to give PfSense different IP addresses, those IP addresses are created within the PfSense mangamenet interface (probably under interfaces and interfaces > VLANs on PfSense). You **will** have to do that **before** you configure the nodes.

If you want the node itself to have more than one IP address, you can do the following:

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

Save, then run: `ifdown mgmt && ifup mgmt` to restart the interface and test it by pinging with another computer on the same subnet. This may not work as expected until you have 2 proxmox nodes or other non-router devices running on the second subnet.

My router is configured to always accept ICMP pings so the ping behavior is the same regardless of whether things are working right or not.

These instructions were taken and modified from: https://wiki.debian.org/NetworkConfiguration#Multiple_IP_addresses_on_one_Interface
