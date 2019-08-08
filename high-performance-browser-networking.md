## High Performance Browser Networking

By Ilya Grigorik

### Chapter 1: Primer on Latency and Bandwidth

- Latency is the time elapsed between a source sending a packet and the destination receiving it.
- Bandwidth is the maximum throughput of a communication path.

<img src="img/lat-band.png" alt="Visual representation of latency and bandwidth." />

- Latency is the sum total of a few different kinds of delays:

  1. Propagation Delay - function of distance & medium of propagation between source and destination
  2. Transmission Delay - function of the packet's length and data transfer rates
  3. Processing Delay - time required to process packet headers, check for bit-level errors etc.
  4. Queuing Delay - amount of time packets stay in queue

- Network data rates are typically measured in bits per second (bps), whereas data rates for non-network equipment are typically shown in bytes per second (Bps).

- It's 30 mega-bits-per-second and not 30 mega-bytes-per-second. This is why ISP speeds usually need to be divided by 8 (1 Byte = 8 bits) to find out the true speed of the internet connection.

- Serving content from a nearby location to the client enables us to significantly reduce the propagation time of all the data packets. This is why CDNs exist. They allow us to upload content once and they distribute it across various servers around the globe in order to reduce propagation delay for the user when the content is requested.

- Last-Mile latency is ironically the biggest contributor to increasing latency rather than crossing oceans or continents.

- To connect our home or office to the Internet, the local ISP needs to route the cables throughout the neighborhood, aggregate the signal, and forward it to a local routing node. All this in practice can alone take ~10 ms of work.

- Optical fibers have a distinct advantage when it comes to bandwidth because each fiber can carry many different wavelengths (channels) of light through a process known as wavelength-division multiplexing (WDM).

- The backbones or the fiber links form the core data paths of the Internet. But the strength of the network actually depends on the technology deployed (dial-up, DSL etc.) at the edges ie. connection to our homes. The available bandwidth to the user is a function of the lowest capacity link between the client and the destination server.

- The ideal condition for "fast internet" is higher bandwidths and lower latencies. 

- Achieving higher bandwidths is a relatively easy task – we can add more fiber links, improve WDM techniques. 

- Lowering latency is a much harder challenge. This is mainly because there is simply no way around the laws of physics: the speed of light places a hard limit on minimum latency. Alternatively, since we can’t make light travel faster, we can make the distance shorter but that has it's own challenges imposed by the physical terrain, social and political reasons.
