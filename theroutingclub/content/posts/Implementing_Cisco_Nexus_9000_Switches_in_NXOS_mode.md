---
title: "Implementing Cisco Nexus 9000 Switches in NXOS mode"
date: 2023-05-02
draft: false
tags: [Cisco, NXOS, Cisco Nexus, Nexus 9000]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "This Post is to document the learnings from - Implementing Cisco Nexus 9000 Switches in NXOS_mode"
# AuthorName: "Nilesh Gore"
---

#   1)  Describing VXLAN EVPN in Single Site

##  1.1)    Introduction

Modern virtualized data center fabrics must meet certain requirements to accelerate application deployment and support DevOps needs. Extended Layer 2 connectivity between data centers is required to enable important technologies, such as server clustering, workload mobility across data centers, cloud integration, optimized forwarding, and so on. In addition, fabrics need to support scaling of forwarding tables, scaling of network segments, Layer 2 segment extension, and virtualized networks for multitenant support on shared physical infrastructure.

Here you will cover the Virtual Extensible LAN (VXLAN) Ethernet Virtual Private Network (EVPN) control and data plane, including Layer 2 packet flow with extended connectivity and anycast gateway, as well as Layer 3 packet flow in the fabric. You will also learn about the multitenancy support of the VXLAN EVPN technology, as well as how to manage the VXLAN fabric using Cisco Data Center Network Manager (DCNM), and describe VXLAN EVPN virtual port channels (vPCs) support.

##  1.2)    Describe VXLAN EVPN Control Plane

VXLAN is an industry-standard protocol that uses underlay IP networks to build Layer 2 overlay logical networks. It does not impose specific restrictions on the underlying infrastructure, as long as it is capable of transporting IP packets. In addition, the VXLAN overlay benefits from all capabilities of the underlying transport network, such as fast convergence, load balancing, and multicast replication.

The following example depicts a VXLAN IP underlay network, which provides connectivity between different LAN segments and physical or virtual hosts.

![](/images/cisconxos/cisconxos_1_2_1.png)

The edge devices toward the LAN segments can be optimized according to their functions and relevant protocols for the local connectivity, and can be easily scaled according to the network needs. The core devices in the underlay network can be optimized to facilitate high-speed transport and fast convergence. The placement of end hosts is more flexible because they do need to be constrained to a single physical location.

VXLAN uses a MAC in IP/UDP (MAC-in-UDP) encapsulation technique where the original Layer 2 frame has an extra VXLAN header and is then placed in an IP/UDP packet. The VXLAN header is 8-byte that consists of a 24-bit unique network ID referred to as a VXLAN Network Identifier (VNI) and few reserved bits. The VNI is used to identify Layer 2 segments and to maintain Layer 2 isolation between the segments. With all 24 bits in VNI, VXLAN can support 16 million LAN segments. Valid VNI values are from 4096 to 16777215. In addition, the IP/UDP encapsulation allows each LAN segment to be extended across existing Layer 3 networks making use of Layer 3 Equal-Cost Multipath (ECMP).

VXLAN uses VXLAN tunnel endpoint (VTEP) devices to map devices in local segments to VXLAN segments. VTEP performs encapsulation and de-encapsulation of the Layer 2 traffic. Each VTEP has at least two interfaces: a switch interface on the local LAN segment and an IP interface in the transport IP network. VTEP device uses the IP address on the transport network to encapsulate Ethernet frames and transmits the encapsulated packets to the transport network through the IP interface. A VTEP device also discovers the remote VTEPs for its VXLAN segments and learns remote MAC Address-to-VTEP mappings through its IP interface.

Network Virtualization Endpoint or Network Virtualization Edge (NVE) is the overlay interface configured in Cisco devices to define a VTEP.

![](/images/cisconxos/cisconxos_1_2_2.png)

The VXLAN segments are independent of the underlying network topology; conversely, the underlying IP network between VTEPs is independent of the VXLAN overlay. It routes the encapsulated packets based on the outer IP address header, which has the initiating VTEP as the source IP address and the terminating VTEP as the destination IP address.

