# Creation

## CPU Cores and CPU Limits

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
