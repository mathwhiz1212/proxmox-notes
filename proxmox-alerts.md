Follow these instructions with the following modifications:

export HISTCONTROL="ignoreboth"

to hide any commands with spaces at the beginning, and run the password commands with spaces at the beginning.

You can add this to your `.bashrc` file if you want that to persist across shell instances.

https://technotim.live/posts/proxmox-alerts/

turn on 2FA to allow creation of app password.

export HISTCONTROL="ignoreboth" to hide commands that start with a space

remove spaces fom app password when entering in the command that requires it.

comment out `relayhost=` in the postfix config file. Add the gmail settings to the bottom.

/etc/zfs/zed.d/zed.rc

set ZED_EMAIL_ADDR=
to the email to send alerts to

uncomment ZED_EMAIL_PROG and leave it as mail and lastly uncomment ZED_EMAIL_OPTS.

I set ZED_NOTIFY_VERBOSE to 1 temporarily to get all notifications.

# Troubleshooting

You can view the zfs-zed log with `journalctl -u zfs-zed`

Hit G to go to the end of a journalctl file.

# On more than one node.

Change pve1 to pve2 or whatever your naming scheme is when you reach that step on subsequent nodes:

`/^From:.*/ REPLACE From: pve1-alert <pve1-alert@something.com>`
