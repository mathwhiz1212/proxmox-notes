# Ceph

## Setup

https://pve.proxmox.com/wiki/Deploy_Hyper-Converged_Ceph_Cluster

4 nodes is the minimum.
Do 4x50GB virtual drives per node for testing. 1 OSD per drive.
Do another manager on another node.
Add mangers on more than one node.

Metadata is only needed for CephFS which is not used for VMs, but can be used for isos.

## Creating a Ceph Storage Cluster



## Removing a Node from a Ceph Cluster

PG numbers needs to go down to 0 after “out” ing before you can safely stop. Or at least low. It doesn't always need to be 0.

## Random Ceph Commands

   69  ceph status (or ceph -s)
   watch ceph --status (for continuous monitoirng)
   70  pveceph status
   71  ceph df
   72  ceph mon star

ceph health detail # Does `detail` do anything?

ceph osd perf

https://search.brave.com/search?q=what+does+commit+and+apply+mean+ceph+perf&source=desktop&summary=1&conversation=9d077daf706386fa6dcfcd

Rebalance ceph osd reweight-by-utilization

BRAVE AI: If you need to pause or stop an ongoing rebalance operation, you can disable the balancer temporarily using the ceph osd set noout command to prevent further rebalancing actions.

https://docs.ceph.com/en/latest/rados/operations/balancer/

https://pve.proxmox.com/pve-docs/pveceph.1.html

https://docs.ceph.com/en/reef/rados/operations/monitoring/ (run these as `pveceph COMMAND` commands)

https://www.oreilly.com/library/view/mastering-proxmox/9781788397605/6297ed4c-0a37-4b41-9cef-e48aea6a27fc.xhtml

## Destroying OSDs

First, click "Out", then wait for the cluster to rebalance and say it's healthy again on the Ceph status page. Then stop it. Again, wait till healthy. Then More > Destroy.

If it tells you any step is not safe, try doing it on another OSD and coming back to the one you are out-ing, stop-ing, or destroy-ing.
