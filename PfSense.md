# PfSense

## Install

### Create VM
```
Download the PfSense ISO into your local storage.

ISO images > Download from URL > https://atxfiles.netgate.com/mirror/downloads/pfSense-CE-2.7.2-RELEASE-amd64.iso.gz

Leave the defaults and let it use gzip to decompress the ISO.

# This (2.7.2) is the latest CE release that doesn't require an account. 2.8.x requires an account and introduces breaking changes. You can view 2.7.2 and earlier here: https://atxfiles.netgate.com/mirror/downloads/

Right-click on your node and click "Create VM".

Node: The main one you want to use.
VM ID (something at the beginning of a range you're not using for anything but networking): 4000
Name: pfsense1
Start at Boot (check the box)
Tag: mgmt
```

Select your ISO storage and the `pfSense-CE-2.7.2-RELEASE-amd64.iso` file we downloaded earlier. Click next.

Click next on the system tab to accept the defaults.

On Disks, select 8GB unless you plan to do more intense logging. NOTE: It is easier to expand than shrink virtual disks.

Check `Discard` and `SSD emulation` (you may need to check `Advanced` to see this. Leave the other defaults, and especially leave backups checked and DO NOT check skip replication. It is needed for ZFS replication-backed high-availability setups.

Under CPU, give it 2 cores. You can adjust the type to the lowest version your CPUs support for best performance: 

In absolutely identical CPUs through the entire cluster, you can use `host`. If you do not have absolutely identical CPUs, this will break moving VMs between nodes, or replication.

Enable protection under `pfsense1` > options to prevent against accidental deletion.

Don't check the guest editions checkmark on the pfSense VM in Proxmox.

## Configuration

## Dynamic DNS

Go to pfsense > Services > Dynamic DNS

Select Add

Service type `Namecheap`

Interface `WAN`

hostname: `lab` `joshmudge.com` (`lab` in the first box, `joshmudge.com` in the second, for `lab.joshmudge.com`).

If you want to do a sub-sub-domain, do: hostname: `pve1.lab` `joshmudge.com` (`pve1.lab` in the first box for `pve1.lab.joshmudge.com`)

Grab your dynamic DNS password from your Advanced DNS page for your domain name on Namecheap and input it in the password field.

Add a description and hit save.

Use the duplicate icon to duplicate the Dynamic DNS updater for other domains and change the domain name and description as needed.

You will need to make sure the record you want to update is `A + Dynamic DNS` and not just an `A` record.

**Backup your PfSense configuration to a secure location.**

## Backup

Go to `PfSense > Diagnostics > Backup and Restore`

Check the box for `Do not backup package information.` and make sure `Do not backup RRD data` is checked. You probably want SSH keys to be checked.

You can, and probably should, encrypt the file if the storage you are saving it to is not encrypted itself.

Save the `.xml` backup file somewhere safe.

NOTE TO SELF: Update with my docs: https://docs.google.com/document/d/14MXJ6jj2wq-zlGDdJentC4fKeSjKSJmE7h0zzJzOzVA/edit?tab=t.0
