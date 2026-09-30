# GATE 2027 — Computer Networks: Complete Revision Notes

**How to use:** Read once end to end, then only revisit the formula sheet (Section 10) and traps (Section 11). Sections are ordered by GATE importance. Marked ⭐ = asked almost every year.

**Syllabus check:** GATE CS lists: layering (OSI/TCP-IP), switching, data link (framing, error detection, MAC, Ethernet bridging), routing (shortest path, flooding, DV, LS), fragmentation & IP addressing (IPv4, CIDR, ARP, DHCP, ICMP, NAT), transport (flow/congestion control, UDP, TCP, sockets), application (DNS, SMTP, HTTP, FTP, email). Security and IPv6 are not in the official list, so they are kept short at the end. Confirm against the official GATE 2027 brochure when released.

---

## 1. Layering and Devices

| OSI layer | TCP/IP layer | PDU | Examples | Device |
| --- | --- | --- | --- | --- |
| 7 Application | Application | Message/Data | HTTP, DNS, SMTP, FTP | Gateway |
| 6 Presentation | (in app) |  | encryption, compression, encoding |  |
| 5 Session | (in app) |  | dialog control, synchronization |  |
| 4 Transport | Transport | Segment (TCP) / Datagram (UDP) | TCP, UDP |  |
| 3 Network | Internet | Packet | IP, ICMP, ARP\*, routing | Router, L3 switch |
| 2 Data link | Network access | Frame | Ethernet, PPP, Wi-Fi | Bridge, Switch |
| 1 Physical |  | Bit | cables, signals | Hub, repeater |

\*ARP is usually placed between layers 2 and 3.

**Domains (very frequently asked):**

| Device | Collision domains | Broadcast domains |
| --- | --- | --- |
| Hub / repeater | 1 total | 1 |
| Bridge / switch | 1 per port | 1 (unless VLANs) |
| Router | 1 per port | 1 per port |

- Each VLAN is its own broadcast domain.
- Layer-wise: end-to-end (transport), hop-to-hop (network, data link).
- Connectionless vs connection-oriented: IP and UDP are connectionless and unreliable (best effort); TCP is connection-oriented and reliable.

---

## 2. Physical Layer Basics and Delays ⭐

**Delays**

- Transmission delay: Tt = L / B (L = packet bits, B = bandwidth in bps)
- Propagation delay: Tp = d / v (distance / signal speed)
- Queuing and processing delays are usually given or ignored.
- a = Tp / Tt (used everywhere in efficiency formulas)
- Bandwidth-delay product = B × RTT (bits that can be "in the pipe")
- RTT = 2 × Tp (ignoring other delays)

**Units:** 1 Kbps = 10³ bps, 1 Mbps = 10⁶ bps. For memory sizes 1 KB = 2¹⁰ bytes. Convert bytes to bits (×8).

**Channel capacity**

- Nyquist (noiseless): C = 2B log₂(L) bps, L = number of signal levels
- Shannon (noisy): C = B log₂(1 + S/N)
- SNR(dB) = 10 log₁₀(S/N). Example: 30 dB means S/N = 1000.
- When both limits are given, the answer is the **minimum** of the two.

**Switching**

- Circuit switching: dedicated path, setup time, no per-packet header, constant delay, wasteful for bursty traffic.
- Packet switching: datagram (each packet routed independently, may reorder) or virtual circuit (path set up once, packets carry a VC number).
- Message switching: whole message stored and forwarded.

**Packet switching total time** (message L bits, packet size P bits, N links i.e. N−1 routers, same bandwidth B, ignoring propagation):

Total time = (N + L/P − 1) × (P / B)

With propagation add N × Tp (or sum of link delays). Store-and-forward means each router receives the full packet before forwarding.

**Multiplexing:** TDM (synchronous/statistical), FDM, WDM.

---

## 3. Data Link Layer

### 3.1 Framing

- Character (byte) stuffing: insert an escape byte before flag/escape bytes in data.
- Bit stuffing (HDLC): flag is `01111110`. After five consecutive 1s in data, the sender inserts a 0. Receiver removes a 0 after five 1s.
- Length field and physical-layer coding violations are other methods.

### 3.2 Error Detection and Correction ⭐

