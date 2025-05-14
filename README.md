# proxmox-notes

AJ has excellent Proxmox documentation here: https://github.com/bnnanet/learn-colocation/issues/

Here is mine:

[Proxmox Setup](Proxmox%20Setup.md)

[Hardware](Hardware.md)

[Clustering](Clustering.md)

Corosync voting device.
Link to AJ Raspi docs.

[Cluster Management](Cluster%20Management.md)

Removing a Node from the cluster.

[Preventing Failures](Preventing%20Failures.md)

[Recovering From Failures](Recovering%20From%20Failures.md)

[ZFS](ZFS.md)

[Ceph](Ceph.md)

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
pfSense.

Useful links

Virtual node

Host cpu. Can't migrate like that.
Get a VM ready to cluster non-leader.
Clone, don't backup and restore. Can probably do template.
OS doesn't need much if you're using secondary drives for storage.

Minimal Debian install.
ACME w/Caddy reverse proxy.
Proxmox built-in requires port 80 port forwarding. Maybe Caddy too?
Always manually specify caddyfile when restarting or you will be confused.
Linux tools
Ssh-copy-id to proxmox nested host and guests.
Lshw
hwinfo
iotop
htop
screen
smartmontools (Proxmox comes with).
stress
Useful links
HP partsurfer hp only.
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

MacOS tweaks
Give it a PVID because it doesn't like VLANs.

Brew install zstd
Use tar to create a zst archive
tar --zstd -cf Proxmox.zst Proxmox
tar --zstd -xf Proxmox.zst -C Proxmox2 # -C being a directory to put the files or folders.