VXLAN enables flexible placement of multitenant segments throughout the data center. It provides a way to extend Layer 2 segments over the underlying shared network infrastructure so that tenant workloads can be placed across different physical pods in the data center.


### VXLAN Control Plane Options

VXLAN scales based on how well VTEP devices handle broadcast, unknown unicast, and multicast (BUM) traffic. VTEP devices use host MAC address to VTEP IP address mappings to forward encapsulated frames across the IP transport network. A control plane has to take care of VTEP device discovery and the population of the mapping tables.

The two most widely used control planes for VXLAN technology with Cisco equipment are as follows:

*   Multicast control plane (flood-and learn)
*   Multiprotocol Border Gateway Protocol (MP-BGP) EVPN control plane

By using a multicast control plane, the VNI is mapped to an IP multicast group in the transport IP network. Each VTEP device is independently configured with this multicast group and participates in PIM routing. The multicast distribution tree for this group is built through the transport network based on the locations of participating VTEPs. Initially, the VTEPs only learn the MAC addresses of devices that are directly connected to them. Remote MAC address to VTEP mappings is learned through conversational learning. However, the requirement to enable multicast capabilities in the underlay network presents a challenge to some organizations because they do not want to enable multicast in their data centers or WANs.

In the VXLAN flood-and-learn mode, end-host information learning and VTEP discovery are both data-plane based, with no control protocol to distribute end-host reachability information among the VTEPs. To overcome the limitations of flood-and-learn VXLAN, Cisco VXLAN EVPN uses MP-BGP EVPN, as the control plane for VXLAN.

### VXLAN EVPN Control Plane

MP-BGP EVPN is a control protocol for VXLAN based on IETF RFC 8365. MP-BGP EVPN represents a VXLAN solution using a control plane that is not based on multicast and is more scalable than the VXLAN flood-and-learn mode. It introduces control-plane (instead of data plane) learning for end hosts behind remote VTEPs so that VMs or servers for each tenant have a unique identity across the fabric. It provides control-plane and data-plane separation. In addition, MP-BGP EVPN uses a unified control plane for both Layer 2 and Layer 3 forwarding in a VXLAN overlay network. So, you can build bridged overlays or routed overlays. While bridged overlays are simpler to deploy, routed overlays are easier to scale out.

Like other network routing control protocols, MP-BGP EVPN is designed to distribute Network Layer Reachability Information (NLRI) for the network. A unique feature of EVPN NLRI is that it includes both the Layer 2 and Layer 3 reachability information for end hosts that reside in the EVPN VXLAN overlay network. In other words, it advertises both the MAC and IP addresses of EVPN VXLAN end hosts. This capability forms the basis for VXLAN-integrated routing and bridging (IRB) support. Since MP-BGP enables isolation of groups of interacting agents, VMs/servers that belong to the same tenant are logically isolated from other tenants.

![](/images/cisconxos/cisconxos_1_2_3.png)

Layer 2 MAC addresses need to be distributed because VXLAN is a Layer 2 extension technology. Unlike a traditional VLAN, which is confined to a specific location in a network that remains within the Layer 2 and Layer 3 boundary, a VNI is a virtual Layer 2 segment in the overlay network. However, from the underlay network’s point of view, the VNI can span multiple discontiguous sites, reaching beyond the Layer 2 and Layer 3 boundaries of the underlay infrastructure. Traffic between end hosts in the same VNI needs to be bridged in the overlay network. Therefore, VTEP devices in a given VNI need to know about other MAC addresses of end hosts in this VNI. Distribution of MAC addresses through MP-BGP EVPN allows unknown unicast flooding in the VXLAN to be reduced.

Layer 3 host IP addresses are advertised through MP-BGP EVPN so that inter-VXLAN traffic can be routed to the destination end host through an optimal path. For inter-VXLAN traffic that needs to be routed to the destination end host, host-based IP routing can provide the optimal forwarding path to the destination host.

