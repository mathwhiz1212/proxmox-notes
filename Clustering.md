# Clustering

# Hardware

At least 3 nodes for ZFS replication, or at least 4 nodes for Ceph replication.

You can use 2 nodes + a voting device (like a raspberry pi) in a home lab, but you'll have less redundancy.

If you don't have the physical hardware (say, number of devices) to do a physical Proxmox cluster, you can do a virtual cluster for testing (see notes below if you want to do that).

# Quorom

For the cluster to work, you need a quorum, a majority of nodes that are in "good" condition without hardware or network failure.

If you don't have a Quorom, the cluster will fail (no VMs or containers running). Here is what you can lose and still have a functioning cluster:

If you have 2 nodes and a voting device, you can lose a node OR the voting device, but not both.

3 nodes, you can lose 1.

4 nodes, you can lose 1.

4 nodes + a voting device, you can lose 2 nodes or 1 node and the voting device.

5 nodes, you can lose 2 nodes.

## Prep the leader

## Prep the followers

## Virtual Proxmox Cluster

DO NOT specify a VLAN in the nested Proxmox install, the Proxmox node VM needs the VLAN specified, but the VMs underneath it shouldn't have VLANs specified.

You can create virtual network interfaces to isolate VMs on different networks.

Raspi pre-cluster
Qdpi1
172.18.0.239
Had to configure VLAN 400 PVID.
pfSense
Is on local-zfs
Install 64-bit Debiain on a raspberry pi 3.
https://github.com/bnnanet/learn-colocation/issues/14
Install locales instead of locale
Apt remove fish (if you don’t use fish)
You may want to enable root login temporarily in /etc/ssh/sshd_config after installing openssh-server
I did ssh-authorize early so I could run commands vis SSH easier.

# Network Interfaces
nano /etc/network/interfaces.d/eth0
auto eth0
#iface eth0 inet dhcp
#iface eth0 inet6 auto
iface eth0 inet static
  address 172.18.0.239
  gateway 172.18.0.1

In screen
ifdown eth0 && ifup eth0
Connect on new IP
Ssh root@172.18.0.239
apt upgrade -y
On voting device (raspberry pi):
apt-get install -y corosync-qnetd
On cluster nodes:
apt-get install -y corosync-qdevice

On each zpool
zfs set atime=off hardpool # turn off atime.
Did SSD optimizations for ZFS.
Is ashift hidden because it’s a mirrror?
hardpool  ashift                         0                              default
rpool     ashift                         12                             local



Disk for OS, another disk for everything else.
LVM for OS?

Pre-cluster:
Finalize your node name
nano /etc/hostname
nano /etc/hosts
Add ALL nodes to /etc/hosts
172.18.0.210 pve1.lab.joshmudge.com pve1
172.18.0.220 pve2.lab.joshmudge.com pve2
Hostname vmpve1 will set the hostname without the need for rebooting.
Finalize your node IP
nano /etc/hosts
nano /etc/network/interfaces
ONLY create VM / LXC instances on the leader
If you change the leader’s hostname, also update the VM config files to match.
Update to the latest versions of all-the-things
apt update & pveupgrade on all.
reboot.
/etc/network/interfaces correct IP
Physical cluster
210 pve1
220 pve2
Virtual cluster
150 vmpve1
151 - caddy VM
160 vmpve2
170 vmpve3

Copy authorized_keys to all nodes.
Should already have me, might need to add each other.
It will automatically add authorized keys as the cluster grows, it's just the initial setup where you need to add the keys.
Don't they regen keys when creating the cluster?
Ran ssh root@IP for each node to add them to known_hosts
Check
https://pve.proxmox.com/wiki/Cluster_Manager#_requirements
Make sure time is synced.
https://github.com/bnnanet/learn-colocation/issues/14
QDevice setup is here. Flashing Debian on a raspberry pi for ease of configuration is here.
Clustering
Make sure ALL of the node storage names and ALL of the node network interfaces have the same names before you cluster.


On leader/first node:
Datacenter > Cluster > Create Cluster
pvelab
Choose a network interface.
Can add fallback interfaces.
Add nodes to the cluster
Go to a node you want to add to the cluster and run:
Pvecm add CURRENTNODEIP (IP of a node already in the cluster)
Pvecm status
Pvecm nodes

