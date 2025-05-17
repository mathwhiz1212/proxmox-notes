Things I'd recommend for production that I don't have in my home lab:

0. A UPS.

1. SSDs for all storage, preferably NVMes for the metadata/cache/slog device. Preferably Enterprise-class drives with a strong write tolerance and power failure data loss protection of some kind. Minimum is SSDs for special devices if you're using hard drives.

2. 10Gbit/s networking or as close as you can get (if you're running Ceph) on a proper business class switch. Less speed can work depending on the use case.

3. Auto-restart x seconds/minutes after power comes back in the event of a power failure.

4. Mirrored OS drives for easy recovery.

5. Separate, and preferably physically separate Corosync and Ceph networks.

6. Use at least 3 nodes for ZFS replication and at least 4 for Ceph (preferably with identically-sized drives).

Production Recommendations I am using:

1. Surge protectors.
2. Proxmox Backup Server.
3. Separate management network VLAN.
4. pfSense for routing between VLANs.
5. That router is in a high-availability group backed by ZFS replication.