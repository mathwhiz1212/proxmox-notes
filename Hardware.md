# Hardware

AJ's recommendations: https://github.com/bnnanet/learn-colocation/issues/13

You can look up SSDs to see their TBW and DWPD (drive writes per day) by the search bar: [https://www.techpowerup.com/ssd-specs/](https://www.techpowerup.com/ssd-specs/)

## My Hardware

I don't recommend most of this, I went as cheap as possible and re-used hardware I had:

- Wifi-Ethernet bridge: https://www.amazon.com/dp/B07N1WW638
- If you have access to Ethernet for your home lab, use it. I didn't.
  
- Managed Switch: https://www.amazon.com/NETGEAR-8-Port-Gigabit-Ethernet-Switch/dp/B0D9W9YNWD/
- I started off with the 5-port version, but this one is way easier to use. It has "quality of life" features, loop prevention (not just detection like the 5-port) and link aggregation support.

- Ethernet cables capable of 1000mbps, Cat5e or newer. I used mostly Cat5e cables I already had access to.
- Currenlty, you can get 5 shielded Cat 6a cables for ~$20-25: https://www.amazon.com/gp/product/B00HEM5IWC/

- Kingston SSDNow V300 120GB OS drives for all 3 machines.

- Circa 2008-2014 (mostly, if not entirely, Western Digital) 1TB hard drives.
- 2 in each machine in a ZFS mirror to guard against drive failure, more likely on these old drives.
- I would do a mirror on new drives because drive failures are the most common problem you may encouter with ZFS-backed disk-intensive tasks.

## Computers:

- HP (i7-3770, 16GB DDR3 (4x4GB, max is 4x8GB), unpatched VM escape vulnerability, BIOS update isn't available anymore, can "fix" by disabling hyper-threading)
- HP (i7-4770, 16GB DDR3 (2x8GB, already at max capacity))
- Custom (AMD FX Series CPU, 8GB DDR3 (2x4GB))

The HPs are the nodes, The AMD FX PC is the Proxmox Backup Server and the voting/quorum QDevice.

HP has a PartSurfer tool that lets you look up your serial number and find all the parts that went into your HP computer: https://partsurfer.hp.com/

Dell has something similiar if you know your service tag: https://www.dell.com/support/home/en-us

Just click on Quick Links > Product specifications.
