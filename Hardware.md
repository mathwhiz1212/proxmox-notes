# Hardware

AJ's recommendations: https://github.com/bnnanet/learn-colocation/issues/13

You can look up SSDs to see their TBW and DWPD (drive writes per day) by the search bar: https://www.techpowerup.com/

## My Hardware

I don't recommend this, I went as cheap as possible and re-used hardware I had:

- Wifi-Ethernet bridge (didn't have ethernet, but had wifi for the lab): https://www.amazon.com/dp/B07N1WW638
- Managed Switch: https://www.amazon.com/dp/B07PJ7XZ7X

- Ethernet cables capable of 1000mbps, Cat5e or newer. I used mostly Cat5e cables I already had access to.

- Kingston SSDNow V300 120GB OS drives for all 3 machines.

- Circa 2012-2014 Western Digital 1TB hard drives - 2 in each machine in a ZFS mirror to guard against drive failure, more likely on these old drives. I would do the same on new drives because drive failures are the most common problem.

## Old Computers:

- HP (i7-3770, 16GB DDR3 (4x4GB, max is 4x8GB), unpatched VM escape vulnerability, BIOS update isn't available anymore, can "fix" by disabling hyper-threading)
- HP (i7-4770, 16GB DDR3 (2x8GB, already at max capacity))
- Custom (AMD FX Series CPU, 8GB DDR3 (2x4GB))

The HPs are the nodes, The AMD FX PC is the Proxmox Backup Server and will be the voting device once I remove the Raspberry Pi, and add the PBS server as a Corosync QDevice.

HP has a PartSurfer tool that lets you look up your serial number and find all the parts that went into your HP computer: https://partsurfer.hp.com/