Migrating virtualized proxmox nodes.
Shut down VM.
Set CPU to x86-64-v2 AES
Migrate
Then run this command on the new node:
qm set VMID --cpu host
Virtualized cluster
Clone
Change MAC address
Change /etc/hosts and /etc/hostname and add to cluster in interface.
/etc/network/interfaces to change IP.
Change /etc/default/grub timeout.
Reboot
Migrating backups to a new node
scp root@172.18.0.210:/hardpool/vz/dump/vzdump-qemu-10003-2025_04_27-02_20_43.vma.zst /hardpool/vz/dump/

Having issues adding nodes with 2FA enabled:
Establishing API connection with host '172.18.0.150'
TASK ERROR: TFA-enabled login currently works only with a TTY. at /usr/share/perl5/PVE/APIClient/LWP.pm line 121
CLI gives:
Please enter superuser (root) password for '172.18.0.150': *************
Establishing API connection with host '172.18.0.150'
500 Can't connect to 172.18.0.150:8006 (hostname verification failed)

Or GUI:
Log in to the web interface on an existing cluster node. Under Datacenter → Cluster, click the Join Information button at the top. Then, click on the button Copy Information. Alternatively, copy the string from the Information field manually.

Next, log in to the web interface on the node you want to add. Under Datacenter → Cluster, click on Join Cluster. Fill in the Information field with the Join Information text you copied earlier. Most settings required for joining the cluster will be filled out automatically. For security reasons, the cluster password has to be entered manually.

To enter all required data manually, you can disable the Assisted Join checkbox.

After clicking the Join button, the cluster join process will start immediately. After the node has joined the cluster, its current node certificate will be replaced by one signed from the cluster certificate authority (CA). This means that the current session will stop working after a few seconds. You then might need to force-reload the web interface and log in again with the cluster credentials.
Now your node should be visible under Datacenter → Cluster.