MP-BGP EVPN can also advertise the IP subnet prefix routes of VNIs. The prefix routes can be used to route traffic to the destination hosts when the host IP routes are missing: for instance, when the host IP routes have yet to be learned by the VTEPs through MP-BGP. VTEP can also advertise the prefix routes outside the VXLAN network if the subnets need to be routable and made known outside the VXLAN network.

In addition to host and subnet reachability advertisements, the VXLAN EVPN control plane provides VTEP peer discovery and authentication, which mitigates the risk of rogue VTEPs in the VXLAN overlay network. It also facilitates seamless and optimal workload mobility with the distributed anycast gateway function. Its ARP suppression feature provides early ARP termination, a localized ARP learning process, and minimized network flooding. The dynamic ingress replication enables a unicast alternative to a multicast underlay.

To summarize, for a data center network, the VXLAN MP-BGP EVPN control plane provides the following benefits:

*   Flexible workload placement that is not restricted with physical topology of the data center network.
    *  Place virtual machines anywhere in the data center fabric.

*   Optimal east-west traffic between servers within and across data centers.
    *  East-west traffic between servers, or virtual machines, is achieved by most specific routing at the first hop router. First hop routing is done at the access layer. Host routes must be exchanged to ensure the most specific routing to and from servers or hosts. Virtual machine mobility is supported by detecting new endpoint attachment when a new MAC address/IP address is seen directly connected to the local switch. When the local switch sees the new MAC/IP, it signals the new location to the rest of the network.

*   Eliminate or reduce flooding in the data center.
    *  Flooding is reduced by distributing MAC reachability information via MP-BGP EVPN to optimize flooding relating to Layer 2 unknown unicast traffic. Optimization of reducing broadcasts associated with ARP/IPv6 Neighbor solicitation is achieved by distributing the necessary information through MP-BGP EVPN. The information is then cached at the access switches. Address solicitation requests can be responded to locally without sending a broadcast to the rest of the fabric.

*   A standards-based control plane that can be deployed independent of a specific fabric controller, which provides:
    *  IP reachability information for the tunnel endpoints associated with a host and the network segment behind a specific tunnel endpoint.
    *  Distribution of host MAC reachability to reduce/eliminate unknown unicast flooding.
    *  Distribution of host IP/MAC bindings to provide local ARP suppression.
    *  Host mobility.
    *  A single address family (MP = BGP EVPN) to distribute both Layer 2 and Layer 3 route reachability information.

*   Segmentation of Layer 2 and Layer 3 traffic.
    *  Traffic segmentation is achieved with using VXLAN encapsulation, where VNI acts as segment identifier.

### MP-BGP EVPN VTEP Peer Discovery and Authentication

Before MP-BGP EVPN, VXLAN did not have a control-protocol-based VTEP peer-discovery mechanism or a method for authenticating VTEP peers. These limitations presented major security risks in real-world VXLAN deployments because they allowed easy insertion of a rogue VTEP into a VNI segment to send or receive VXLAN traffic. Hence, secure VTEP implementation is important, so traffic is only accepted from authenticated VTEPs whose information is learned via the MP-BGP EVPN control plane.

With the MP-BGP EVPN control plane, VTEPs use the following peer discovery and authentication mechanism:

![](/images/cisconxos/cisconxos_1_2_4.png)

1. The VTEP device first needs to establish BGP neighbor adjacency with other VTEPs or with Internal Border Gateway Protocol (iBGP) route reflectors, as in the example above. A route reflector in an MP-BGP EVPN control plane acts as a central point for BGP sessions between VTEPs. Instead of each VTEP peering with every other VTEP, the VTEPs peer with a spine device designated as a route reflector. For redundancy purposes, an extra route reflector can be utilized.

In addition to the BGP updates for end-host NLRI, VTEPs exchange the following information about themselves through BGP:

