## High Performance Browser Networking

By Ilya Grigorik

### Chapter 1: Primer on Latency and Bandwidth

#### Speed is a Feature

- Latency is the time elapsed between a source sending a packet and the destination receiving it.
- Bandwidth is the maximum throughput of a communication path.

<img src="img/lat-band.png" alt="Visual representation of latency and bandwidth." />

#### The Many Components of Latency

- Latency is the sum total of a few different kinds of delays:

  1. Propagation Delay - function of distance & medium of propagation between source and destination
  2. Transmission Delay - function of the packet's length and data transfer rates
  3. Processing Delay - time required to process packet headers, check for bit-level errors etc.
  4. Queuing Delay - amount of time packets stay in queue

- Network data rates are typically measured in bits per second (bps), whereas data rates for non-network equipment are typically shown in bytes per second (Bps).

- It's 30 mega-bits-per-second and not 30 mega-bytes-per-second. This is why ISP speeds usually need to be divided by 8 (1 Byte = 8 bits) to find out the true speed of the internet connection.

- Serving content from a nearby location to the client enables us to significantly reduce the propagation time of all the data packets. This is why CDNs exist. They allow us to upload content once and they distribute it across various servers around the globe in order to reduce propagation delay for the user when the content is requested.

#### Last-Mile Latency

- Last-Mile latency is ironically the biggest contributor to increasing latency rather than crossing oceans or continents.

- To connect our home or office to the Internet, the local ISP needs to route the cables throughout the neighborhood, aggregate the signal, and forward it to a local routing node. All this in practice can alone take ~10 ms of work.

- Optical fibers have a distinct advantage when it comes to bandwidth because each fiber can carry many different wavelengths (channels) of light through a process known as wavelength-division multiplexing (WDM).

#### Bandwidth at the Network Edge

- The backbones or the fiber links form the core data paths of the Internet. But the strength of the network actually depends on the technology deployed (dial-up, DSL etc.) at the edges ie. connection to our homes. The available bandwidth to the user is a function of the lowest capacity link between the client and the destination server.

#### Delivering Higher Bandwidth and Lower Latencies

- The ideal condition for "fast internet" is higher bandwidths and lower latencies.

- Achieving higher bandwidths is a relatively easy task – we can add more fiber links, improve WDM techniques.

- Lowering latency is a much harder challenge. This is mainly because there is simply no way around the laws of physics: the speed of light places a hard limit on minimum latency. Alternatively, since we can’t make light travel faster, we can make the distance shorter but that has it's own challenges imposed by the physical terrain, social and political reasons.

### Chapter 2: Building Blocks of TCP

- At the heart of the Internet are two protocols: IP and TCP.

- The IP, or Internet Protocol provides the host-to-host routing and addressing.

- TCP, or Transmission Control Protocol is a *transport* protocol that provides the *abstraction* of a reliable network running over an unreliable channel, hiding most of the complexity of network communication from our applications: retransmission of lost data, in-order delivery, congestion control and avoidance, data integrity, and more.

- The HTTP standard does not mandate TCP as the only transport protocol. A different protocol such as UDP can also be used with HTTP but due to the many great and convenient features TCP provides out of the box, all HTTP traffic on the Internet today is delivered via TCP. This is what makes understanding TCP important from a web optimization standpoint.

- The reason we've heard of IPv4 and not IPv{1,2,3} is because before IPv4, IP and TCP were not separate entities. It was only in 1981 that the two protocols were separated.

#### Three Way Handshake

- All TCP connections begin with a three-way handshake. No application data can be exchanged between client and server before this handshake is done. The handshake's three phases are called `SYN`, `SYN ACK` and `ACK`.

<img src="img/threeway.png" alt="Diagram showing the three-way handshake" />

- The delay imposed by the three-way handshake makes new TCP connections expensive to create, and is one of the big reasons why connection reuse is a critical optimization for any application running over TCP.

#### Congestion Avoidance and Control

- Congestion Collapse is a recognized problem in complex networks. It occurs in a situation with asymmetric bandwidth capacity between network nodes. Congestion Collapse is a stable condition wherein if the algorithm for selecting packets to be dropped is fair, the network continues to operate, just in a degraded condition.

- To address the issue of Congestion Collapse, TCP has multiple mechanisms in place: flow control, congestion control (slow start), and congestion avoidance.

#### Flow Control

- Flow control is a mechanism to prevent the sender from overwhelming the receiver with data it may not be able to process – the receiver may be busy, under heavy load, or may only be willing to allocate a fixed amount of buffer space. To address this, each side of the TCP connection advertises its own `receive window (rwnd)`, which communicates the size of the available buffer space to hold the incoming data.

- While Flow Control helps manage congestion on the receiver's end, there are still measures needed to prevent either side from overwhelming the underlying network – Slow Start and Congestion Avoidance help here.

#### Congestion Control / Slow Start

- Since neither the sender nor the receiver know the available bandwidth at the beginning of a new connection Slow Start and Congestion Avoidance help in estimating and adapting speeds to the continuously changing conditions within the network.

- TCP slow start is an algorithm which balances the speed of a network connection. Slow start gradually increases the amount of data transmitted until it finds the network’s maximum carrying capacity.

- Fetching a file over a new TCP connection (handshake + slow start)

<img src="img/slowstart.png" />

#### Congestion Avoidance

- TCP as a system is designed to use packet loss as a feedback mechanism to help regulate it's performance.

