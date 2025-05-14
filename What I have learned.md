Migrating virtual nodes or any VMs.
Set the CPU to the lowest common denominator.
And I truly identical hardware configuration you could set the CPU type to host in this configuration and it would work at maximum performance.
Creating an overly large OS disc for the samba server.
Had to resize the operating system desk a couple times and create a new disc then copy the files over.
Resizing involves resizing the file system, then the partition, then the ZFS volume, then they can you disc size with some margin, It seems like the margin is necessary perhaps because of a gigabyte versus gibibyte problem.
I know being shut down before a correspondent configuration change could be completed.
Stop the corosync and pve-cluster services.
Put the cluster file system in local mode.
Edit the corosync  file to exactly match the other ones.
Reboot the node.
Yes I wanted to change the cluster configuration I would increment the configuration number further after stopping the service on all of the nodes in the cluster and probably manually making that change to all nodes.
If I understand correctly this would be necessary.
Physical node problems.
Switch forgot a config change after I unplugged and replugged it, locking the corosync voting device out of the VLAN it was using to communicate with the Proxmox Nodes.
Node renamed networking device after the cable was unplugged and replugged.
The combination led to a failed cluster.
Turning on Ceph replication while ZFS replication is still enabled doesn't work.
HA will still work, but VM migrations will fail.
Clustering
Make sure that you are happy with and you have decided on naming schemes for your downloads and IP addresses and storage devices and ZFS pools because once you cluster you want them all to be named the same and you're not changing it. It's probably easier to start over from scratch than to rename something.

More things I did after learning through AJ
Switch
VLAN
pfSense
Separate VLAN and subnet for the management network, WAN, and Guest networks.
Firewall rules.
Port forwarding.
A bit weird because the LAN-is-WAN.
Guest network
Isolated network with its own VLAN, SDN and firewall rules in pfSense and Proxmox.
Samba Server with encryption.
Virtual Proxmox cluster.
HA groups.
ZFS replication, then Ceph replication on 4 nodes with 4x50GB drives each (16 total OSDs)
Ceph doesn't run well on hard drives, gigabit networking, and 3.5-4GB RAM, but it does run at 17-25MB/s while rebalancing
SSD emulation and discard because it's running on top of ZFS.
qemu-agent for monitoring.
qm set VMID --vpu host
qm unlock VMID.
Removed a node.
ZFS tweaks
zfs set atime=off rpool
On physical nodes:
See SSD reduce writes script. Add: do not use in production.
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
