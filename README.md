Multicast Video Distribution: Protocol Design, Configuration, and Verification using PIM Sparse-Dense Mode
Author: Ajibawo, Oladipo Oluyemisi

Date: May 2014


Overview
This research article presents the design, configuration, and systematic verification of an IP multicast video distribution network for a multi-room hotel environment. The study covers multicast communication models, IGMP operation, PIM protocol variants, and the practical implementation of PIM Sparse-Dense Mode with Auto-RP for automatic Rendezvous Point discovery, load balancing, and automatic failover.

Table of Contents
·	Section 1: Introduction and Multicast Video Communication
·	Section 2: Internet Group Management Protocol (IGMP)
·	Section 3: Protocol Independent Multicast (PIM)
·	Section 4: Configuration
·	Section 5: Verification
·	Network Topology
·	Key Findings
·	References

Section 1: Introduction
IP multicasting has been successfully deployed in universities, hospitals, schools, and hotels. This article presents its application within GoodNight Plc, a hotel in the north of England, replacing a traditional subscription TV channel system with an efficient multicast video distribution architecture.
Multicast vs. Unicast
Feature	Unicast	Multicast
Source address	Single	Single
Destination address	Single	Group (shared by multiple receivers)
Bandwidth usage	Scales linearly with receivers	Constant regardless of receivers
Packet replication	At source	At distribution tree branch points

Advantages of Multicast
·	Bandwidth efficiency: Single stream serves multiple simultaneous receivers
·	Optimised performance: Packets are replicated only at distribution branch points
·	Scalability: Suited to both LAN and large WAN environments
·	Supports distributed applications: Video conferencing, e-learning, web caching
Challenges
·	Best-effort UDP delivery (no guaranteed reliability)
·	Packets may arrive out of sequence
·	No TCP congestion avoidance; QoS provisioning required
·	Duplicate packets possible from routing protocol mechanisms

Section 2: IGMP
The Internet Group Management Protocol (IGMP) manages multicast group membership between hosts and directly connected routers.
·	Hosts send membership report messages when joining a group
·	Routers send periodic queries (every 125 seconds by default, to 224.0.0.1)
·	If no response is received for a group, the router stops forwarding to that network
·	IGMPv2 is the most widely deployed version

Section 3: PIM
Protocol Independent Multicast (PIM) uses the unicast routing table to execute Reverse Path Forwarding (RPF) rather than maintaining a separate multicast routing table.
PIM Protocol Variants
Protocol	Tree Type	RP Required	Best Suited For
PIM Dense Mode (PIM-DM)	Source tree (S,G)	No	Densely distributed receivers
PIM Sparse Mode (PIM-SM)	Shared tree (*,G)	Yes	Sparsely distributed receivers
PIM Sparse-Dense Mode	Both	Optional (Auto-RP)	Mixed environments
Source-Specific Multicast (SSM)	Source tree (S,G)	No (source known)	Known-source applications
Bidirectional PIM (BIDIR-PIM)	Bidirectional shared	Yes (DF mechanism)	Many-to-many applications

Notation Reference
Notation	Meaning
(S, G)	Source-specific entry: source S, multicast group G
(*, G)	Shared tree entry: any source, multicast group G


Section 4: Configuration
Network Topology Summary
The prototype network deployed for GoodNight Plc uses the following RP assignment:
Multicast Streams	Primary RP	Backup RP
227.1.6.5, 227.1.6.1, 227.1.6.2	R4 Loopback 0 (6.6.1.4)	R3 Loopback 1 (6.6.1.2)
227.1.6.3, 227.1.6.4, 224.2.127.254	R3 Loopback 0 (6.6.1.5)	R4 Loopback 1 (6.6.1.3)

Enable PIM Sparse-Dense Mode (Example: R3)
R3(config)# ip multicast-routing
R3(config)# interface GigabitEthernet0/0
R3(config-if)# ip pim sparse-dense-mode

Configure Candidate RP on R3
R3(config)# ip pim send-rp-announce lo0 scope 6 group-list 2 interval 10
R3(config)# ip pim send-rp-announce lo1 scope 6 group-list 1 interval 10

Configure RP Mapping Agent on R1
R1(config)# ip pim send-rp-discovery loopback0 scope 6 interval 10

Auto-RP vs. Bootstrap Router (BSR)
Feature	Auto-RP	BSR
RP announcement method	Multicast	Flooding (unicast to elected routers)
TTL scoping	Supported	Not supported
PIM Sparse-Dense fallback	Switches to Dense Mode	Does not switch
C-RP announcement	Multicast	Unicast to elected BSR


Section 5: Verification
Commands Used
Command	Purpose
show ip pim neighbor	Verify PIM neighbour adjacencies and modes
show ip pim interface	Confirm Sparse-Dense Mode on all interfaces
show ip pim rp mapping	Verify elected and backup RPs via Auto-RP
show ip mroute	Inspect full multicast routing table
show ip mroute 227.1.6.3	Inspect specific multicast stream routing entry
debug ip pim auto-rp	Monitor Auto-RP announcement and discovery

Failover Verification
Loopback 0 on R3 (RP address 6.6.1.5/32) was shut down to trigger failover. Post-shutdown verification confirmed that multicast streams 227.1.6.3, 227.1.6.4, and 224.2.127.254 successfully transitioned their RP from 6.6.1.5 (R3 Lo0) to 6.6.1.3 (R4 Lo1).

Key Findings
·	PIM Sparse-Dense Mode with Auto-RP eliminates the need for static RP configuration on every router
·	Load balancing across two RPs distributes multicast group responsibility and prevents single points of failure
·	Automatic failover was successfully verified using show ip pim rp mapping and debug ip pim auto-rp
·	Session Announcement Protocol (SAP) stream 224.2.127.254 was correctly included in the RP group-list and failover scope
·	Multicast provides a significantly more bandwidth-efficient solution than traditional subscription TV systems in hotel environments

References
·	Cisco (1998). Overview of IP Multicast Technology. Cisco Systems Inc.
·	Cisco (2008a). Cisco IP Multicast Command Reference. California, USA: Cisco Systems Inc.
·	Cisco (2008b). Bidirectional PIM Deployment Guide. Cisco Systems.
·	Doyle, J. and Carroll, J. (2001). Routing TCP/IP, Volume II. Indianapolis: Cisco Press.
·	Handley, M., Perkins, C. and Whelan, E. (2000). Session Announcement Protocol (RFC 2974).
·	Handley, M., Fenner, B., Holbrook, H. and Kouvelas, I. (2000). Protocol Independent Multicast (RFC 4601).
·	Jelger, C. and Noel, T. (2008). IP Multimedia in Next-Generation Mobile Networks.
·	Juyoung, K., Seok Joo, K., Shin Gak, K. and Dae Young, K. (2001). Multicast Delivery Based on Unicast and Subnet Multicast. IEEE Communications, 5(5), pp. 181-183.
·	Parkhurst, R. (1999). Cisco Multicast Routing and Switching. New York: McGraw Hill Inc.
·	Williamson, B. (1999). Developing IP Multicast Networks. Indianapolis: Cisco Press.

Ajibawo, Oladipo Oluyemisi