After 2FA was enabled, the GUI worked fine.
Prior to that:
GUI:
Establishing API connection with host '172.18.0.150'
TASK ERROR: TFA-enabled login currently works only with a TTY. at /usr/share/perl5/PVE/APIClient/LWP.pm line 121
CLI: pvecm add IP (as per https://pve.proxmox.com/wiki/Cluster_Manager)
Please enter superuser (root) password for '172.18.0.150': *************
Establishing API connection with host '172.18.0.150'
500 Can't connect to 172.18.0.150:8006 (hostname verification failed)


pvecm status # see what the status is, votes, servers, etc.

Replication
Datacenter > Replication > VM > target node > schedule (2 minutes) > Rate Limit (80% network bandwidth, I did not because they’re on a virtual network)
Added duplicate to replicate to vmpve3 as well.
Check status by looking at vmpve1 > Replication.

High Availability
Datacenter > HA > Groups
Create one variant where each server is preferred (higher is more priority).
900, 800, 700
Back to HA > Resources > Add > VM
How many times should we try to restart it or relocate it, what group to use, and we want it to be started when it migrates.
Edit to prefer to be on another node to watch migration.
There cannot be any CD ISOs attached.
Made the current preferred node go down by changing the vlan in the network device on the VM settings.
Make Debian netinstall grub boot fast, skip the grub menu.

After cluster
Add node names and IPs to /etc/hosts
Edit pushd /etc/pve/ files NEVER edit the local versions.
Use .back versions and edit the original.
bump up the config_version = number to a higher number to force the cluster to see this config as newer in the event of a conflict.
It will propagate to the other nodes.
Local corosync file (don't edit) at cat /etc/corosync/corosync.conf
Editing the local file can take down an entire cluster. Won't sync with the other machines, others will fail themselves because they are out of date.
May have to enable and start corosync-qnetd in systemd.
systemctl enable corosync-qnetd

Troubleshooting
Any change: pvecm status
Cluster management.
Run the command on one machine, wait 10 minutes, do it on another machine, wait 10 minutes.
Production:
Config change, move everything off the node and reinstall because killing the cluster service can cause undesired behavior.
If the watchdog triggers and everything isn't back online, it'll shut everything down on the node.
Remove node from cluster (or it will be a negative vote)
Reinstall it
Add it back.
Corosync may sync old config data in memory after a config change appears to take effect, so you may need to restart it:
Restart corosync (on every node, ideally before adding more nodes)
Killall pmxcfs
systemctl start pve-cluster
Pvecm status
Arp -a

cfdisk is a GUI for fdisk.
Replication job then
HA
Group
Prefer pve1
Priority highest wins.
900
100
HA
Add VM or containers to be kept online.
Group prefer pve1
Started after migration.
New group with inverted priority.
Prefer moving somewhere else so you can see it migrate.
Unplug network cable to see the watchdog start the VM on the live backup server.
If you have a lot of nodes or vms you actually wanted to wait a couple minutes because if your computer is simply lost power and got it back it'll come back online sooner than a migration would complete.
2 minutes is the delay.
It's ok if this doesn't show correct number of votes.
Nofailback means it won't go back to where it failed from. Minimal downtime.
Plug node back in and watch it live migrate back.
Brief delay where cluster sees what config and data each has and which is latest.
Can rename vm by hitting edit in Proxmox gui.
Observe replication shifted direction.
Recent copies are in RAM so they can boot the vm fast.
ZFS is RAM first Filesystem second.
CephFS keeps connections open so instead of low downtime it's buffering for a few seconds.
Pauses tcp session then continue.
New feature where if the priority is equal it will choose the one with the least load.

Pve DNS records A+dynamic DNS.

https://www.youtube.com/watch?v=1P1lhIqPDLc
Some troubleshooting around 41:00 that might be a learning moment.
journalctl -xifu
pmxcfs is the Filesystem process.
https://pve.proxmox.com/wiki/Storage_Replication

Init new disk

In Proxmox gui:
Wipe
GPT partition table.

Ceph can use a subset of the cluster to be a Ceph Cluster.
Auto error correction because there will be errors, voting with data to fix stuff.

ZFS replication will be a snapshot a minute or two behind.
The VM will just start from the last snapshot on another node.
Snapshot sync on a timer frequently.
How frequently depends on your needs.
Choose Linux Filesystem in cfdisk if you want to install ZFS on a partition (not recommended ZFss likes the whole drive).
Do ZFS in Proxmox. Add new.

Datacenter > replication> add > VM 100 replication target is the other server?

Can do less often than every minute if bandwidth is limited for the size of the snapshot.

Frequent write applications may also benefit from less frequent backups if they're not mission critical.

Rate limit to 80% of your capacity so 10MB/s for 100mb.

The VM needs to be on ZFS.

Migrate a VM right click on it. Or shutdown if needed, backup, and restore to the new location. Select  backup> restore > location

The progress indicator when cloning and backing up will go slower at first and then it reaches a point where there's just zeros and it will finish.

Quorum node says network is up, probably is not us it's you.

Install and start qemu-guest-agent


Browser inspector to see login errors.

journald for logs.
PVE access log grep auth or ticket.
POST.*/ticket

Changing ZFS pools or directories
https://forum.proxmox.com/threads/change-zfs-boot-pool-name.154692/
https://forum.proxmox.com/threads/i-need-a-little-help-with-renaming-virtual-disk-on-storage-with-zfs-file-system.56735/
https://forum.proxmox.com/threads/renaming-zfs-pools-in-use.77832/


Time Sync Issue
https://forum.proxmox.com/threads/time-sync-issue.134509/
https://forum.proxmox.com/threads/time-not-sync.132032/
https://forum.proxmox.com/threads/wrong-time-wrong-time-synchronisation.55208/
https://unix.stackexchange.com/questions/377621/clock-instability-in-debian-9-stretch

ZFS Tips and Tricks from Proxmox
https://pve.proxmox.com/wiki/ZFS:_Tips_and_Tricks

Ceph
Install on Node > use no-subscription repo
Set public & private networks as the same VLAN as the Proxmox Nodes.
https://www.reddit.com/r/ceph/comments/hbdfir/beginner_need_help_in_understanding_public/
Leave replicas alone for now.
https://search.brave.com/search?q=number+of+replicas+ceph&source=desktop&summary=1&conversation=c9e232ce19e12d91f82f3b
The basic installation and configuration is complete. Depending on your setup, some of the following steps are required to start using Ceph:
Install Ceph on other nodes
Create additional Ceph Monitors
Create Ceph OSDs
Create Ceph Pools
To learn more, click on the help button below.


apt install qemu-guest-agent
Turn on qemu agent in the VM settings.