- Slow-start initializes a connection with a conservative window and, for every roundtrip, doubles the amount of data in flight until it exceeds the receiver’s flow-control window, a system-configured congestion threshold (ssthresh) window, or until a packet is lost. At this point the congestion avoidance algorithm takes over.

- In congestion avoidance, instead of exponentially increasing the speed on the network, the algorithm increases by speeds by 1 unit on each ACK. This is done until more packets are lost which then repeats the process of congestion avoidance (or control, depending on the size of the congestion window).

#### Bandwidth-Delay Product

- The maximum amount of unacknowledged data that can be in flight at any point in time. Product of data link’s capacity and its end-to-end delay.

- **Bandwidth plays a much less crucial role in a TCP system. It is factors like the three-way handshake, slow start and most importantly latency that are the main bottlenecks**

#### Head-of-Line Blocking

- While TCP is a popular choice for most situations, it is not necessarily the best choice for every occasion. Some of its features, such as in-order and reliable packet delivery, are not always necessary and can introduce unnecessary delays and negative performance implications. One such negative implication is Head-of-Line blocking.

- Every TCP packet carries a unique sequence number when put on the wire, and the data must be passed to the receiver in-order. If one of the packets is lost en route to the receiver, then all subsequent packets must be held in the receiver’s TCP buffer until the lost packet is retransmitted and arrives at the receiver. Because this work is done within the TCP layer, our application has no visibility into the TCP retransmissions or the queued packet buffers, and must wait for the full sequence before it is able to access the data. Instead, it simply sees a delivery delay when it tries to read the data from the socket. This effect is known as TCP head-of-line (HOL) blocking.

- The delay imposed by head-of-line blocking allows our applications to avoid having to deal with packet reordering and reassembly, which makes our application code much simpler. However, this is done at the cost of introducing unpredictable latency variation in the packet arrival times, commonly referred to as _jitter_, which can negatively impact the performance of the application.

#### Tuning Application Behavior

- Tuning performance of TCP allows the server and client to deliver the best throughput and latency for an individual connection. However, how an application uses each new, or established, TCP connection can have an even greater impact:

  1. No bit is faster than one that is not sent; send fewer bits.
  2. We can’t make the bits travel faster, but we can move the bits closer.
  3. TCP connection reuse is critical to improve performance.

### Chapter 3: Building Blocks of UDP

- `UDP` came into picture much after `TCP/IP`, around the time `TCP` and `IP` were being separated.

- The primary feature of `UDP` is not what _more_ it brings to the table, but what it chooses to omit.

- `UDP` is also known as the _null protocol_.

- The term `packet` applies to any formatted block of data, while the term `datagram` is often reserved for packets delivered via an unreliable service – no delivery guarantees, no failure notifications.

- `UDP` originally stands for User Datagram Protocol but due to its unreliable nature, it's frequently also called Unreliable Datagram Protocol.

- `DNS` or Domain Name System is an application of `UDP`.

#### Null Protocol Services

- The `IP` networking layer has the primary task of delivering datagrams from the source to the destination host based on their addresses. To do so, the messages are encapsulated within an IP packet which identifies the source and the destination addresses, as well as a number of other routing parameters.

- `UDP` simply provides a layer of abstraction on top of `IP` by embedding the source and the target application ports of the communicating hosts.

- `UDP`'s non-services (things it omits):

  1. No guarantee of message delivery
  2. No guarantee of order of delivery
  3. No connection state tracking
  4. No congestion control

- `TCP` is a byte-stream oriented protocol capable of transmitting application messages spread across multiple packets without any explicit message boundaries within the packets themselves. `UDP` datagrams, on the other hand, have definitive boundaries: each datagram is carried in a single IP packet, and each application read yields the full message; datagrams cannot be fragmented.

#### UDP and Network Address Translators

- `IPv4` addresses are only 32 bits long, they provide a maximum of 4.29 billion unique IP addresses. While this is a big number on the surface, it isn't large enough to keep up with everyone trying to connect to the Internet. To solve this address depletion issue, the IP `Network Address Translator (NAT)` specification was introduced as an (interim) solution.

- Each `NAT` is responsible for maintaining a table mapping of local IP and port tuples to one or more globally unique (public) IP and port tuples. The local IP address space behind the translator can then be reused among many different networks, thus solving the address depletion problem.

- A big challenge with `UDP` is establishing connection, this is especially true for P2P applications, such as VoIP, games, and file sharing. The reason for this challenge is primarily the presence of `NAT`s and how difficult it can be to traverse them.

- NAT Traversal is the process of providing the _right_ (public IP that is actually part of the `NAT`'s translation table) information to a `NAT` so it can route you correctly.

- Popular NAT traversal techniques include `STUN`, `TURN` and `ICE`.

- `STUN` or Session Traversal Utilities for NAT is a protocol where the client sends a request to a `STUN` server on the public network to find out its public IP address and port as seen from the public network. The downside for this protocol is that it is not sufficient to deal with all `NAT` topologies and network configurations. In some cases, `UDP` may be blocked altogether by a firewall or some other network appliance.

- `TURN` or Traversal Using Relays around NAT is a protocol where the client sends a request to a public "relay" server which helps connect to the server. The obvious downside in this exchange is that it is no longer peer-to-peer! `TURN` also has a very high cost of operating.

- `ICE` or Interactive Connectivity Establishment is a protocol, and a set of methods that seek to establish the most efficient tunnel between the participants - direct connection where possible, leveraging `STUN` negotiation where needed, and finally fallback to `TURN` if all else fails.
