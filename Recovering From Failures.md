# Recovering from Failure

Node that didn’t get a config update, and fails itself.

```
systemctl stop corosync pve-cluster
pmxcfs -l
nano /etc/pve/corosync.conf
# update file with changes so it matches the file on the other nodes exactly, or there will probably be trouble.
reboot
```

- Corosync will refuse to start if the configuration version is older than the current running configuration.

- Don’t turn off a node shortly after a config/cluster change like a node being removed to help avoid this problem.

## Failing and Restoring a Node

Increment the number of expected votes down to match the number of active nodes. This allows for quorum with fewer votes to manage or recover the cluster:

`pvecm expected x` x is the number of active nodes.
