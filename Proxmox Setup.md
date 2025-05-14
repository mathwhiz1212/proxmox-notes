# Proxmox Setup

Setup script (that configures the official free no-subscription beta repository):

```
wget https://git.coolaj86.com/josh/proxmox-scripts/raw/branch/main/Proxmox/proxmox-setup.sh
chmod +x proxmox-setup.sh
./proxmox-setup.sh
```

## Networking

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
