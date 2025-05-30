# Samba Install

I have 2 hard drives in a ZFS mirror for data redundancy.
[Create a ZFS Mirror](ZFS.md#create-a-zfs-mirror)

Have a separate virtual drive for the OS and for Samba. This will make backups and some administration a lot easier.

Debian netinstall with just the ssh server.

No sbin fix
echo 'export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin' >> .bashrc
bash
https://www.digitalocean.com/community/tutorials/how-to-view-and-update-the-linux-path-environment-variable

Install Samba:

apt install samba nano

Add to /etc/samba/smb.conf:

[Test]
  comment = test
  path = /home/josh/samba
  browseable = yes
  read only = no
  guest ok = no
  valid users = josh

[global]
server max protocol = SMB3
smb encrypt = required
lanman auth = no

 ;this makes encryption required (it’s interesting to look at Wireshark before and after).

Create the josh samba user:
smbpasswd -a josh
Give permissions to /samba to the josh samba user:
chown -R josh:josh /samba
Then restart Samba:
systemctl restart smbd
Samba will run on port 445 on the server.
VLAN 500

Connect to the IP (192.168.1.222) with the WORKGROUP workgroup and the user and password you choose. It should work on Windows, Mac, and Linux. Samba 3.

https://linuxconfig.org/how-to-set-up-a-samba-server-on-debian-10-buster

Right click on This PC > Add a network location >
Windows does backslashes: \\192.168.1.222\Test

Samba Security
Troubleshooting phone app
https://search.brave.com/search?q=authentication+failed+after+requiring+encryption+samba&source=desktop
https://serverfault.com/questions/657942/encrypting-smb-traffic-with-samba
“If this is set to mandatory then all traffic to a share must must be encrypted once the connection has been made to the share. The server would return "access denied" to all non-encrypted requests on such a share. Selecting encrypted traffic reduces throughput as smaller packet sizes must be used (no huge UNIX style read/writes allowed) as well as the overhead of encrypting and signing all the data.”
General securing Samba
https://www.makeuseof.com/ways-to-secure-samba-server-on-linux/
https://www.cyberciti.biz/faq/how-to-configure-samba-to-use-smbv2-and-disable-smbv1-on-linux-or-unix/

I wonder if passing the drive directly to the VM would increase performance, if you have an entire ZFS pool to dedicate to it.
