Failures
Node that didn’t get config update.
```
systemctl stop corosync pve-cluster
pmxcfs -l
nano /etc/pve/corosync.conf
# update file with changes so it matches the file on the other nodes exactly, or there will probably be trouble.
reboot
```

- Corosync will refuse to start if the configuration version is older than the current running configuration.

- Don’t turn off a node shortly after a config/cluster change like a node being removed to help avoid this problem.
