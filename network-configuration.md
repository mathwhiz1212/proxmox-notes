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

### Interfaces (/etc/network/interfaces)

```
auto lo
iface lo inet loopback

iface enp3s0 inet manual

auto vmbr0
iface vmbr0 inet manual
	bridge-ports enp3s0
	bridge-stp off
	bridge-fd 0
	bridge-vlan-aware yes
	bridge-vids 2-4094

auto mgmt
iface mgmt inet static
	address 172.18.0.210/24
	gateway 172.18.0.1
	vlan-id 400
	vlan-raw-device vmbr0

source /etc/network/interfaces.d/*
```
### Troubleshooting

If you don't have internet after this configuration, check your VLAN configuration, you'll need the server PVID to be a trunk port.

### [pfSense](PfSense.md)