**Parity:** single parity detects all odd-bit errors and misses even-bit errors. 2D parity detects more and can correct 1-bit errors.

**Internet checksum:** 16-bit one's complement sum of 16-bit words. Receiver adds everything including the checksum and expects all 1s (0xFFFF). Used by IP (header only), TCP, UDP.

**CRC ⭐**

- Generator polynomial of degree r (r+1 bits). Append r zeros to the data, divide (modulo-2, XOR), append the remainder.
- Receiver divides the received frame by the generator. Remainder 0 means accepted.
- Detects all burst errors of length ≤ r. Detects all single-bit errors if the generator has at least two terms (x⁰ term is 1). Detects all odd-number-of-bit errors if divisible by (x+1). Detects all double errors if the generator does not divide x^k + 1 for small k.
- Common: CRC-32 used in Ethernet.

**Hamming distance**

- To **detect** d errors: minimum distance ≥ d + 1
- To **correct** d errors: minimum distance ≥ 2d + 1

**Hamming code:** with m data bits and r check bits, need 2^r ≥ m + r + 1. Check bits sit at positions that are powers of 2 (1, 2, 4, 8...). Parity bit p_i covers all positions whose binary representation has bit i set. The syndrome (computed at receiver) gives the position of the erroneous bit directly.

### 3.3 Flow Control and Sliding Window ⭐⭐

Let a = Tp / Tt and Tt = frame size / bandwidth. One cycle = Tt + 2Tp (ignoring ACK transmission time).

| Protocol | Sender window | Receiver window | Efficiency (utilization) | Seq numbers needed |
| --- | --- | --- | --- | --- |
| Stop-and-Wait | 1 | 1 | 1 / (1 + 2a) | 2 (1 bit) |
| Go-Back-N | N | 1 | N / (1 + 2a) (capped at 1) | N + 1 |
| Selective Repeat | N | N | N / (1 + 2a) (capped at 1) | 2N (sender + receiver window) |

- With k-bit sequence numbers: GBN max window = 2^k − 1; SR max window = 2^(k−1).
- To get 100% utilization, N ≥ 1 + 2a.
- **Retransmission**: GBN resends the lost frame and all following ones; SR resends only the lost frame. GBN uses cumulative ACKs; SR uses individual ACKs and buffers out-of-order frames.
- With error probability p: Stop-and-Wait efficiency ≈ (1−p)/(1+2a). Expected transmissions per frame = 1/(1−p).
- Minimum sender window needed for max throughput, and the "bits in pipe" idea: Window size = BW × RTT / frame size.
- Piggybacking: attach the ACK to outgoing data frames.

### 3.4 Medium Access Control (MAC) ⭐⭐

**ALOHA**

- Pure: S = G·e^(−2G), maximum 1/(2e) ≈ **18.4%** at G = 0.5. Vulnerable time = 2T.
- Slotted: S = G·e^(−G), maximum 1/e ≈ **36.8%** at G = 1. Vulnerable time = T.

**CSMA variants:** 1-persistent (transmit immediately when idle, used by Ethernet), non-persistent (wait random time if busy), p-persistent (slotted).

**CSMA/CD (classic Ethernet) ⭐**

- Must detect collision before finishing transmission: **Tt ≥ 2Tp**, so minimum frame size L_min = 2 × Tp × B.
- Collision detection time in worst case = 2Tp.
- Efficiency ≈ 1 / (1 + 6.44a).
- **Binary exponential backoff:** after n-th collision, choose K uniformly from {0, 1, ..., 2^n − 1}, wait K × slot time (slot = 51.2 µs for 10 Mbps, = 512 bit times). n is capped at 10; give up after 16 attempts.
- Jam signal (48 bits) sent after a collision.

**Ethernet frame (IEEE 802.3)**

- Preamble 7 B + SFD 1 B, Destination MAC 6 B, Source MAC 6 B, Type/Length 2 B, Data 46–1500 B, CRC 4 B.
- Minimum frame = 64 B (excluding preamble), maximum = 1518 B. MTU = 1500 B.
- MAC address = 48 bits (6 bytes). Broadcast = FF:FF:FF:FF:FF:FF.
- Speeds: Ethernet 10 Mbps, Fast 100 Mbps, Gigabit 1000 Mbps. Full-duplex (switched) links use no CSMA/CD.
- Why minimum 64 B? 10 Mbps, max 2500 m with repeaters, 2Tp ≈ 51.2 µs → 512 bits = 64 B.

