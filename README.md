# proxmox-notes

This is a work in progress meant to be a companion to [AJ's excellent documentation](https://github.com/bnnanet/learn-colocation/issues/), to fill in some cracks and share what I've learned.

[PfSense Setup](https://github.com/mathwhiz1212/proxmox-notes/blob/main/PfSense.md)

[Proxmox Setup](Proxmox%20Setup.md)

[Hardware](Hardware.md)

[Clustering](Clustering.md)

TODOs:
- Corosync voting device.
- Link to AJ Raspi docs.
- Make it more readable by adding spaces and editing.

[Cluster Management](Cluster%20Management.md)

- Removing a Node from the cluster.

[Preventing Failures](Preventing%20Failures.md)

[Recovering From Failures](Recovering%20From%20Failures.md)

[ZFS](ZFS.md)

[Ceph](Ceph.md)

More I could add:

Linux Permissions
Debian Setup
Install
VM Setup

Minimal Debian install.
Actual removal.

Troubleshooting
Ubuntu Server Live
Using Caddy as a reverse proxy.
Security warning.

Useful links

Virtual node

Host cpu. Can't migrate like that unless CPUs are indentical.
Get a VM ready to cluster non-leader.
Clone, don't backup and restore. Can probably do template.
OS doesn't need much if you're using secondary drives for storage.

Minimal Debian install.

Ssh-copy-id to proxmox nested host and guests.

Useful links

Network set up.
IP addresses and hostnames for everything.

VLANs
Subnets
Domain names.

Qemu
Random commands.
Shrink a disk
shrink FS
Shrink partition
Shrink ZFS volume.
Shrink qemu disk (with some margin for GB Vs GiB).
Oveprovisioning

Problems
Loose or bad Ethernet cables.
Slow spec Ethernet cables.

2FA

WebAUTHn
OTP
Recovery keys

MacOS

Brew install zstd
Use tar to create a zst archive
tar --zstd -cf Proxmox.zst Proxmox
tar --zstd -xf Proxmox.zst -C Proxmox2 # -C being a directory to put the files or folders.
