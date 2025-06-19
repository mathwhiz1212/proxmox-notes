# Proxmox Setup

Proxmox uses `qemu` for virtual machines, and `lxc` for containers under-the-hood, with some features added on top.

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

# Network Configuration
https://github.com/mathwhiz1212/proxmox-notes/blob/main/network-configuration.md