*   Layer 3 VNI
*   VTEP address
*   Router MAC address

1.  When a VTEP receives BGP EVPN route updates from a remote VTEP BGP neighbor, such as VTEP 1 from remote VTEP 2 in this example, it adds the VTEP address from that route advertisement to the VTEP peer list. Similarly, it adds VTEP 3 in the peer list after a BGP EVPN route update.

1.  Local VTEP uses this VTEP peer list as an allowed list of valid VTEP peers.

1.  If your local VTEP receives a packet from another VTEP, such as VTEP 4, which is not on that allow list, it considers the source of the packet as invalid or unauthorized.

1.  As a result, your local VTEP will discard VXLAN encapsulated traffic from an invalid VTEP.

For additional security, the existing BGP Message Digest 5 (MD5) authentication can be conveniently applied to the BGP neighbor sessions, so that switches cannot become BGP neighbors to exchange MP-BGP EVPN routes until they successfully authenticate each other with a preconfigured password. BGP neighbor authentication in MP-BGP EVPN is configured in the same way as previously supported in BGP, and the two neighbors will authenticate each other using the MD5 hash of the password phrase.

This example shows the BGP neighbor authentication in MP-BGP EVPN between two VTEPs, where VTEP 1 IP address is 10.200.200.101 and VTEP 2 IP address is 10.200.200.102. The VTEP 1 configuration is the following:

```bash
    switch1# show running-config | begin router
    router bgp 65500
      router-id 10.200.200.101
      log-neighbor-changes
      address-family ipv4 unicast
      neighbor 10.200.200.102
        remote-as 65500
        password 3 f4200cfc725bbd28
        update-source loopback0
        address-family ipv4 unicast
        address-family l2vpn evp
          send-community both
```

Similarly, VTEP 2 MP-BGP configuration is the following:

```bash
    switch2# show running-config | begin router
    router bgp 65500
      router-id 10.200.200.102
      log-neighbor-changes
      address-family ipv4 unicast
      neighbor 10.200.200.101
        remote-as 65500
        password 3 f4200cfc725bbd28
        update-source loopback0
        address-family ipv4 unicast
        address-family l2vpn evp
          send-community both
```

If you enter the authentication password as cleartext, the system will change the password to a Triple Data Encryption (3DES)-encrypted password in the configuration. As indicated above, both BGP neighbors should have the same password for successful authentication. After a VTEP device has established BGP neighbor adjacency with other VTEPs, you can display the VNI peer status and information using the show nve peers command, including its options to display details for a specific peer.

```bash
    switch#  show nve peers
    Interface  Peer-IP           State  LearnType  Uptime    Router-Mac
    ---------  ----------------  -----  ---------  --------  --------------
    nve1       10.200.200.102    Up     CP         1w3d      6412.2574.6ae7
    nve1       10.200.200.103    Up     CP         1w3d      7c69.f6df.e71f
```

**Note:** Control plane indicates the control plane learning.

```bash
    switch#  show nve peers peer-ip 10.200.200.102 detail
    Details of nve Peers:
    -------------------------------------------------------------
    Peer-IP: 10.200.200.102
        NVE Interface:          : nve1
        Peer State:             : Up
        Peer Uptime:            : 1w3d
        Router- Mac:            : 6412.2574.6ae7
        Peer First VNI:         : 30210
        Configured VNIs:        : 30200, 30210, 50000, 50001
        Provision State:        : add-complete
        Route-Update:           : Yes
        Peer-Flags:             : DisableLearn
        Learnt CP VNIs:         : 30200, 30210
    ---------------------------------------------------------------
```

### MP-BGP EVPN Control Plane IRB Support

####    L2VPN EVPN Address Family

The EVPN NLRI is carried in BGP using the BGP multiprotocol extension with a new address family called Layer 2 VPN (L2VPN) EVPN. Similar to the VPNv4 address family in the BGP MPLS-based IP VPN (RFC 4364), the L2VPN EVPN address family for EVPN uses route distinguishers (RDs) to maintain uniqueness among identical routes in different virtual routing and forwarding (VRF) instances, and route targets (RTs) to define the policies that determine how routes are advertised and shared by different VRF instances.

