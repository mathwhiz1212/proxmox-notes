Things I'd recommend for production that I don't have in my home lab:

0. A UPS.

1. SSDs for all storage, preferably NVMes for the metadata/cache/slog device. Preferably Enterprise-class drives with a strong write tolerance and power failure data loss protection of some kind. Minimum is SSDs for special devices if you're using hard drives.

2. low-latency networking for Corosync and 10Gbit/s networking or as close as you can get if you want to run Ceph. Slower speeds can work depending on the use case.

A proper business class switch is worth the investment.

3. Auto-restart x seconds/minutes after power comes back in the event of a power failure.

4. Mirrored OS drives for easy recovery.

5. Separate, and preferably physically separate Corosync and Ceph networks.

6. Use at least 3 nodes for ZFS replication and at least 4 for Ceph (preferably with identically-sized drives).

7. Server hardware with redundant NICs, adequate RAM and fixed network device names that do not change if re-added. The redundant NICs can allow the server to be plugged in to more than one network.

The theme is redundancy at every layer. A UPS, redundant networks, redundant NICs, redundant power supplies, mirrored enterprise drives, and a high-availability cluster so that it's very unlikely to fail, and alerts so you can fix problems as they come. With all this redundancy, you can probably just replace a redundant part and carry on.

Production Recommendations I am using:

1. Surge protectors.
2. Proxmox Backup Server for backups outside of the nodes themselves (with some nice deduplication and compression features for the VM/container snapshots). Automated backups are preferred.
3. Separate management network VLAN for managing the servers and VMs.
4. pfSense for routing between VLANs.
5. The pfSense router is in a high-availability group backed by ZFS replication.