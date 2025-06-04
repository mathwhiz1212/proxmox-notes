Follow these instructions with the following modifications:

https://technotim.live/posts/proxmox-alerts/

turn on 2FA to allow creation of app password.

export HISTCONTROL="ignoreboth" to hide commands that start with a space

remove spaces fom app password when entering in this command

/etc/zfs/zed.d/zed.rc

set ZED_EMAIL_ADDR=
to the email to send alerts to

uncomment ZED_EMAIL_PROG and leave it as mail and lastly uncomment ZED_EMAIL_OPTS.

I set ZED_NOTIFY_VERBOSE to 1 temporarily to get all notifications.

You can view the zfs-zed log with `journalctl -u zfs-zed`

Hit G to go to the end of a journalctl file.
