# ZFS

## Create a ZFS Mirror

Create a ZFS mirror (in Proxmox, or by command line if you want/need a custom/matching name). Here is the command line version:
You may want to verify the drives are using the GPT partition scheme: gdisk /dev/sdb [or whatever your disk name is]
It will probably automatically convert it to GPT, THIS WILL DESTROY ALL DATA, BACKUP ALL YOU CARE ABOUT. If it doesn’t you can type o then enter, and w to write the data to the disk and hit enter. Say y when it asks you to confirm y/n.
zpool create hardpool mirror /dev/sdb /dev/sdc
This name will need to be the same on every node for the cluster to work and it’s not easy to change. Read: It’s easier to copy the data somewhere else, recreate the pool, and copy the data back than it is to rename it, so choose your name well.
Turn off atime:  zfs set atime=off hardpool
Turn on the newest compression (as of early 2025): zfs set compression=lz4 hardpool
