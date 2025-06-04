Follow these instructions with the following modifications:

https://technotim.live/posts/proxmox-alerts/

turn on 2FA to allow creation of app password.

export HISTCONTROL="ignoreboth" to hide commands that start with a space

remove spaces fom app password when entering in this command

set root emai as the email to send alerts to

/etc/zfs/zed.d/zed.rc

uncomment ZED_EMAIL_PROG and leave it as mail and lastly uncomment ZED_EMAIL_OPTS.

After performing those two tweaks I am now able to receive an email notification every time zfs scrubs. If you only wish to be notified on errors set ZED_NOTIFY_VERBOSE to 0.

Hit G to go to the end of a journalctl file.

/tmp/zed.debug.log

