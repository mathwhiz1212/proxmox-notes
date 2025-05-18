# OS Setup

Ubuntu Server

Minimal install.
Configure IP
Import SSH keys from github.
Turn off swap
sudo swapoff -a
Edit /etc/fstab to comment out swap out. Add a noatime line while you’re there.
For example:
LABEL=FED34 / btrfs defaults,noatime
sudo nano /etc/fstab
Remove snap.
sudo apt remove snap-store snap snapd -y
Install utilities
sudo apt install nano screen curl -y
Update
`apt update && apt upgrade -y`

Debian netinstall

Domain:
pve
Grub
nano /etc/default/grub
Timeout to 1
Update-grub

No sbin over SSH fix
echo 'export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin' >> .bashrc
Bash
https://www.digitalocean.com/community/tutorials/how-to-view-and-update-the-linux-path-environment-variable

authorized_keys permissions
chmod 700 .ssh
sudo chmod 640 .ssh/authorized_keys
sudo chown $USER .ssh
sudo chown $USER .ssh/authorized_keys

https://stackoverflow.com/a/34887294 


Reserved blocks - 40MB or so, whatever that percentage is. 1-2%.
https://serverfault.com/questions/933157/partitioning-is-5-reserved-blocks-required-recommended 



