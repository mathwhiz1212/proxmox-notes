# Proxmox Setup

## Install

Can use a RAIDz0 ZFS filesystem on a single OS drive if you don't have more than one for that purpose.

If you plan to use the OS drive to store virtual machines, and plan to use ZFS replication, you will want to use ZFS instead of another filesystem.

On consumer SSDs in a home lab, it's best to set the `hddsize` in the formatting settings to half the physical size of your drive to allow for better wear leveling if you care about longevity.

Server-class SSDs should work just fine without this.

## Configuration

Setup script (that configures the official free no-subscription beta repository):

```
wget https://git.coolaj86.com/josh/proxmox-scripts/raw/branch/main/Proxmox/proxmox-setup.sh
chmod +x proxmox-setup.sh
./proxmox-setup.sh
```

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

### pfSense

# Install



# Configuration
