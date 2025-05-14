# Cluster Management

Docs: https://pve.proxmox.com/wiki/Cluster_Manager

# Remove a node from cluster

https://pve.proxmox.com/wiki/Cluster_Manager#_remove_a_cluster_node

## Remove the node from the Ceph cluster (if you use Ceph)

OSD > out
Then stop
More > Destroy
Then destroy manager for the node.
ceph osd crush remove vmpve5
Destroy any metadata or manager services attached to the node.

## Remove the node from the Proxmox Cluster

Pvecm nodes to get a list of nodes
Shutdown the node completely before going any further or you may have a broken cluster.
pvecm delnode hp4
Replace hp4 with your node name from the pvecm nodes list.
Use pvecm nodes to make sure the node has been removed from the list.
More notes on re-adding from the docs:
If, for whatever reason, you want this server to join the same cluster again, you have to:


do a fresh install of Proxmox VE on it,


then join it, as explained in the previous section.


The configuration files for the removed node will still reside in /etc/pve/nodes/hp4. Recover any configuration you still need and remove the directory afterwards.


Note	After removal of the node, its SSH fingerprint will still reside in the known_hosts of the other nodes. If you receive an SSH error after rejoining a node with the same IP or hostname, run pvecm updatecerts once on the re-added node to update its fingerprint cluster wide.