A route distinguisher is an 8-bit octet number used to distinguish one set of routes (one VRF instance) from another. It is a unique number prepended to each route, so that if the same route is used in several different VRF instances, BGP can treat them as distinct routes. The route distinguisher is transmitted along with the route through MP-BGP when EVPN routes are exchanged with MP-BGP peers.

Route targets can be applied to a VRF instance to control the import and export of routes between this instance and other VRF instances. The route-target attributes for a route are distributed in the form of a BGP extended community attribute, so the BGP configuration on the devices that run MP-BGP EVPN must be enabled to generate or process extended community attributes.

In the Cisco NX-OS implementation, the BGP route distinguisher and route target can be generated automatically for ease of configuration. The BGP route distinguisher can be derived automatically from the VNI and BGP router ID of the VTEP switch, and the BGP route target can be generated automatically as the BGP autonomous system (AS) VNI. Alternatively, you can manually configure the BGP route distinguisher and route target.

If all the MP-BGP EVPN VTEPs in a network are Cisco Nexus switch platforms, the recommended approach is to use autogenerated route-distinguisher and route-target values. If multiple vendors’ VTEP devices are interoperating, the recommended approach is to manually configure the values to avoid possible problems caused by the differences in vendors’ implementations. For eBGP deployment scenarios in which VTEPs are in different BGP domains, the BGP route targets must be manually assigned.

####    MP-BGP EVPN Address Learning

The MP-BGP EVPN control plane provides IRB by distributing both Layer 2 and Layer 3 reachability information for the end host residing in the VXLAN overlay networks. Each VTEP performs local learning to obtain MAC and IP address information from its locally attached hosts and then distributes this information through the MP-BGP EVPN control plane. Hosts attached to remote VTEPs are learned remotely through the MP-BGP control plane. This approach reduces network flooding for end-host learning and provides better control over end-host reachability information distribution.

The MP-BGP EVPN control plane operation for end-host NLRI learning and distribution in an MP-iBGP EVPN using route reflectors, includes the following main steps:

![](/images/cisconxos/cisconxos_1_2_5.png)

1.  A VTEP in the MP-BGP EVPN learns the MAC addresses and IP addresses of locally attached end hosts through local learning. This learning can be based on the local data plane using the standard Ethernet and IP learning procedures, such as source MAC address learning from the incoming Ethernet frames and IP address learning when the hosts send Gratuitous Address Resolution Protocol (GARP) and Reverse ARP (RARP) packets or ARP requests for the gateway IP address on the VTEP.

1.  After learning the local-host MAC and IP addresses, the VTEP advertises the host information in the MP-BGP EVPN control plane so that this information can be distributed to other VTEPs. This approach enables EVPN VTEPs to learn the remote end hosts in the MP-BGP EVPN control plane. The EVPN routes are advertised through the L2VPN EVPN address family. The BGP L2VPN EVPN routes include the following information:

*   Route Distinguisher
*   MAC address length: 6 bytes
*   MAC address: Host MAC address
*   IP address length: 32 or 128
*   IP address: Host IP address (IPv4 or IPv6)
*   Layer 2 VNI: VNI of the bridge domain to which the end host belongs
*   Layer 3 VNI: VNI associated with the tenant VRF routing instance

MP-BGP EVPN uses the BGP extended community attribute to transmit the exported route targets in an EVPN route. When an EVPN VTEP receives an EVPN route, it compares the route-target attributes in the received route to its locally configured route-target import policy to decide whether to import or ignore the route. This approach uses the decades-old MP-BGP VPN technology (RFC 4364) and provides scalable multitenancy, in which a node that does not have a VRF instance locally does not import the corresponding routes.

