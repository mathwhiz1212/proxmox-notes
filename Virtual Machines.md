# Creation

## CPU Cores and CPU Limits

CPUs
Always do 2 CPUs even if you apply a CPU limit of 1, I believe it's for garbage collection so it can use 2 threads as needed at a slower speed.

You can generally overprovision CPUs somewhat 4x or less if your workload isn't CPU intensive. If it is CPU intensive, you may not be able to overprovision at all without sacrificing performance.

AJ:

Cores there is virtualized cores.
CPU limit is the equivalent real cores.
So for a 4ghz cpu 2 would mean 8ghz-cores, 0.25 would mean 1ghz-cores.

I'm sure that any number over 1 means a whole core's worth and then some. I'm not sure if a number under 1 restricts to a single core (I hope not).
vCPUs ~= base clock rate x cores x 2 (if hyperthreaded)
But then you've got performance cores and efficiency cores and all that in desktop systems that don't map strictly to that calculation.
So a 12-core hyperthreaded Xeon @ 2ghz with 3ghz boost would be roughly:
12 x 2 x 2 = 48 vCPUs
In that scenario the CPU limit per vCPU would be about 0.5.
But that's all for pricing cloud services, not for what you'd use for your own stuff.

If you have identical CPUs on all your nodes, you can use `qm set --cpu host`, otherwise, find the lowest standard all your node's CPUs support here: https://qemu.readthedocs.io/en/master/system/qemu-cpu-models.html

The default `x86-64-v2-AES` should usually work, but may not result in the best performance.

You can temporarily set the CPU to the highest a node supports, but if you want to migrate the virtual machine, use ZFS replication, Ceph replication, or a high-availability group, the VM will not migrate if the target node doesn't support the CPU technology.
