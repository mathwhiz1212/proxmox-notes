Proxmox Backup Server
Ssh-copy-id root@IP
ssh@IP
No-subscription repository
Download key:
wget http://download.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg
Edit /etc/apt/sources.list.d/pbs-enterprise.list
Comment out the existing line and add:
deb http://download.proxmox.com/debian/pbs bookworm pbs-no-subscription
Save and exit.
Apt update
Apt upgrade
Apt dist-upgrade
apt install -y screen curl htop iotop
Visit https://IP:8007 to manage PBS in the web interface.
Adding to Proxmox cluster
Add storage
Wipe disk(s) (make sure they’re the right ones).
Create a ZFS mirror (in Proxmox, or by command line if you want/need a custom/matching name):
You may want to verify the drives are using the GPT partition scheme: gdisk /dev/sdb [or whatever your disk name is]
It will probably automatically convert it to GPT, THIS WILL DESTROY ALL DATA, BACKUP ALL YOU CARE ABOUT. If it doesn’t you can type o then enter, and w to write the data to the disk and hit enter. Say y when it asks you to confirm y/n.
zpool create hardpool mirror /dev/sdb /dev/sdc
This name will need to be the same on every node for the cluster to work and it’s not easy to change. Read: It’s easier to copy the data somewhere else, recreate the pool, and copy the data back than it is to rename it, so choose your name well.
Turn off atime:  zfs set atime=off hardpool
Turn on the newest compression (as of early 2025): zfs set compression=lz4 hardpool
Add Datastore in PBS
Use your ZFS path as the backing path.
Find it using zfs list
Mine was /hardpool
I named the datastore hard-backup.
Dashboard > show fingerprint > copy
Back to a proxmox node in the cluster
Datacenter > Storage > Add > Proxmox Backup Server
Paste the fingerprint into the fingerprint box.
Set an ID (name), I choose hard-backup
Server - enter IP address
Enter the username as root@pam, the root user’s password, then enter the datastore name that we created earlier
If you want encryption > generate client-side encryption key > download it to a secure location.
Then select add.


https://www.wundertech.net/how-to-set-up-proxmox-backup-server/
Other notes:
