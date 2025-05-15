# Ceph

## Setup
4 nodes is the minimum.
Do 4x50GB virtual drives per node for testing. 1 OSD per drive.
Do another manager on another node.
Add mangers on more than one node.

Metadata is only needed for CephFS which is not used for VMs, but can be used for isos.

## Creating a Ceph Storage Cluster



## Removing a Node from a Ceph Cluster

## Random Ceph Commands

   69  ceph status (or ceph -s)
   watch ceph --status (for continuous monitoirng)
   70  pveceph status
   71  ceph df
   72  ceph mon star

https://www.oreilly.com/library/view/mastering-proxmox/9781788397605/6297ed4c-0a37-4b41-9cef-e48aea6a27fc.xhtml