**Token Ring (IEEE 802.5)** (a = Tp/Tt, N stations)

- Early token release: η = 1 / (1 + a/N)
- Delayed token release: η = 1 / (1 + (N+1)a/N)
- Token holding time limits how long each station can transmit.

**Wireless (802.11):** CSMA/CA with ACKs (collision *avoidance* because collisions can't be detected). RTS/CTS handles hidden-terminal problem. Uses inter-frame spaces (SIFS \< DIFS) and backoff.

### 3.5 Bridges, Switches, VLAN

- Transparent bridge/learning switch: learns source MAC per port, forwards by destination MAC, floods if unknown or broadcast, filters if the destination is on the same port as the source.
- **Spanning Tree Protocol (STP):** elects a root bridge (lowest ID), each bridge picks a root port (least cost to root), and designated ports per LAN. Removes loops from the topology.
- Source routing bridges are used in Token Ring.
- VLAN: logical partition of a switched network into separate broadcast domains. Inter-VLAN traffic needs a router/L3 switch. 802.1Q adds a 4-byte tag.

### 3.6 PPP (brief)

Byte-oriented, flag `01111110`, uses LCP to set up links and NCP for network-layer config. Authentication: PAP, CHAP.

---

## 4. Network Layer

### 4.1 IPv4 Header ⭐

- Version (4 bits), **IHL** (4 bits, in units of 4 B; min 5 = 20 B, max 15 = 60 B), ToS/DSCP (8), **Total length** (16 bits, max 65535 B), Identification (16), Flags (3: reserved, **DF**, **MF**), **Fragment offset** (13 bits, in units of **8 bytes**), **TTL** (8), **Protocol** (8; ICMP=1, TCP=6, UDP=17, OSPF=89), Header checksum (16, recomputed at every router because TTL changes), Source IP (32), Destination IP (32), Options (up to 40 B).
- Header size: 20–60 B. Data max = 65535 − header.

### 4.2 Fragmentation ⭐

- Happens when packet size > MTU of the next link. Only the **destination** reassembles.
- Each fragment's data (except the last) must be a **multiple of 8 bytes**.
- Fragment offset = (byte offset of the data within the original payload) / 8.
- Every fragment gets its own IP header (copy of the original header, with new total length, offset, MF).
- MF = 1 on all fragments except the last; MF = 0 and offset > 0 on the last.
- If DF = 1 and the packet is too big, router drops it and sends ICMP "Fragmentation needed".
- **Example:** 4020 B datagram (20 B header + 4000 B data), MTU 1500. Max data per fragment = 1480 (multiple of 8). Fragments: 1480, 1480, 1040 of data, with offsets 0, 185, 370. Total 3 fragments.
- Each fragment adds 20 B of header overhead. If later links have smaller MTU, fragments are fragmented further.

### 4.3 IP Addressing ⭐⭐

**Classful**

| Class | First bits | First octet | Default mask | Networks | Hosts/net |
| --- | --- | --- | --- | --- | --- |
| A | 0 | 0–127 (1–126 usable) | /8 | 2⁷ | 2²⁴ − 2 |
| B | 10 | 128–191 | /16 | 2¹⁴ | 2¹⁶ − 2 |
| C | 110 | 192–223 | /24 | 2²¹ | 254 |
| D | 1110 | 224–239 | multicast |  |  |
| E | 1111 | 240–255 | reserved |  |  |

**Special addresses**

- Network address: host bits all 0 (identifies the network, not assignable).
- Directed broadcast: host bits all 1.
- Limited broadcast: 255.255.255.255 (never forwarded by routers).
- 0.0.0.0: "this host" (used by DHCP client at boot) or default route.
- 127.0.0.0/8: loopback.
- **Private** (not routable on the Internet): 10.0.0.0/8, 172.16.0.0 – 172.31.255.255 (/12), 192.168.0.0/16.
- 169.254.0.0/16: link-local (APIPA).

### 4.4 Subnetting, CIDR, VLSM ⭐⭐

- A /n prefix means n network bits, 32−n host bits. Usable hosts = 2^(32−n) − 2.
- Subnet mask: n ones then zeros. /26 = 255.255.255.192, /27 = ...224, /28 = ...240, /29 = ...248, /30 = ...252.
- **Network address = IP AND mask.** Broadcast address = network address with host bits set to 1.
- Subnets created by borrowing b bits = 2^b (modern practice allows all-0 and all-1 subnets; older GATE questions may exclude them — read the question).
- **CIDR aggregation (supernetting):** blocks can be combined only if they are contiguous, the count is a power of 2, and the first block's address is divisible by the total size. Aggregated prefix = common leading bits.
- **Longest prefix match:** the router picks the matching entry with the longest prefix. Default route 0.0.0.0/0 matches everything with the shortest prefix.
- **VLSM:** allocate the largest subnets first, at properly aligned boundaries.
- **Worked example:** 200.10.11.144/28 → block size 16, network 200.10.11.144, broadcast 200.10.11.159, usable hosts 14 (145–158).
- **Quick method:** block size = 256 − (interesting octet of mask). The network address is the largest multiple of the block size ≤ the IP's octet.

### 4.5 Supporting Protocols

- **ARP:** maps IP → MAC. Broadcast request, unicast reply. Cached. Sits between L2/L3. Proxy ARP lets a router answer for other hosts. **RARP** maps MAC → IP (obsolete).
- **DHCP ⭐:** application layer, UDP (server 67, client 68). **DORA:** Discover (broadcast) → Offer → Request (broadcast) → Acknowledge. Gives IP, mask, gateway, DNS, lease time. Needs a relay agent when the server is on another network.
- **ICMP:** error reporting and query, carried in IP (protocol 1). Types: Echo request/reply (ping, 8/0), Destination unreachable (3), Source quench (4, deprecated), Redirect (5), Time exceeded (11, TTL = 0 or reassembly timeout), Parameter problem (12). **Traceroute** sends packets with TTL = 1, 2, 3... and uses the Time Exceeded replies. **ping** uses Echo.
- **NAT ⭐:** translates private ↔ public addresses at the boundary router. **PAT/NAPT** maps many private hosts to one public IP using different port numbers. Table entries: (private IP, port) ↔ (public IP, new port). Breaks end-to-end principle and makes inbound connections hard.
- **IGMP:** group membership for multicast.

### 4.6 Routing ⭐⭐

**Static vs dynamic.** Intra-AS (IGP): RIP, OSPF. Inter-AS (EGP): BGP.

**Distance Vector (Bellman-Ford)**

- Each router tells its **neighbors** its **whole** table periodically. d_x(y) = min over neighbors v of \[c(x,v) + d_v(y)\].
- Converges slowly: **count-to-infinity** problem (bad news travels slowly; good news travels fast).
- Fixes: **split horizon** (don't advertise a route back on the interface you learned it from), **poison reverse** (advertise it back with infinite cost), triggered updates, hold-down timers. Split horizon does not fix loops involving three or more routers.
- **RIP:** hop-count metric, **max 15 hops** (16 = infinity), updates every 30 s, runs over **UDP 520**.
- Number of iterations/exchanges to converge relates to network diameter.

**Link State (Dijkstra)**

- Each router floods **link-state advertisements** about its **own links** to **all** routers. Each router builds the full topology and runs Dijkstra.
- Faster convergence, more memory and computation, more control traffic at startup.
- **OSPF:** IP protocol 89 (not TCP/UDP), hierarchical areas (Area 0 = backbone), uses Hello packets, supports authentication, load balancing, multiple metrics (cost).
- Dijkstra complexity O(V²) with array, O(E log V) with heap.

**BGP:** path-vector protocol, runs on **TCP 179**, policy-based routing between autonomous systems. eBGP between ASes, iBGP within an AS. Path list prevents loops.

**Flooding:** send every incoming packet on all links except the one it arrived on. Needs hop count or sequence numbers to stop duplicates. Always finds the shortest path, very robust, very wasteful.

### 4.7 Congestion and Traffic Shaping

- **Leaky bucket:** output at a constant rate regardless of input burstiness; excess is queued or dropped.
- **Token bucket:** tokens are added at rate ρ, bucket capacity C. A packet needs a token to go. Allows bursts.
- **Maximum burst time S = C / (M − ρ)**, where M is the maximum output rate. Burst bytes = C + ρS = M × S.
- Other methods: choke packets, load shedding, RED, admission control.

### 4.8 IPv6 (light)

128-bit addresses (hex, 8 groups of 16 bits), fixed 40 B header, no header checksum, no fragmentation by routers (source does it; Path MTU discovery), extension headers, no broadcast (uses multicast/anycast), ICMPv6 replaces ARP with Neighbor Discovery. Transition: dual stack, tunneling, translation. Minimum MTU 1280 B.

---

## 5. Transport Layer ⭐⭐

### 5.1 Ports and Sockets

- Port is 16 bits (0–65535). Well-known: 0–1023, registered: 1024–49151, dynamic: 49152–65535.
- **Socket = IP address + port.** A connection is identified by the 4-tuple (src IP, src port, dst IP, dst port). Transport layer provides process-to-process delivery.

### 5.2 UDP

- Header is **8 bytes**: source port, destination port, length, checksum (16 bits each).
- Connectionless, no ordering, no retransmission, no congestion control. Checksum covers a pseudo-header + header + data. Used by DNS, DHCP, SNMP, TFTP, RIP, streaming, VoIP.

### 5.3 TCP Segment

- Header 20–60 B: source port, dest port, **sequence number (32)**, **ACK number (32)**, header length (4 bits, units of 4 B), flags (URG, **ACK, PSH, RST, SYN, FIN**), **window size (16)**, checksum, urgent pointer, options (MSS, window scale, SACK, timestamp).
- Sequence number = number of the **first byte** of data in the segment. ACK number = next byte **expected** (cumulative ACK).
- **MSS** = MTU − 20 (IP) − 20 (TCP) = 1460 for Ethernet.
- SYN and FIN each consume one sequence number; a pure ACK does not.

### 5.3a Connection Management ⭐

**Three-way handshake:** Client SYN (seq = x) → Server SYN+ACK (seq = y, ack = x+1) → Client ACK (seq = x+1, ack = y+1). Data can ride on the third segment.

**Termination:** FIN → ACK → FIN → ACK (4 segments, because each direction closes independently; half-close possible). The side that sends the last ACK waits in **TIME_WAIT for 2 × MSL** so the final ACK can be retransmitted and old duplicates expire.

**States:** CLOSED, LISTEN, SYN_SENT, SYN_RCVD, ESTABLISHED, FIN_WAIT_1, FIN_WAIT_2, CLOSE_WAIT, LAST_ACK, CLOSING, TIME_WAIT.

**SYN flood:** a DoS attack that fills the server's half-open connection queue. Mitigation: SYN cookies. RST aborts a connection.

### 5.4 Flow Control

- Receiver advertises **rwnd** (window size). Sender never has more than min(cwnd, rwnd) unacknowledged bytes.
- Sender throughput ≤ Window / RTT. Link utilization = (W × MSS) / (BW × RTT) (capped at 1).
- Window needed to fill the pipe = BW × RTT.
- **Window scaling** option allows windows larger than 2¹⁶ − 1 (up to 2³⁰).
- **Sequence number wraparound:** time = 2³² bytes / bandwidth (bytes/s). The sequence space must be larger than the maximum segment lifetime × data rate.
- **Silly window syndrome:** receiver advertises tiny windows or sender sends tiny segments. Fixes: **Clark's** solution (receiver doesn't advertise until it has a MSS or half the buffer free), **Nagle's** algorithm (sender holds small data until outstanding data is ACKed).
- **Delayed ACK:** receiver may wait up to \~500 ms to ACK, but must ACK at least every second full-sized segment.

### 5.5 Congestion Control ⭐⭐

Variables: **cwnd** (congestion window), **ssthresh** (slow-start threshold). Unit is MSS.

1. **Slow start:** cwnd starts at 1 MSS. It increases by 1 MSS per ACK, so it **doubles every RTT** (1, 2, 4, 8...) until cwnd ≥ ssthresh.
2. **Congestion avoidance (AIMD):** cwnd increases by about 1 MSS per RTT (linear).
3. **Loss by timeout:** ssthresh = cwnd / 2, cwnd = 1, go back to slow start. (Both Tahoe and Reno.)
4. **Loss by 3 duplicate ACKs:**
   - **Tahoe:** ssthresh = cwnd / 2, cwnd = 1, slow start.
   - **Reno (fast retransmit + fast recovery):** ssthresh = cwnd / 2, cwnd = ssthresh (+3 during recovery), then continue in congestion avoidance.

- **Fast retransmit:** retransmit on 3 duplicate ACKs without waiting for the timer.
- **Effective window** = min(cwnd, rwnd).
- Typical GATE question: "Find cwnd after the k-th RTT" — build the sequence 1, 2, 4, ..., reach ssthresh, then +1 per RTT. Remember that ssthresh is halved at every loss event.
- Average throughput of AIMD ≈ 0.75 × W / RTT (W = window at loss).

### 5.6 Timers and RTT Estimation

- EstimatedRTT = (1 − α)·EstimatedRTT + α·SampleRTT, α = 0.125
- DevRTT = (1 − β)·DevRTT + β·|SampleRTT − EstimatedRTT|, β = 0.25
- **RTO = EstimatedRTT + 4·DevRTT**
- **Karn's algorithm:** do not use RTT samples from retransmitted segments. Double the RTO on each retransmission (exponential backoff).
- TCP uses cumulative ACKs and (by default) behaves like GBN with some SR features (receiver buffers out-of-order data; SACK adds selective ACKs).

### 5.7 Transport Reliability Numerics

- Total transmission time for a file = connection setup (1.5 RTT until the first data byte can be sent, 1 RTT until the server replies) + slow-start rounds + remaining data / rate.
- **Efficiency with window W (frames):** min(1, W / (1 + 2a)).

---

## 6. Application Layer ⭐

### 6.1 DNS

- Maps names to IPs. Uses **UDP port 53** for queries (TCP 53 for zone transfers and large responses).
- Distributed hierarchy: root → TLD (.com, .in) → authoritative servers → local/recursive resolver.
- **Recursive query:** resolver does the whole job for the client. **Iterative:** server replies with a referral to the next server. Typical: host → local resolver is recursive, resolver → root/TLD/authoritative is iterative.
- **Records:** A (host → IPv4), AAAA (IPv6), NS (name server for a domain), CNAME (alias), MX (mail server), PTR (reverse, IP → name), SOA (zone authority info).
- Caching with TTL reduces load. DNS is an application-layer protocol although it supports the Internet's infrastructure.

### 6.2 HTTP ⭐

- Port 80 (HTTPS 443), over TCP, **stateless** (cookies add state).
- **Non-persistent (HTTP/1.0):** new TCP connection per object. Per object: 1 RTT for TCP setup + 1 RTT for request/response + transmission time. So each object costs **2 RTT + transmission**.
- **Persistent (HTTP/1.1 default):** connection kept open.
  - Without pipelining: 1 RTT per object (after the initial connection setup).
  - With pipelining: all requests are sent back to back, about 1 RTT for all the objects (plus transmission).
- **Response-time example:** base HTML + n embedded objects, non-persistent, no parallelism: 2 RTT + T_html + n × (2 RTT + T_obj). Persistent with pipelining: 2 RTT (for the page) + 1 RTT (for all objects) + transmission times.
- Methods: GET, POST, HEAD, PUT, DELETE, OPTIONS, TRACE, CONNECT. Status: 1xx info, 2xx success (200 OK), 3xx redirect (301, 304 Not Modified), 4xx client error (400, 403, 404), 5xx server error (500, 503).
- **Conditional GET** (If-Modified-Since) and **proxy/web cache** reduce response time and traffic.

### 6.3 Email ⭐

- **SMTP:** port **25**, TCP, **push** protocol (client → server, server → server). Uses commands HELO, MAIL FROM, RCPT TO, DATA, QUIT. Only 7-bit ASCII, so **MIME** encodes binary/non-ASCII content.
- **Pull** protocols (mail server → user): **POP3** (port 110, download and delete, stateless-ish), **IMAP** (port 143, keeps mail on the server, folders, stateful), webmail uses HTTP.
- Path: sender's UA → sender's mail server (SMTP) → receiver's mail server (SMTP) → receiver's UA (POP3/IMAP/HTTP).

### 6.4 FTP

- **Two TCP connections:** control on port **21** (persistent, commands) and data on port **20** (opened per file transfer, then closed). Called **out-of-band** control. FTP server maintains state (current directory, user).
- **Active mode:** server initiates the data connection from port 20. **Passive mode:** client initiates (works behind NAT/firewalls).
- **TFTP:** UDP port 69, simple, no authentication.

### 6.5 Other Protocols

Telnet 23 (insecure remote login), SSH 22, SNMP 161/162 (UDP), NTP 123 (UDP), LDAP 389.

---

## 7. Network Security (lower GATE priority)

- **Symmetric:** same key for both sides. DES (56-bit key, 64-bit block), 3DES, AES (128-bit block, keys 128/192/256). Keys needed for n users: n(n−1)/2.
- **Asymmetric:** public/private pair. Keys needed for n users: 2n. **RSA:** choose primes p, q; n = pq; φ(n) = (p−1)(q−1); pick e with gcd(e, φ) = 1; d = e⁻¹ mod φ. Encrypt c = mᵉ mod n; decrypt m = cᵈ mod n. **Confidentiality:** encrypt with the receiver's public key. **Digital signature:** encrypt (the hash) with the sender's private key.
- **Diffie-Hellman:** key exchange over an insecure channel; shared key = g^(ab) mod p. Vulnerable to man-in-the-middle without authentication.
- **Hash functions:** MD5 (128-bit), SHA-1 (160), SHA-256. Properties: one-way, collision resistant. **MAC/HMAC:** hash + shared secret, gives integrity and authenticity.
- **Digital signature:** provides authentication, integrity, non-repudiation. **Certificates/PKI:** a CA binds an identity to a public key (X.509).
- **Firewalls:** packet-filter (L3/L4 rules), stateful (tracks connections), application-level gateway/proxy.
- **IPSec:** AH (integrity/authentication), ESP (also encryption); transport vs tunnel mode. **SSL/TLS:** secures TCP apps (HTTPS), sits between transport and application.
- **Attacks:** DoS/DDoS, spoofing, sniffing, man-in-the-middle, replay, SYN flood, ARP poisoning, DNS spoofing.
- Classical ciphers: Caesar (shift), monoalphabetic substitution, Vigenère (polyalphabetic), transposition, one-time pad (perfectly secure if the key is random, as long as the message, and never reused).

---

## 8. Must-Memorize Port Table

| Protocol | Port | Transport |
| --- | --- | --- |
| FTP data / control | 20 / 21 | TCP |
| SSH | 22 | TCP |
| Telnet | 23 | TCP |
| SMTP | 25 | TCP |
| DNS | 53 | UDP (TCP for transfers) |
| DHCP server / client | 67 / 68 | UDP |
| TFTP | 69 | UDP |
| HTTP | 80 | TCP |
| POP3 | 110 | TCP |
| NTP | 123 | UDP |
| IMAP | 143 | TCP |
| SNMP | 161 / 162 | UDP |
| BGP | 179 | TCP |
| HTTPS | 443 | TCP |
| RIP | 520 | UDP |
| OSPF | (IP protocol 89) | IP directly |

---

## 9. Solved Patterns (practice these shapes)

1. **CSMA/CD min frame size:** bandwidth 10 Mbps, cable 2 km, speed 2×10⁸ m/s → Tp = 10 µs, 2Tp = 20 µs → L = 20 µs × 10⁷ = 200 bits.
2. **Sliding window:** 1 Mbps link, 1000-bit frames, Tp = 20 ms → Tt = 1 ms, a = 20, 1 + 2a = 41. Window needed for full utilization = 41. With 3-bit sequence numbers: GBN max window = 7 → η = 7/41 ≈ 17%; SR max window = 4 → η = 4/41.
3. **Subnet:** a /25 network has 126 usable hosts. To split a /24 into 4 equal subnets use /26 (62 hosts each).
4. **TCP congestion:** ssthresh = 16, slow start 1 → 2 → 4 → 8 → 16 (4 RTTs), then 17, 18, ... A timeout at cwnd = 20 → ssthresh = 10, cwnd = 1 → 2, 4, 8, 10 (the ssthresh cap), then linear.
5. **Packet switching time:** 1000-byte message, 100-byte packets, 3 links (2 routers), B = 8 Kbps, ignoring propagation → packet time = 800/8000 = 0.1 s, total = (3 + 10 − 1) × 0.1 = 1.2 s.
6. **Token bucket:** C = 250 KB, ρ = 2 MB/s, M = 25 MB/s → S = 250 KB / 23 MB/s ≈ 10.9 ms.
7. **TCP throughput bound:** rwnd = 64 KB, RTT = 100 ms → max throughput = 64 KB / 0.1 s = 640 KB/s ≈ 5.12 Mbps.

---

## 10. Formula Sheet

| Topic | Formula |
| --- | --- |
| Transmission delay | Tt = L / B |
| Propagation delay | Tp = d / v |
| a | Tp / Tt |
| Nyquist | 2B log₂ L |
| Shannon | B log₂(1 + S/N) |
| Stop-and-Wait efficiency | 1 / (1 + 2a) |
| GBN / SR efficiency | N / (1 + 2a) |
| GBN max window | 2^k − 1 |
| SR max window | 2^(k−1) |
| Min window for 100% | 1 + 2a |
| CSMA/CD min frame | L ≥ 2 × Tp × B |
| CSMA/CD efficiency | 1 / (1 + 6.44a) |
| Pure ALOHA | G e^(−2G), max 0.184 |
| Slotted ALOHA | G e^(−G), max 0.368 |
| Token ring (early / delayed) | 1/(1 + a/N) ; 1/(1 + (N+1)a/N) |
| Hamming bits | 2^r ≥ m + r + 1 |
| Detect / correct d errors | d + 1 ; 2d + 1 |
| CRC burst detection | all bursts ≤ r (degree of generator) |
| Hosts in /n | 2^(32−n) − 2 |
| Packet switching | (N + L/P − 1)(P/B) |
| TCP RTO | EstRTT + 4 DevRTT |
| TCP throughput | W / RTT |
| Window for full pipe | BW × RTT |
| Token bucket burst time | C / (M − ρ) |
| Sequence wraparound time | 2³² / rate (bytes/s) |
| Symmetric / asymmetric keys | n(n−1)/2 ; 2n |

---

## 11. Common Traps (check before answering)

1. Units: bits vs bytes, K = 1000 for bandwidth but 1024 for memory. Convert everything to bits and seconds first.
2. RTT = 2Tp, not Tp. In sliding window include only Tt + 2Tp unless ACK transmission time is given.
3. Utilization can never exceed 100%: take min(1, N/(1+2a)).
4. Fragment offset is in **8-byte units**; fragment data sizes must be multiples of 8 (except the last).
5. Fragmentation counts **header per fragment**, so total bytes on the wire increase.
6. IHL is in 4-byte words; TCP header length is also in 4-byte words.
7. Usable hosts = 2^h − 2 (network and broadcast addresses excluded).
8. Hubs do not separate collision domains; switches do not separate broadcast domains.
9. SMTP is push; POP3/IMAP are pull. FTP uses two connections; HTTP is stateless.
10. TCP ACK number is the **next expected byte**, not the last received.
11. Reno and Tahoe differ only on triple-duplicate-ACK handling; on timeout both set cwnd = 1.
12. Split horizon doesn't stop every count-to-infinity loop.
13. RIP max useful distance is 15; OSPF runs directly over IP (protocol 89); BGP uses TCP.
14. Checksum is recomputed at each router (TTL changes), while the IP address and the payload stay the same unless NAT rewrites them.
15. NAT breaks end-to-end semantics and changes the transport checksum because ports/IPs change.

---

## 12. Suggested Practice Plan

1. Solve GATE PYQs topic-wise in this priority order: sliding window and MAC (CSMA/CD, ALOHA) → IP addressing, subnetting, fragmentation → TCP (congestion, handshake, throughput) → routing (DV/LS) → application protocols and ports → error detection (CRC, Hamming, checksum) → DNS/HTTP timing problems.
2. Keep a one-page mistakes log of formulas and traps you got wrong, and redo it a week later.
3. Take at least 10 full mock tests close to the exam and keep Computer Networks to about 12–15 minutes inside each.
