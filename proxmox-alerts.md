Follow these instructions with the following modifications:

Set the root email in proxmox to the email you want the alerts to go to.

Datacenter > Users > root > edit.

export HISTCONTROL="ignoreboth"

to hide any commands with a space at the beginning, and run the password commands with spaces at the beginning.

You can add this to your `.bashrc` file if you want that to persist across shell instances.

https://technotim.live/posts/proxmox-alerts/

turn on 2FA to allow creation of app password.

remove spaces fom app password when entering in the command that requires it.

comment out `relayhost=` in the postfix config file. Add the gmail settings to the bottom.

# ZFS Alerts

In `/etc/zfs/zed.d/zed.rc`

Set ZED_EMAIL_ADDR=
to the email to send alerts to

I set ZED_NOTIFY_VERBOSE to 1 temporarily to get all notifications.

# Testing

If you are sure that unplugging a drive won't hurt anything (say you have a mirror or a good backup you just made this instant), you can unplug a drive and see if you get an email.

You can see the degraded state in Node > ZFS.

# Troubleshooting

Restart the `zfs-zed` service using `systemctl restart zfs-zed`

You can view the zfs-zed log with `journalctl -u zfs-zed`

Hit G to go to the end of a journalctl file.

In `/etc/zfs/zed.d/zed.rc`
you may need to specify a program and command line options if your situation is unique:

Uncomment ZED_EMAIL_PROG (mail program, `mail` probably works) and uncomment ZED_EMAIL_OPTS (CLI options)

# On more than one node.

Change pve1 to pve2 or whatever your naming scheme is when you reach that step on subsequent nodes:

`/^From:.*/ REPLACE From: pve1-alert <pve1-alert@something.com>`
