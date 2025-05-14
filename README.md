# proxmox-notes
Notes from my Proxmox cluster and related topics.

Here are some topics:

[ZFS](ZFS.md)

[Clustering](Clustering.md)

[Ceph](Ceph.md)

Corosync voting device.
Link to AJ Raspi docs.
Linux Permissions
Debian Setup
Install
VM Setup

Minimal Debian install.
Removing a Node from the cluster.
Actual removal.
Hardware
What I'm using.
Don't use this.
What you should use.
Business class switch? Ask AJ.
Proxmox Problems I've encountered and how to fix them.
Troubleshooting
Ubuntu Server Live
Using Caddy as a reverse proxy.
Security warning.
pfSense.
MacOS
Assign PVID because it doesn't like VLANs.
Here's my network interface config.
Debian network interfaces for Proxmox (/etc/network/interfaces)
Useful links
Virtual Proxmox cluster
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
CPUs
Always do 2 CPUs even if you apply a cpu limit of 1, I believe it's for garbage collection so it can use 2 threads as needed at a slower speed.
Virtual cluster
DO NOT specify a VLAN in the nested Proxmox install.
Problems
Loose or bad Ethernet cables.
Slow spec Ethernet cables.
VLANs
if an access port (PVID)  is configured in this model of netgear switch, you cannot specify the VLAN ID or it will not pass the traffic.
AJ: Or it may pass the VLAN ID through as a nested VLAN ID, which would also cause problems.


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
