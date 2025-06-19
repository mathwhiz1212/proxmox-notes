# ZFS

## Create a ZFS Mirror

Create a ZFS mirror (in Proxmox, or by command line if you want/need a custom/matching name). Here is the command line version:
You may want to verify the drives are using the GPT partition scheme: `gdisk /dev/sdb` [or whatever your disk name is]

It will probably automatically convert it to GPT, THIS WILL DESTROY ALL DATA, BACKUP ALL YOU CARE ABOUT.

If it doesn’t, you can type o then enter, and w to write the data to the disk and hit enter. Say y when it asks you to confirm y/n.

Creating a mirrored ZFS pool named `hardpool` using `/dev/sdb` and `/dev/sdc` :

`zpool create hardpool mirror /dev/sdb /dev/sdc`

This name will need to be the same on every node for the cluster to work and it’s not easy to change.

Read: It’s easier to copy the data somewhere else, recreate the pool, and copy the data back than it is to rename it, so **choose your name well.**

Turn on the newest compression (as of early 2025):

`zfs set compression=lz4 hardpool`

Turn off atime (slight reduction in disk wear):

`zfs set atime=off hardpool`

## RAIDz

To create a RAIDz[RAID 5 equivalent) array:

`zpool create hardpool raidz /dev/sdb /dev/sdc /dev/sdd`

You can also use `sdb sdc sdd` instead of `/dev/sdb /dev/sdc /dev/sdd`

### On physical nodes:

If testing on consumer SSDs, see this SSD wear-reduction script. I'd recommend running the commands one-by-one ONLY when you understand what they do and can choose what to do and not do. The defaults are preferable if you don't understand what you're doing:

https://git.coolaj86.com/josh/proxmox-scripts/raw/branch/main/Proxmox/reduce-ssd-writes.sh

**Do not use in production, or on hard drives.**

# ZFS Replication