When a VTEP switch originates MP-BGP EVPN routes for its locally learned end hosts, it uses its own VTEP address as the BGP next hop. This BGP next hop must remain unchanged through the route distribution across the network because the remote VTEP must learn the originating VTEP address as the next hop for VXLAN encapsulation when forwarding packets for the overlay network.

1.  The route reflector works like neighbor relationship aggregation point and provides BGP update exchange.

**Note:** The route reflector implementation eliminates the need for a full mesh iBGP peering between the VTEPs.

1.  As a result of the MP-BGP EVPN control plane operation, every VTEP knows how to reach every end system.

Thus, the MP-BGP EVPN address learning process is decoupled from the underlaying protocol. The underlay network provides IP reachability for all the VTEP addresses that are used to route the encapsulated VXLAN packets toward the egress VTEP through the underlay network. The network devices in the underlay network need to maintain routing information only for the VTEP addresses. They don’t need to learn the EVPN routes. This approach simplifies the underlay network operation and increases its stability and scalability.


### MP-BGP EVPN Route Types

The EVPN NLRI leverages the following route types in the advertisements:

*   **Route type 1 — Ethernet auto-discovery (AD) route:** Advertised on per EVPN instance (represented by the VNI) and per Ethernet segment basis. This route type is used for mass withdrawal of MAC addresses (fast convergence during failure scenarios) and aliasing for load balancing.

*   **Route type 2 — MAC/IP advertisement route:** These routes are per-VLAN routes, so only VTEPs that are part of a VNI require these routes. The host's IP and MAC addresses are advertised to the peers within NRLI. The control plane learning of MAC addresses reduces unknown unicast flooding.

*   **Route type 3 — Inclusive multicast Ethernet tag route:** This route establishes the connection for BUM traffic (multicast tunnel endpoint peer discovery) from a source to a remote VTEP.

*   **Route type 4 — Ethernet segment route:** Used for designated forwarder (DF) election for BUM traffic. It also can be used for redundancy group discovery,

*   **Route type 5 — IP prefix route:** The IP prefixes are advertised independently of the MAC-advertised routes. With EVPN IRB, host route /32 is advertised using route type 2 and subnet /24 is advertised using route type 5.

### Host and Subnet Route Advertisements

VXLAN with an MP-BGP EVPN control plane supports MAC address-only advertisements as route type 2, since the MAC attributes are mandatory. In addition, the IP information is optional for route type 2, such as in MAC and IP address advertisement.

The following example depicts the MAC address-only route advertisements:

![](/images/cisconxos/cisconxos_1_2_6.png)

The host MAC advertisement (route type 2) has different elements, including the following:

*   MAC address
*   L2VNI (MPLS Label 1)
*   Route target for MAC-VRF

The output of the **show bgp l2vpn evpn 0000.3001.1101** command can help you verify the received MAC address-only route advertisement:

![](/images/cisconxos/cisconxos_1_2_7.png)

The following example shows the MAC and IP route advertisements.

![](/images/cisconxos/cisconxos_1_2_8.png)

The MAC and IP advertisement (route-type 2) has different elements, including the following:

*   MAC and IP addresses
*   L2VNI (MPLS Label 1)
*   Route target for MAC-VRF
*   L3VNI (MPLS Label 2)
*   Route target for IP-VRF
*   Router MAC

Similarly, you can verify the received MAC and IP route advertisement using the **show bgp l2vpn evpn 192.168.10.101** command:

![](/images/cisconxos/cisconxos_1_2_9.png)

The following example depicts the subnet route advertisements:

![](/images/cisconxos/cisconxos_1_2_10.png)

The internal and external subnet prefixes (route-type 5) have different elements, including the following:

*   IP Prefix
*   L3VNI (MPLS Label)
*   Route target for IP-VRF
*   Router MAC

Similarly to host prefix verification, you can display the subnet route advertisement using the **show bgp l2vpn evpn 192.168.10.0** command:

![](/images/cisconxos/cisconxos_1_2_11.png)


