# ZFS

## Create a ZFS Mirror

You can create a ZFS mirror in Proxmox, but the command line has more flexbility and works a bit better for my use case. If you do use the GUI, make sure you use the same name for you ZFS pools across your cluster or you will be in for some pain. What follows is the command line method.

### Prep

You may want to verify the drives are using the GPT partition scheme: `gdisk /dev/sdX`. Replace X with your disk name, which you can find with `fdisk -l`.

If the disk is not already using GPT, gdisk will automatically convert the partition table to GPT, WHICH WILL DESTROY ALL DATA, BACKUP ALL YOU CARE ABOUT BEFORE RUNNING GDISK. It will ask you to confirm, but still, be careful.

You can follow these instructions to write a new GPT partition table to the drive (which will delete all data):

Run gdisk `/dev/sdX` type `o` (to write a new GPT table) then `hit enter`, and type `w` to write the data to the disk and `hit enter`. Type `y` and `hit enter` when it asks you to confirm y/n.

### Creating a mirrored ZFS pool named `hardpool` using `/dev/sdb` and `/dev/sdc` :

`zpool create hardpool mirror /dev/sdb /dev/sdc`

The pool name (in this case `hardpool`) will need to be the same on every node for the cluster to work properly and it’s not easy to change.

Read: It’s easier to copy the data somewhere else, recreate the pool, and copy the data back than it is to rename it, so **choose your name well.**

# Optimizations

Turn on the newest compression algorithm (as of early 2025):

`zfs set compression=lz4 hardpool`

Turn off atime (slight reduction in disk wear):

`zfs set atime=off hardpool`

## RAIDz

To create a RAID (RAID 5 equivalent) array:

`zpool create hardpool raidz1 /dev/sdb /dev/sdc /dev/sdd`

You can also use `sdb sdc sdd` instead of `/dev/sdb /dev/sdc /dev/sdd`

## More Info and Troubleshooting

See: https://pve.proxmox.com/wiki/ZFS_on_Linux

## Replacing a Failed Drive

Run `zpool status -g` and look for the bad drive. Here's a screenshot of what a RAIDz1 array looks like with a removed drive:
![Screenshot 2025-06-19 at 5 20 28 PM](https://github.com/user-attachments/assets/d75a16ab-b6a4-48b7-b0bc-5b9c15338a98)

The unindented `1243382129790207461` is the GUID for the array. Below that you see:

```
5159885723712038455 ONLINE
12395445472837876450 ONLINE
4404938314048604666 REMOVED
```

You may see something else like `DEGRADED` instead of `REMOVED` for a bad drive. Hopefully you set up [drive alerts](https://github.com/mathwhiz1212/proxmox-notes/blob/main/proxmox-alerts.md#zfs-alerts) so you know about problems in advance.

`4404938314048604666` is the GUID of the removed drive.

You'll want to use that to replace the drive. You don't actually need it unless there's a problem, but if there's a problem, you need it (See: https://askubuntu.com/a/305981).

`zpool replace hardpool 4404938314048604666 /dev/disk/by-id/ata-ST1000DM003-1ER162_Z4Y2PWLM`

You can find the disk by-id info by:

 `ls /dev/disk/by-id`

 and grab the first entry. For example, in:

 ```
ata-ST1000DM003-1ER162_Z4Y2PWLM
ata-ST1000DM003-1ER162_Z4Y2PWLM-part1
ata-ST1000DM003-1ER162_Z4Y2PWLM-part2
```
Use `ata-ST1000DM003-1ER162_Z4Y2PWLM1`
 
The replacement drive will need to be empty, and preferrably have a GPT partition table. If it doesn't, you can use `gdisk /dev/sdX` to do that (which will **erase all data**).

You can wipe the disk in the Proxmox web interface under `Node > Disks`.

**This is still to be tested, today.**

### On physical nodes:

If testing on consumer SSDs, see this SSD wear-reduction script. I'd recommend running the commands one-by-one ONLY when you understand what they do and can choose what to do and not do. The defaults are preferable if you don't understand what you're doing:

https://git.coolaj86.com/josh/proxmox-scripts/raw/branch/main/Proxmox/reduce-ssd-writes.sh

**Do not use in production, or on hard drives.**

# ZFS Replication
