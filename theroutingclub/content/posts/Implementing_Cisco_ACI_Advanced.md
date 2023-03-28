---
title: "Implementing Cisco ACI Advanced"
date: 2022-12-16
draft: false
tags: [Cisco, ACI, Cisco ACI, PBR, Multi-Site]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "This Post is to document the learnings from - Implementing Cisco ACI Advanced Course"
# AuthorName: "Nilesh Gore"
---

#   1)  Describing Cisco ACI Advanced Packet Forwarding

##  1.1)    Introduction

In Cisco Application-Centric Infrastructure (ACI), MAC address and its IP addresses are stored as an endpoint. Some endpoints are stored in the endpoint table on leaf switches. Local endpoints are the main source of endpoint information for the entire Cisco ACI fabric. Each leaf is responsible for reporting its local endpoints to the Council of Oracle Protocol (COOP), database, which is located on each spine switch. COOP database on spine switches stores all the endpoint information in the fabric and is synchronized among the spine switches. Each endpoint in the endpoint table has the location and the endpoint group (EPG) for the MAC address and/or IP addresses. This information is used to route or switch packets, and used to apply a contract. Cisco ACI learns endpoints (both MAC and IP addresses) from data plane, which is different from other normal switches. IP data plane learning is very powerful and effective but sometimes it is too sensitive. Here, you will learn the details of the endpoint learning to be able to understand and design the Cisco ACI fabric and its packet forwarding.

##  1.2)    Packet Forwarding between Leaf Switches

Prior to diving into the endpoint learning detail, you will see how a packet flows through the fabric, and how different bridge domain modes influence the data flow.

The following figure illustrates endpoint identity and location learning along the forwarding path.

![](/images/aciadvanced/aciadvanced_1_2_1.png)

The following figure presents a Cisco ACI topology that is used in various packet forwarding scenarios.

![](/images/aciadvanced/aciadvanced_1_2_2.png)

Four scenarios will be presented:

*   Source leaf knows the destination—destination is on the same leaf
*   Source leaf knows the destination—destination is on a different leaf
*   Source leaf does not know the destination—decides to use Spine-Proxy mode
*   Source leaf does not know the destination—decides to use Flood mode

Virtual Extensible LAN (VXLAN) packet acronyms:\

*   dXXXo: Outer Destination XXX (dIPo = Outer Destination IP)
*   sXXXo: Outer Source XXX (sIPo = Outer Source IP)
*   dXXXi: Inner Destination XXX (dIPi = Inner Destination IP)
*   sXXXi: Inner Source XXX (sIPi = Inner Source IP)
*   Group IP Outer (GIPo): Outer Multicast Group IP
*   Virtual Network Identifier (VNID)

### Source Leaf Knows the Destination—on the Same Leaf

If the destination MAC address is known to an ingress leaf and it is on the same leaf, the packet is forwarded to the local port, like traditional Layer 2 or Layer 3 forwarding.

![](/images/aciadvanced/aciadvanced_1_2_3.png)

### Source Leaf Knows the Destination—on a Different Leaf

If the destination MAC address is known to an ingress leaf and it is not on the local leaf, the packet is forwarded directly to the destination leaf. Look at the following example where the traffic flows between two endpoints; MAC or IP addresses of both endpoints are known. Endpoint 1-1 (EP1-1) is known as a local to leaf 1 with tunnel endpoint 1 (TEP 1) and the endpoint 3-2 (EP 3-2) is known as a remote endpoint to this leaf. When traffic is going from EP 1-1 to EP 3-2, leaf 1 will add an outer header and encapsulate the packet with TEP 1 (leaf's 1 TEP) as the source address and TEP 2 (leaf's 2 TEP) as the destination address. The forwarding between two leaf switches over infra virtual routing and forwarding (VRF) (VRF overlay-1) is then based on that outer header addresses. When a packet comes to one of the spine switches, the spine will forward the packet to leaf 2 based on the outer header destination TEP. Leaf with TEP 2 first checks the outer header and finds out that TEP 2 is indeed the final destination of the packet. So Leaf 2 decapsulates the packet (remove the outer header) and forward the packet to the correct port to reach EP 3-2 based on inner header addresses. The forwarding in the destination leaf (leaf 2) will be performed in the bridge domain or VRF that is carried in the VXLAN header in a format of Virtual Network Identifier (VNID).

![](/images/aciadvanced/aciadvanced_1_2_4.png)

### Source Leaf Does Not Know the Destination—Spine-Proxy Mode

If the destination endpoint address is not known to an ingress leaf, then there are two options.

The first option is spine-proxy. Happens mainly with the following three scenarios:

*   The packet is a unicast Layer 3 routed packet and the destination IP (unknown to the ingress leaf) belongs to one of the BD subnets on the ingress leaf.
*   The packet is a unicast Layer 2 switched packet and the BD Layer 2 Unknown Unicast mode is set to hardware-proxy.
*   The packet is a broadcast ARP request, the BD ARP Flooding is disabled, and the ARP target IP (unknown to the ingress leaf) belongs to one of the BD subnets on the ingress leaf.

In this example of spine-proxy, the destination is the endpoint EP 3-1. Leaf 1 does not know where the address of the endpoint EP 3-1 resides (possibly it has timed out or it is a silent host or EP 3-1 never had communication with anyone in leaf 1) and it is one of the three scenarios that are mentioned above. So, it will encapsulate the packet with an anycast TEP address of spine as a destination for spine-proxy. When spine receives the packet, it checks the outer destination address and finds out the destination is its own proxy TEP address. So, spine will decapsulate the packet and check its COOP database to look for the packet inner destination address. In the example, it finds out the destination endpoint is on leaf 2. Hence, it encapsulates the packet with outer destination IP address of TEP 2. When the packet reaches leaf 2, the behavior is the same as in the previous scenario: leaf 2 decapsulates the packet (remove the outer header) and forward the packet to the correct port to reach EP 3-1 based on the inner header addresses.

![](/images/aciadvanced/aciadvanced_1_2_5.png)

### Source Leaf Does Not Know the Destination—Flood Mode

The second option (and fourth overall forwarding scenario presented) is flood. Happens in many scenarios such as Layer 2 multicast, Layer 2 broadcast traffic and so on. In comparison with the previous example, there are two more scenarios:

*   The packet is a unicast Layer 2 switched packet and the BD Layer 2 Unknown Unicast mode is set to flood.
*   The packet is a broadcast ARP request and the BD ARP Flooding is enabled.

In this option, the packet is flooded within the bridge domain. For example, leaf 1 does not know where the MAC address of EP 2-2 resides, so ACI will flood the packet to all interfaces in the bridge domain.

In ACI, forwarding tag (FTAG) trees are built to load-balance multidestination traffic and to avoid any loop within the infra VRF. One of the spine switches is chosen as an FTAG tree root. Root builds branches to every leaf switch directly connected to that particular spine switch. Then, as shown in the example below, leaf 3, for example, spread another branch to reach the other spine switch. So, the path to all the nodes is now built with a loop-free path. Packets are then flooded by following the FTAG tree branches; a mechanism to ensure that the packet is sent to each node only once, without any duplicates. To avoid wasting resources (that is to utilize all the links between spine and leaf switches), there are up to 12 FTAG trees supported in Cisco ACI. The ingress leaf assigns an FTAG to the packets when forwarding them to the spine. The FTAG is assigned in the packet as part of the destination multicast address.

When a packet is flooded within a bridge domain, packets are encapsulated in a multicast IP called Group IP Outer (GIPo) instead of TEP of leaf nodes. Each bridge domain is assigned one GIPo internally for this purpose. You define a multicast IP range for these purposes during your initial Cisco Application Policy Infrastructure Controller (APIC) configuration. GIPo comprises of two parts: one is a bridge domain multicast IP address and the other is FTAG ID. FTAG ID is used for a node to identify which FTAG tree (out of 12 possible) a packet needs to follow.

In the example, leaf 2 receives the packet with outer destination IP of bridge domain 1 (BD 1) multicast IP address. Because bridge domain BD 1 resides on that leaf switch, leaf 2 will decapsulate the packet and send it to appropriate endpoints. Leaf 3 does not have any EPGs using bridge domain BD 1 so leaf 3 will not decapsulate the packet but will only forward the packet to the appropriate spine switch based on the FTAG tree.

![](/images/aciadvanced/aciadvanced_1_2_6.png)

##  1.3)    Endpoint Learning

The forwarding scenarios that are mentioned in the previous topic depends on whether the ingress leaf knows the destination endpoint (and some other configurations such as BD Layer 2 Unknown Unicast). In this topic, you will learn how ACI leaf learns an endpoint.

An endpoint consists of one MAC address and zero or more IP addresses. Each endpoint represents a single networking device.

In a traditional network, three tables are used to maintain the network addresses of external devices:

*   A MAC address table for Layer 2 Forwarding
*   A Routing Information Base (RIB) for Layer 3 forwarding
*   An Address Resolution Protocol (ARP) table for the association of IP addresses and MAC addresses

Cisco ACI replaced the MAC address table and ARP table with a single table called the endpoint table. This change implies that Cisco ACI learns that information in a different way than in a traditional network. Cisco ACI learns MAC and IP addresses in hardware by looking at the packet source MAC address and source IP address in the data plane instead of relying on ARP to obtain a next-hop MAC address for IP addresses. This approach reduces the amount of resources that are needed to process and generate ARP traffic. It also allows detection of IP address and MAC address movement without the need to wait for Gratuitous Address Resolution Protocol (GARP) if some traffic is sent from the new host. Although Cisco ACI uses the endpoint table instead of the MAC address and ARP tables, it still uses the RIB and the ARP table (for Layer 3 Outside).

| Traditional Network                                                          | Cisco ACI                                                                   |
|------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| RIB: - IPv4 addresses (/32 and non-/32) - IPv6 addresses (/128 and non-/128) | RIB: - IPv4 addresses (non-/32) - IPv6 addresses (non-/128)                 |
| MAC address table: MAC addresses                                             | Endpoint table: MAC and IP addresses (/32 or /128 only)                     |
| ARP table: Relationship of IP to MAC                                         | ARP table: Relationship of IP to MAC (only for Layer 3 Outside connections) |

![](/images/aciadvanced/aciadvanced_1_3_1.png)

Forwarding table lookup order:

*   Endpoint table (**show endpoint**)
*   RIB (**show ip route**)

A leaf switch has two types of endpoints:

*   Local endpoints
*   Remote endpoints

**Note:** Local endpoint information is stored in endpoint table on each leaf. Remote endpoint is stored in endpoint table on each leaf only when a conversation to the endpoint is happening (conversational learning).

### Checking the Endpoints

There are several methods to check the endpoints in Cisco ACI. The first is by using Cisco APIC user interface. With the user interface, you can check which EPG has which endpoint and on which leaf switch and interface the endpoint was learned. Even though it is a very handy tool for management purposes, it is not showing the actual endpoint table that is used for forwarding.

From APIC user interface (Fabric perspective):

![](/images/aciadvanced/aciadvanced_1_3_2.png)

When you want to do endpoint verification or troubleshooting, you need to go to CLI of a leaf switch and use **show endpoint** command. The following example shows that **show endpoint** command in leaf switch CLI, which is similar to **show mac address-table** command in a standard switch except for the fact that it has more information such as IP address.

From Leaf CLI (Leaf perspective):

```shell
leaf1# show endpoint vrf TK:VRF1
Legend:
s -arp              O -peer-attached    a -local-aged       S -static
V -vpc-attached     p -peer-aged        M -span             L -local
B -bounce           H -vtep
+--------------+---------------+-----------------+--------------+---------+
    VLAN/         Encap           MAC Address       MAC Info/    Interface
    Domain        VLAN            IP Address        IP Info
+--------------+---------------+-----------------+--------------+---------+
TK:VRF1                             192.168.1.11                   tunnel8
17/TK:VRF1      vxlan-15826915    0000.1111.5151                   tunnel8
19                      vlan-5    0000.0000.5151 L                  eth1/1
TK:VRF1                 vlan-5      192.168.0.51 L                  eth1/1
```

There are four different main endpoint types in Cisco ACI.

The first type of endpoint is **physical local endpoint (PL)**. This endpoint is learned and physically attached to the local leaf.

![](/images/aciadvanced/aciadvanced_1_3_3.png)

The second type of endpoint is **virtual local endpoint (VL)**. This endpoint is still local to the leaf, but it is behind a virtual switch (Cisco Application Virtual Switch [AVS] or Cisco ACI Virtual Edge [AVE]).

![](/images/aciadvanced/aciadvanced_1_3_4.png)

With both local endpoint types, you see a flag L (for local) from the **show endpoint** command output.

The third endpoint type is called **remote endpoint (XR)** which represents an endpoint on another leaf. Remote endpoint does not have the L flag in the **show endpoint** command output.

![](/images/aciadvanced/aciadvanced_1_3_5.png)

The fourth endpoint type is **on-peer endpoint (O)**. This endpoint is on another leaf but that leaf is a vPC peer of your local leaf switch and the endpoint is connected to an orphan port on that vPC peer. This endpoint type has a specific O flag that is shown in the **show endpoint** command output.

![](/images/aciadvanced/aciadvanced_1_3_6.png)

**Note:**If an endpoint is connected to a vPC port, it is shown as local on both leaf switches that are members of the same vPC domain.

### VLAN Types in Cisco ACI

VLAN in Cisco ACI is just an identifier to classify endpoints to each EPG. Bridge domain is the Layer 2 domain instead of VLAN.

There are three types of identifiers in Cisco ACI:

*   Access Encap VLAN ID: used to communicate to external device. You configure this VLAN ID on the ACI and also on the external device—a VLAN on the wire. This VLAN ID is also used as an identifier for a security group, EPG. An EPG can have more than one identifier, it can also be a VXLAN as shown in the example below. Access Encap VLAN ID is internally mapped to platform-independent VLAN ID.

*   Platform-Independent (PI) VLAN ID: used internally on a particular node. PI VLAN ID can map the internal ID to an access encap VLAN or VXLAN ID, or bridge domain switch virtual interface (SVI) ID. You can use the PI VLAN ID to get information about the EPG. Even though you can have the same access encap VLAN IDs on another leaf, PI VLAN IDs may be different. Their functionality is nevertheless the same.

*   VXLAN ID: to make forwarding across the nodes possible, you need to have an identifier that is shared among all the nodes. This identifier is VNID. Mainly this ID is used as a bridge domain VNID for bridging the traffic or as a VRF VNID for routing the traffic.

![](/images/aciadvanced/aciadvanced_1_3_7.png)

In the **show endpoint** command, you can type an IP address of an endpoint or you can type a MAC address of an endpoint. MAC and IP address are combined in an endpoint table as a single entry, which is why you can see IP and MAC address in the endpoint table output below. In the middle of an output, you can see the access encap VLAN, which is the VLAN ID used on the wire. On the right side of the output, you can see the interface on which the endpoint was learned. On the left side, you can see the PI VLAN ID that can be used to get information about the EPG. You can check the EPG information by using the command **show vlan id vlan-id**. VLAN ID used in this command is not an access encap VLAN ID but PI VLAN ID. If you look at the example below, you can see that PI VLAN 19 belongs to the EPG 1.

![](/images/aciadvanced/aciadvanced_1_3_8.png)


### Local and Remote Endpoint Learning

The following figure describes endpoint identity and location learning along the forwarding path:

![](/images/aciadvanced/aciadvanced_1_3_9.png)

How to learn a MAC address as a local endpoint:

*   When a packet enters the ACI leaf from a downlink as opposed to uplinks, the source MAC address is learned as a local endpoint; the same as normal switches.

*   Downlink is the interface that is connected to external devices and uplink is the interface that is connected to other ACI nodes (typically spine).

How to learn an IP address as a local endpoint:

*   There are two scenarios for ACI leaf to do this via data plane:
    *   When a packet enters the ACI leaf from a downlink as opposed to uplinks and the packet is routed instead of switched, the source IP address is learned as a local endpoint.
    *   When an ARP request enters the ACI leaf from a downlink as opposed to uplinks, the sender IP in the ARP header is learned as a local endpoint.

**Note:** A routed packet here means the packet whose destination MAC is the ACI BD MAC that makes the packet that is routed by the ACI fabric.

ACI can also learn IP address via ARP requests generated by its own control plane just like any other switches and routers. For all of them, the Unicast Routing in the BD must be enabled.

Please note that MAC address will be learned together in the above scenario if it is not learned yet.

If an endpoint is learned as local:

![](/images/aciadvanced/aciadvanced_1_3_10.png)

**How to learn a MAC address as a remote endpoint:**

When a packet enters the ACI leaf on an uplink from another leaf and the packet is Layer 2 switched packet, the source MAC address in the inner header is learned as a remote endpoint.

**How to learn an IP address as a remote endpoint:**

When a packet enters the ACI leaf on an uplink from another leaf and the packet is Layer 3 routed packet, the source IP address in the inner header is learned as a remote endpoint.

If an endpoint is learned as remote:

![](/images/aciadvanced/aciadvanced_1_3_11.png)

To check tunnel interface (TEP):

![](/images/aciadvanced/aciadvanced_1_3_12.png)

The Cisco ACI Fabric routes traffic if the destination MAC of the packet is the ACI router-mac; otherwise, the fabric bridges traffic, which is standard Layer 2 and Layer 3 behavior.

**Note:** Refer to the Cisco ACI Fabric Endpoint Learning White Paper to review specific use cases for endpoint IP address learning. (https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739989.pdf)

### COOP Database

Although both local and remote endpoints are learned from the data plane, remote endpoints are merely a cache, local to each leaf. Local endpoints are the main source of endpoint information for the entire Cisco ACI fabric. Each leaf is responsible for reporting its local endpoints to the COOP database, which is located on each spine switch. Spine switch stores these endpoints in COOP database and synchronizes with other spine switches.

Because this database is accessible, each leaf does not need to know about all the remote endpoints to forward packets to another leaf. When leaf switch does not know the destination endpoint, leaf can forward packet to spine switch to let spine switch decide where to send. This forwarding behavior is called spine proxy.

Normally, spine switch does not push COOP database entries to each leaf switch. Spine switch just receives and stores.

The COOP, running in the fabric ensures the following:

*   All spine switches maintain a consistent copy of endpoint address and location information.
*   All spine switches maintain the endpoint identity to the location-mapping database.

![](/images/aciadvanced/aciadvanced_1_3_13.png)

COOP data path communication provides high priority to transport using secured connections between leaf and spine. COOP is enhanced to apply the MD5 option to protect COOP messages from malicious traffic injection. The Cisco APIC controller and switches support COOP protocol authentication.

Example commands to check a COOP database on a spine:

![](/images/aciadvanced/aciadvanced_1_3_14.png)

### Bounce Entry

There are several scenarios in which an endpoint moves between two Cisco ACI leaf switches, such as a failover event or a virtual machine migration in a hypervisor environment. Cisco ACI data plane endpoint learning detects these events quickly and updates the Cisco ACI endpoint database on a new leaf. In addition to data plane learning, Cisco ACI uses bounce entries to manage the old endpoint information on the original leaf.

When a new local endpoint is detected on a leaf, the leaf updates the COOP database on spine switches with its new local endpoint. If the COOP database has already learned the same endpoint from another leaf, COOP will recognize this event as an endpoint move and report this move to the original leaf that contained the old endpoint information. The old leaf that receives this notification will delete its old endpoint entry and create a bounce entry, which will point to the new leaf. A bounce entry is basically a remote endpoint that is created by COOP communication instead of data plane learning.

Look at the example of Cisco ACI endpoint movement and bounce entry. In the following figure, leaf 1 learns the remote endpoint location for IP B pointing to leaf 2 from the data plane.

![](/images/aciadvanced/aciadvanced_1_3_15.png)

The endpoint with MAC B and IP B on leaf 2 moves to leaf 3, and the new local endpoint is created on leaf 3. This new local endpoint is reported to the COOP database on the spine switches.

![](/images/aciadvanced/aciadvanced_1_3_16.png)

Spine switches will then notify leaf 2 about the move of endpoint B, and leaf 2 will install bounce entries for MAC B and IP B. Bounce entries are basically the same as remote endpoints. Hence two bounce entries, for the MAC and IP addresses, are created.

![](/images/aciadvanced/aciadvanced_1_3_17.png)

At this point, leaf 1 still has the old remote endpoint for IP B, which still points to the old location: leaf 2. If a packet is sent from leaf 1 to IP B, leaf 1 forwards it to leaf 2, instead of leaf 3, based on its remote endpoint cache. Because of the bounce entries, leaf 2 is already prepared for this sort of forwarding from leaf switches with old remote endpoints. Leaf 2 will then bounce the packet to the new leaf 3 based on its bounce entries.

![](/images/aciadvanced/aciadvanced_1_3_18.png)

This bounce entry is a backup mechanism for this type of scenario. Therefore, the bounce entry will not be used if the new traffic from IP B on leaf 3 reaches leaf 1 before leaf 1 sends packets to IP B, because the old remote endpoint on leaf 1 will be updated directly by the data plane traffic from the new leaf.

The advantage of this implementation is scale. No matter how many leaf switches have learned endpoint information, only three components will need to be updated after an endpoint moves. The three components are the COOP database, the new leaf switch to which the endpoint has moved, and the old leaf switch from which the endpoint has moved. Eventually, all other leaf switches in the fabric will update their information about the location of the endpoint through data plane traffic.

##  1.4)    Examine Local and Remote Endpoint Learning

### Examine Local and Remote Endpoint Learning

You will explore your Cisco Application Centric Infrastructure (ACI) environment that has been integrated with VMware vCenter using the automated method that is based on a dynamic VLAN pool. Initially the VMs will belong to a vSphere Distributed Switch (VDS) provisioned on the vCenter by the Cisco Application Policy Infrastructure Controller (APIC). The ESXi host, along with the VDS, is dual-homed to the leafs over a vPC connection.

![](/images/aciadvanced/aciadvanced_1_4_1.png)


After testing connectivity through the VDS and the fabric, you will migrate the vSphere solution to two standard vSwitches, each attached to a single leaf. This migration will allow you to examine certain scenarios that could not be observed in a setup that only has one vPC peer, such as remote (cache) endpoint learning.





##  1.6)    NIC teaming to ACI Fabric

Servers can be connected to the Cisco ACI fabric through network interface card (NIC) Teaming in several ways.

The most commonly used NIC teaming options for dual attached servers are:

*   Virtual Port Channel (vPC)
*   Active/standby NIC Teaming
*   Port Channel for active/active NIC Teaming

![](/images/aciadvanced/aciadvanced_1_6_1.png)

### Active/Standby NIC Teaming

With active/standby NIC Teaming, one interface is active and one or more is in a standby state.

There are different implementations of the failover process depending on the bonding implementation:

*   The MAC address of the active interface stays identical after a failover, so there is no need to remap the IP address of the server to a new MAC address.

*   When a failover happens, the newly active interface uses its own MAC address to send traffic. In this case, the IP-to-MAC mapping must be updated on all the servers in the same Layer 2 domain. Therefore, with this type of implementation, the server sends a GARP/RARP request after a failover.

With the first implementation, the bridge domain configuration does not require any change if the newly active interface starts sending traffic immediately after the failover. Since the endpoint information (MAC, IP and its combination) didn’t change, ACI leaf simply detects the endpoint move via the packet from the new interface with the same MAC and IP. The endpoint to TEP mapping in the COOP database on spine switches is also updated accordingly just like a normal endpoint move.

With the second implementation, the bridge domain must be configured for ARP flooding for the GARP request to reach the servers in the bridge domain. If ARP flooding is disabled and Unicast Routing is enabled, instead of flooding the GARP, ACI tries to route it to the target IP, which is the source of the GARP in the case of Gratuitous ARP, resulting in the GARP not being forwarded to anybody in the BD. Also, when the GARP packet enters the ACI leaf, it triggers an update in the Endpoint table for the IP-to-MAC mapping regardless of whether ARP flooding is enabled. There is a corner case where this endpoint update does not happen. In such cases, you may need to enable GARP-based detection for EP Move Detection Mode. See ACI Fabric Endpoint Learning white paper for details (https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739989.html#_Toc18440060).

### Active/Active NIC Teaming

Servers configured with NIC Teaming active/active, such as Transmit Load Balancing, send the same source IP from multiple NIC cards with different MAC addresses, causing IP flapping between those two MAC addresses on ACI endpoint learning due to the IP data plane learning. In a normal switch, which may not be a problem because it relies only on ARP requests to map an IP to a MAC.

With these types of servers, the best connectivity option is to change the teaming to one of the following options:

*   Active/standby NIC Teaming
*   MAC pinning or equivalent, like Microsoft Hyper-V with Switch-Independent Teaming Hyper-V Port
*   Port Channel for active/active scenario

If the teaming configuration cannot be changed, you could disable the IP data plane learning in the VRF configuration. With the IP data plane learning disabled, the Endpoint table is updated only by the control plane just like any other switches and ARP will be the single source of MAC-IP mapping.

When the server uses active/active teaming but still source the traffic with a single MAC and IP, which will not cause the IP flapping mentioned above. Instead, it causes the flapping of the entire endpoint (both MAC and IP) between two uplinks. Even for a normal switch, it is an issue that is exposed as a MAC flapping, which is typically observed with IP hashing load balancing in VMware vSwitch. In such cases, ensure to configure a port channel or vPC on ACI so that it can handle those links logically as one.

##  1.7)    Endpoint Learning Optimizations

Various configuration knobs are available to set options for Cisco ACI endpoint learning.

Some of the endpoint learning optimization options are as follows:

*   Limit IP learning to subnet (bridge domain-level option)
*   IP data plane learning (VRF-level option)
*   Endpoint loop protection (global configuration option)
*   Enforce subnet check (global configuration option)
*   Rogue endpoint control (global configuration option)

### Limit IP Learning to Subnet (Bridge Domain-Level)

If limit IP learning to subnet option is enabled, the local endpoint IP learning will be limited to only IP addresses for subnets configured on the bridge domain. This option does not limit remote endpoint IP learning. This option is enabled by default.

![](/images/aciadvanced/aciadvanced_1_7_1.png)

If this option is enabled on a bridge domain that had the option that is disabled, endpoint IP addresses that do not belong to the bridge domain subnet are flushed.

If this option is disabled on a bridge domain that had the option that is enabled, Cisco ACI does not flush endpoint IP addresses learned on the bridge domain.

Look at the following example: if a bridge domain is configured with a subnet address of 192.168.1.254/24, the fabric does not learn a local endpoint IP address, such as 192.168.2.1/24, that is outside this range. This behavior prevents unnecessary IP learning, as shown in the following figure.

![](/images/aciadvanced/aciadvanced_1_7_2.png)

Although this feature prevents local IP learning, the local leaf still learns the MAC address, and the remote leaf still learns the IP and MAC addresses (although the local leaf does not learn the IP address, it does not drop the packet). For example, leaf 1 does not learn 192.168.2.1, but it learns MAC B, and leaf 2 learns 192.168.2.1 and MAC B.

![](/images/aciadvanced/aciadvanced_1_7_3.png)

### Enforce Subnet Check

Cisco ACI offers two similar configurations that are related to limiting the data plane learning of endpoints’ IP addresses:

*   Per-bridge domain limit IP address learning to subnet
*   Global enforce subnet check knob

Enforce subnet check feature ensures that Cisco ACI learns endpoints whose IP addresses belong to the bridge domain subnet. This feature also ensures that leaf switches learn remote entries whose IP addresses belong to the VRF that they are associated with, preventing the learning of IP addresses that are not configured as subnets on the bridge domains of the VRF.

You can enable the enforce subnet check under **System Settings > Fabric Wide Settings.**

![](/images/aciadvanced/aciadvanced_1_7_4.png)

This feature enforces subnet checks at the VRF level when Cisco ACI learns the IP address as an endpoint from the data plane. Although the subnet check scope is the VRF instance, this feature can be enabled and disabled only globally under fabric wide setting policy. You cannot enable this option only in one VRF instance. This feature is disabled by default.

Enabling enforce subnet check clears the remote entries in Endpoint table on the leaf switch and prevents learning remote entries for a short amount of time. The entries in the COOP database are not cleared, hence traffic forwarding keeps working even during the configuration change.

**Note:** While no disruption is expected when enabling enforce subnet check, there is the possibility that a given network is working with traffic from subnets that do not belong to the VRF. If so, enabling this feature will cause interruption of traffic flows.

Enforce subnet check works as follows:

*   **On the ingress leaf (local endpoint learning):** The option enforces bridge domain–level subnet checks for local endpoint learning. When this feature is enabled, the Cisco ACI leaf learns an IP address and MAC address as a new local endpoint only when the source IP address of the incoming packet belongs to one of the ingress bridge domain subnets. This behavior is almost the same as limit IP learning to subnet option under the bridge domain. The difference is that limit IP learning to subnet limits only IP learning if the source IP address of a packet does not belong to an ingress bridge domain subnet, whereas this feature limits learning of both the MAC address and IP address when IP learning is triggered but yet prevented because the source IP address does not belong to an ingress bridge domain subnet. Please note that, regardless of the source IP range, the Cisco ACI leaf still learns MAC address if the packet is a bridging traffic because the leaf does not check the IP header or whether it has the IP header for bridging traffic. Thus, enforce subnet check enables slightly stronger checks than limit IP learning to subnet. This check will be enabled on all bridge domains, and you cannot turn the checks on and off per bridge domain. Therefore, limit IP learning to subnet is not required when this feature is enabled.

*   **On the egress leaf (remote endpoint learning):** This option enforces VRF-level subnet checks for remote endpoint learning. When this feature is enabled, the Cisco ACI leaf will learn an IP address as a remote endpoint only when the source IP address of the incoming packet belongs to any bridge domain subnet in the same VRF instance on the egress leaf. This behavior prevents IP spoofing scenarios, in which an endpoint sends a packet with an unexpected source IP address that does not belong to any of the bridge domains on the VRF instance, such as an IP address that exists behind the Layer 3 Outside connection.

### Enforce Subnet Check Use Case

The following figure shows a use case example that provides details about the behavior of the enforce subnet check option.

![](/images/aciadvanced/aciadvanced_1_7_5.png)

When enforce subnet check is enabled leaf 1 does not learn either MAC S2 or IP 172.16.0.1 as a local endpoint, because 172.16.0.1 does not belong to ingress BD1. Leaf 2 does not learn IP 172.16.0.1 as a remote endpoint, because 172.16.0.1 does not belong to any of the bridge domain subnets on leaf 2 in the same VRF instance. If 172.16.0.1 is learned as a local endpoint on leaf 1 and the remote endpoint on leaf 2 before this feature is enabled, those two endpoints are cleared after this feature is enabled.

### IP Data-Plane Learning (VRF-Level)

The IP Data-plane Learning option is located at Tenant > Networking > VRFs. This option is enabled by default. This option enables and disables endpoint data plane IP learning on the VRF.

![](/images/aciadvanced/aciadvanced_1_7_6.png)

When the IP Data-plane learning option under VRF is disabled, endpoint learning behavior on an ACI leaf changes as follows:

*   Local MACs and remote MACs are learned via the data plane (no change with this option).
*   Local IPs are not learned via the data plane.
*   Local IPs are learned from ARP/GARP/Neighbor Discovery (ND) via the control plane.
*   Remote IPs are not learned from unicast packets via the data plane.
*   Remote IPs are learned from multicast packets via the data plane.

When the IP Data-plane Learning option is disabled, existing remote IP endpoints are flushed immediately while bounce entries are retained and aged out normally. Existing local IP endpoints are not flushed either, but they will age out eventually unless control plane packets such as ARP keep them alive.

The IP Data-plane Learning option, under the VRF, must be disabled if you have a possibility that the ACI fabric receives traffics with the same-source IP address from different locations, which causes endpoint IP and MAC binding updates that occur due to data plane traffic. For example, mainframe virtual IP address (VIPA) connectivity and dynamic load-balancing mode for NIC Teaming on Microsoft Windows behave in this manner.

Another use case is when multiple devices share the same IP such as virtual IP (VIP) and ARP/GARP/ND is used to claim the ownership of the VIP among the devices. In that situation, those external devices may source data traffic from the same VIP at the same time, for example, when a failover is taking place. It could result in the ACI fabric learning the VIP from multiple places via the data plane. This issue can be avoided by disabling IP Data-plane Learning.

The example below shows active/standby servers that share the same active IP address that is primarily owned by the active server. When server 1 is active and server 2 is standby, server 1 takes care of 192.168.2.100 that is learned on leaf 2 E1/1.

![](/images/aciadvanced/aciadvanced_1_7_7.png)

When server 2 takes over the active role, server 2 sends GARP and 192.168.2.100 is now learned on leaf 2 E1/2.

![](/images/aciadvanced/aciadvanced_1_7_8.png)

A problem occurs if server 1 sends RESET from the VIP 192.168.2.100 to terminate existing TCP sessions that server 1 has been taking care of, resulting in data plane IP learning for 192.168.2.100 on leaf 2 E1/1.

![](/images/aciadvanced/aciadvanced_1_7_9.png)

Unless server 2 sends GARP for the VIP again, traffic toward the VIP 192.168.2.100 will be forwarded to server 1 that is currently standby, and that drops packet.

![](/images/aciadvanced/aciadvanced_1_7_10.png)

Now, look at the example that explains how this issue can be avoided by disabling IP Data-plane Learning.

![](/images/aciadvanced/aciadvanced_1_7_11.png)

When server 2 takes over the active role, server 2 sends GARP. Local IP addresses are still learned by APR/GARP/ND and so 192.168.2.100 is now learned on leaf 2 E1/2.

![](/images/aciadvanced/aciadvanced_1_7_12.png)

If IP Data-plane Learning is disabled, endpoint learning information is not updated through RST from server 1 because local IP addresses are not learned from data plane.

![](/images/aciadvanced/aciadvanced_1_7_13.png)

Now, traffic toward the VIP 192.168.2.100 will be correctly forwarded to server 2 that is currently active.

![](/images/aciadvanced/aciadvanced_1_7_14.png)

**Note:** In this EPG-to-EPG routed traffic example, the contract policy will be applied on the egress leaf as the remote endpoint IP is not learned. If it is EPG-to-EPG bridged traffic, the contract policy can be applied on the ingress leaf since remote MAC is still learned.

##  1.9)    Endpoint Loop Protection

The endpoint loop protection is a feature that is configured at the global level. The feature is turned on for all bridge domains. The endpoint loop protection feature can be enabled by choosing **System > System Settings > Endpoint Controls > Ep Loop Protection.**

![](/images/aciadvanced/aciadvanced_1_9_1.png)

Endpoint loop protection acts if the Cisco ACI fabric detects an endpoint flapping between two interfaces more than a specified number of times during a given time interval.

You can specify:

*   **Loop Detection Interval:** Specifies the time to detect a loop. The default setting is 60 seconds.
*   **Loop Detection Multiplication Factor:** Sets the number of times a single endpoint moves between ports within the loop detection interval. The default is 4.
*   **Action:** When too many moves are detected within a loop detection interval you can choose whether Cisco ACI should disable one of the ports to which the endpoint is connected (Port Disable option), or disable learning within the bridge domain (BD Learn Disable option).

Cisco ACI leaf switches have a deduplication feature that lets the ACI count the move of individual endpoints and detect a loop regardless of whether a single endpoint is moving too often (which most likely is not a loop but maybe an incorrect NIC-teaming configuration) or multiple endpoints are moving too often (as happens with loops).

##  1.10)    Rogue Endpoint Control

Rogue endpoint control is a feature that can help in case there are MAC or IP addresses that are moving too often between ports. With rogue endpoint control, only the misbehaving endpoint (MAC/IP) is quarantined, which means that Cisco ACI keeps its TEP and port that is fixed for a certain amount of time when learning is disabled for this endpoint. The feature also raises a fault to allow easy identification of the problematic endpoint. The feature works within a site.

![](/images/aciadvanced/aciadvanced_1_10_1.png)

You can configure the following endpoint controls properties:

*   **Rogue endpoint detection interval:** Specifies the time to detect rogue endpoints. The default is 60 seconds.
*   **Rogue endpoint detection multiplicator factor:** If the endpoint moves more times than this number, within the endpoint detection interval, the endpoint is declared rogue. The default is 4.
*   **Hold interval:** Defines how long, after the endpoint is declared rogue, the endpoint is kept static, so learning is prevented and the traffic to and from the rogue endpoint is dropped. After this interval, the endpoint is deleted. The default is 1800.

Rogue endpoint control does not stop a Layer 2 loop, but it provides mitigation of the impact of a loop on the COOP control plane by quarantining the endpoints. Rogue endpoint control also helps if there is incorrect configurations on servers (such as incorrect NIC Teaming configuration), which may cause endpoint flapping. In such a case, Cisco ACI, instead of disabling the server ports, as endpoint loop detection may do, stops the learning for the endpoint that is moving too often, and it provides a fault with the IP of the endpoint that is moving too often so that the administrator can verify its configuration. It is recommended to enable rogue endpoint control (the feature is disabled by default).

For reference, access videos from various CiscoLive! Events:

*   Mastering ACI Forwarding Behavior: A day in the life of packet—BRKACI-3545 On-Demand https://www.ciscolive.com/global/on-demand-library.html?search=ACI%20forwarding#/session/1542224298129001r6yL

*   Your First 7 Days of ACI—BRKACI-1001 On-Demand https://www.ciscolive.com/global/on-demand-library.html?search=BRKACI-1001#/

For comprehensive information about Cisco ACI Packet Forwarding, please visit the Cisco ACI Fabric Endpoint Learning White Paper available at https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739989.html and Cisco ACI Infrastructure Design White Paper at https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-737909.html

### Summary of the Endpoint Learning Optimizations and General Recommendations

The following is the summary of the endpoint learning optimizations and general recommendations:

| Endpoint Learning Optimization | General Recommendation                                                                                                                                                                                   |
|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Limit IP Learning to Subnet    | Enabled by default.                                                                                                                                                                                      |
| Enforce Subnet Check           | Always recommended enabling to prevent any spoofed IP or any unintentional misconfigurations to work.                                                                                                    |
| IP data-plane learning on VRF  | Disable only for a corner case where the same IP address is expected to show up in two different places in the same VRF such as dynamic load-balancing mode for NIC Teaming on Microsoft Windows server. |


!



| Endpoint Learning Optimization | General Recommendation                                                                                                                                                                                                                                                                                                                                                                                                                          |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Endpoint Loop Protection       | Recommended to enable Rogue Endpoint instead.                                                                                                                                                                                                                                                                                                                                                                                                   |
| Rogue Endpoint                 | Generally recommended to enable. Be aware that this feature sticks the flapping endpoint to one of the flapping interfaces and Cisco ACI cannot tell which interface is the correct one. If you'd like to avoid the slight possibility of one interface black-holing the entire traffic, you could leave this option disabled which does not stop the flapping and traffic will be intermittently dropped until the cause of the flap is fixed. |

#   2)  Using Advanced Cisco ACI policy and Tenant Configuration

##  2.1)    Introduction

The Cisco Application-Centric Infrastructure (ACI) policy and tenant configurations are essential when making the most of Cisco ACI. There are many features that will help you design and optimize what best suits your use cases and data center. For example, you can plan how your deployment will communicate with and share CiscoWorks Common Services across external networks. You can also understand all different options for your security policies.

##  2.2)    Layer 3 Outside Transit Routing

The Cisco APIC software supports external Layer 3 connectivity with OSPF (NSSA) and Internal Border Gateway Protocol. The fabric advertises the tenant’s bridge domain subnets out to the external routers on the External Layer 3 Outside (L3Out) connections. The routes that are learned from the external routers are not advertised to other external routers. The fabric behaves like a stub network that can carry the traffic between the external Layer 3 domains. It also provides servers in a data center with IP reachability to the outside, which could be WAN, intranet to the company network, or others, which is the communication between the internal EPG (servers) and L3Out (the outside). Then, no matter how many L3Outs ACI may have, the communication was always between the internal EPG and L3Outs, and not between one L3Out connection to another L3Out connection.

From the APIC release 1.1, ACI introduced the capability to be a transit network by allowing communication between two L3Outs. This functionality is called transit routing. In transit routing, multiple L3Out connections within a tenant and VRF are supported and the APIC advertises the routes that are learned from one L3Out connection to another L3Out connection. The external Layer 3 domains peer with the fabric on the border leaf switches. The fabric is a transit Multiprotocol-Border Gateway Protocol (MP-BGP) domain between the peers. To succeed, there are two elements. One is to advertise external routes that are learned from one L3Out to another, typically referred to as “export.” Another is to apply contract between two L3Outs.

The two simple and recommended ways to export the external routes (advertise the external routes from one L3Out to another) are the following:

*   **Default-export** route profile with a prefix-list in the L3Out
*   **Export Route Control Subnet** scope with L3Out subnets in the L3Out EPG

The following figure explains the details on each option.

![](/images/aciadvanced/aciadvanced_2_2_1.png)

In this scenario, the router in the legacy network (on the right in the picture) needs to communicate to the 172.16.1.0/30 subnet behind the router in the partner network (on the left in the picture) through the Cisco ACI fabric. This scenario is the transit routing between L3Out Legacy and L3Out Partner.

The Cisco ACI fabric needs to advertise (export) 172.16.1.0/30 that is learned from L3Out Partner to L3Out Legacy. The export configuration is performed on L3Out Legacy that advertises and exports out the subnet. The following are the two export configuration examples:

Option 1—"default-export" Route Profile

![](/images/aciadvanced/aciadvanced_2_2_2.png)

In Cisco ACI, there are many ways to configure and use route profiles (route-map). For transit routing, the recommended configuration with a route profile (route-map) is to use "default-export" with Type "Match Routing Only" and prefix-lists. Here, routing protocol controls are solely with the route-map "default-export" and no L3Out subnets under the External EPG (L3Out EPG) should be configured with the scope "Export Route Control Subnet." The L3Out subnets, in this case, are used only to apply contracts with "External Subnets for the External EPG."

In this example, Cisco ACI is configured to advertise 172.16.100.100/32 and 172.16.1.0/30, that should be learned from other L3Outs, to L3Out BGP.

Please note that the “default-export” route profile takes effect on the L3Out without associating it to L3Out EPG or subnets unlike other route profiles with custom names.

Another option is to use the scope "Export Route Control Subnet" with L3Out subnets in the L3Out EPG. This configuration is the original option for the Transit Routing (to advertise [export] external routes to the L3Out from another L3Out prior to the route profile). This configuration is very simple and designed to reflect the intent directly without going through the traditional route-map configuration.

Option 2: "Export Route Control Subnet" Scope with L3Out Subnets

![](/images/aciadvanced/aciadvanced_2_2_3.png)

**Note:** When additional parameters such as BGP community need to be added to the advertised route, a route profile needs to be used as well. In this scenario, the recommended configuration is to use a route profile with a custom name (not "default-export") with Type "Match Prefix AND Routing Policy.” Then apply it to the L3Out subnet with "Export Route Control Subnet" scope or to the L3Out EPG itself.

**Note:** When using the "default-export" route profile (option 1), the additional parameters to be set on the advertised route can be configured in the same "default-export" route profile. Therefore, option 1 is recommended to keep the configuration simple in case you may need to set some additional parameters to the advertised routes in the future.

With either option 1 or option 2 export configuration, the external route 172.16.1.0/30 learned from L3Out Partner is advertised to L3Out Legacy. For the mutual communication, the export configuration needs to be done in L3Out Partner for the subnet from L3Out Legacy (x.x.x.x/y).

Once the external routes are exchanged correctly via export configuration for both L3Outs, the contract needs to be configured between L3Out EPGs. Like a normal L3Out to internal EPG communication, the L3Out EPG needs to define the external subnet that belongs to itself via the scope "External Subnets for the External EPG." With the option 2 configuration with "Export Route Control Subnet" scope, the full configuration should look like this:

In a large network, it may not be scalable to define all subnets individually with the scope "Export Route Control Subnet" or with the prefix-list in the “default-export” route profile. In this case, you can define an aggregate option that will mark all learned subnets for export.

![](/images/aciadvanced/aciadvanced_2_2_4.png)

As shown in the figure, there are several subnets that are received from the partner router that should be advertised to the legacy router. In this scenario, you can define the 0.0.0.0/0 prefix-list with Aggregate option in L3Out Legacy in the case of the configuration option 1 ("default-export" route profile).

![](/images/aciadvanced/aciadvanced_2_2_5.png)

This option instructs the fabric to try to advertise any routes in the routing table via L3Out Legacy, which does not mean that ACI summarizes the routes into one 0.0.0.0/0 route and advertise it. You could also configure a prefix-list 172.16.0.0/16 with Aggregate option and 10.10.10.0/24 without the Aggregate option to avoid unnecessary advertisement.

For the configuration option 2, you can define the 0.0.0.0/0 subnet with “Export Route Control Subnet” scope and “Aggregate Export” in L3Out Legacy.

![](/images/aciadvanced/aciadvanced_2_2_6.png)

This option accomplishes the exact same thing as “default-export” route profile with the aggregated 0.0.0.0/0 prefix-list. The restriction in this option 2 is that you can only use “Aggregate Export” with the 0.0.0.0/0 subnet. You cannot do 172.16.0.0/16 with “Aggregate Export” like the option 1 with the “default-export” route profile.

In some scenarios, you may need to export static routes between Layer 3 outside, as shown in the figure.

The following figure shows the process exporting static routes:

![](/images/aciadvanced/aciadvanced_2_2_7.png)

There is a static route towards 192.18.0.0/18 configured on L3Out Partner to point the route towards Route Partner. If you need to advertise the static route on L3Out Legacy, the same export configuration needs to be done on L3Out Legacy. From that perspective, it does not matter whether the routes to export are originally static routes or routes from a routing protocol.

**Note:** As a part of Transit Routing, the route control in import direction was also introduced to limit the external routes ACI can learn from each L3Out. The recommended configurations are the same as the export direction.

*   "default-import" route profile with a prefix-list in the L3Out
*   "Import Route Control Subnet" scope with L3Out subnets in the L3Out EPG

By default, these import direction controls are disabled and ACI learns all external routes that the routing protocol advertises. To use import direction controls, you need to enable “Import Route Control Enforcement” under the L3Out itself.

Some limitations exist on the supported transit routing combinations through the fabric. The latest matrix that shows supported transit routing combinations is available at the following link: https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/l3-configuration/cisco-apic-layer-3-networking-configuration-guide-52x/m_transit_routing_v2.html#reference_A60D8979B21C4B3DA6A0C454FBE9029E

The latest matrix showing supported transit routing combinations.

![](/images/aciadvanced/aciadvanced_2_2_8.png)

##  2.4)    Using Tenant Common for Shared Services

Shared services designs are always challenging in terms of keeping security while allowing flexibility for those services that should be shared across different organizations, departments, and for Cisco ACI tenants.

In here, “shared service” implies a commonly used service such as Domain Name System (DNS) that is shared across multiple tenants (organizations). In Cisco ACI, shared service can be typically provided by using either one of the following options depending on the network design:

*   The common tenant where the shared service and its consumers in other tenants are in the same common VRF.

*   The VRF route leaking where the shared service and its consumers are in the different VRFs.

Here you will learn what a predefined tenant common is, where, when, and how it can be used to implement shared services.

The common tenant is a preconfigured tenant that shares objects across all other tenants on Cisco ACI. It provides a method to share a particular endpoint in the common tenant that provides a CiscoWorks Common Service such as Cisco DNS to other endpoints in other tenants, or it provides a common Cisco ACI policy such as a contract filter that other tenants can reuse.

First, you will review how other tenants can use the policies in the common tenant. As shown in the figure, the contract that is configured in the common tenant can be selected from the drop-down menu as if it’s part of the user tenant, allowing you to define a common security rule such as ICMP in the common tenant and reused by any EPGs in the fabric. However, an unintended contract association can happen, since all the EPGs will be providing and consuming the same contract everywhere. Hence, the recommendation for this example particularly is to use a filter that is defined in the common tenant and create a contract in each tenant by reusing the filter, by which you can avoid providing and consuming the same contract with two intended EPGs.

![](/images/aciadvanced/aciadvanced_2_4_1.png)

Just like a contract or a filter, other tenants can use a VRF, a BD, and even an L3Out in the common tenant, and are typically used to provide a shared service such as DNS from the common tenant.

Before discussing how to use the common tenant to share services on Cisco ACI, it is recommended to review some VRF design considerations.

![](/images/aciadvanced/aciadvanced_2_4_2.png)

In Cisco ACI, each tenant can have its own VRFs (Virtual Routing and Forwarding) and BDs (Bridge Domain). VRFs are to provide Layer 3 forwarding domain (IP space segmentation). BDs are to provide Layer 2 forwarding/flooding domain (MAC space segmentation). All BDs need to belong to one VRF just like a normal switch, which means each VRF (IP space) or BD (MAC space) is closed within each tenant resulting in a potential bottleneck for configuration designs if you want to separate applications by creating tenants per organization or department even though all applications belong to the same VRF.

There are mainly two different ways to use the common tenant for shared services.

*   Share the common VRF with multiple tenants
*   Share the common BD with multiple tenants

![](/images/aciadvanced/aciadvanced_2_4_3.png)

In case the Layer 3 reachability is required for the shared service such as DNS in the common tenant (as in the shared service and EPGs in each tenant are in different subnets), creating a common VRF and sharing it from the common tenant is enough so that Layer 2 flooding domains can be kept local to each tenant. In this case, the shared service itself can belong to either a L3Out or a BD/EPG in the common tenant.

In case the Layer 2 reachability is required for the shared service such as DNS in the common tenant (as in the shared service and EPGs in each tenant are in the same subnet), creating a common VRF and a common BD is required to share the common BD with other tenants. This scenario may be rare because the requirement to separate the tenants for applications that belong to the same subnet is not common.

One of the common requirements is that the shared service is reachable via the L3Out in the common tenant. In that case, the configuration diagram looks like this:

![](/images/aciadvanced/aciadvanced_2_4_4.png)

The differences in the configurations from a normal single tenant scenario are:

1.  BDs in each tenant points to the VRF from the common tenant.
1.  Use contracts in the common tenant.

But each user tenant can select both the VRF and contracts from the common tenant without any additional configuration.

![](/images/aciadvanced/aciadvanced_2_4_5.png)

The configuration itself is pretty straight forward and the important part is to understand where to deploy the VRF/BD. And also, always reconsider whether the multiple tenants’ separation is actually needed to keep the configuration simple.


##  2.5)    Using VRF route leaking for Shared Services

When using the common tenant, the restriction was to keep all the applications at least in the same VRF. Using a single VRF to keep the configuration simple is one of the first things network engineers need to consider about design even in traditional networks. However, there are scenarios where although most of the applications are closed within its own VRF, a few applications have to be able to communicate across VRFs. The first thing to consider is whether the VRFs can be consolidated into one. But it may not be doable when there are applications using overlapping IP subnets in each VRF. In such scenarios, the VRF route leaking needs to be configured. Such requirements are not specific to ACI but sometimes seen in traditional routers as well.

You will learn what kind of configurations are required for the VRF Route Leaking in ACI with the following example where the EPG 1 in VRF 1 needs to communicate with EPG 2 in VRF 2.

![](/images/aciadvanced/aciadvanced_2_5_1.png)

For EPG 1 to be able to communicate with EPG 2, there are two things to consider.

1.  Leak the subnets for EPG 1 and EPG 2 into each VRF for forwarding.
1.  Allow the traffic with a contract for security

In Cisco ACI, both are accomplished via a contract. Configuring an appropriate contract between two EPGs in different VRFs implies subnets for these EPGs need to be leaked at the same time.

The appropriate contract here depends on the scope of contracts. By default, the scope of all contracts is set to VRF. In case, the VRF route leaking is required, the scope needs to be set to either global, tenant, or application profile. In case the two VRFs are in the same tenant, the scope tenant can be used. In case the two EPGs are in the same application profile in the same tenant, the scope application profile can be used. In the example of this section, the two VRFs and EPGs are in different tenants. Hence, it needs to be scope global.

![](/images/aciadvanced/aciadvanced_2_5_2.png)

Also, as you may have noticed from the previous section about the common tenant, the contract in a user tenant is not visible from other user tenants. Hence, you need to explicitly export the contract from the provider tenant to the consumer tenant. When exporting a contract to another tenant, the provider tenant must be the one that exports the contract. Or you could use a contract with a global scope from the common tenant to avoid exporting the contract. In this section, you will see the example of exporting the contract.

On top of the contract configuration across VRFs, you need do two more following configurations so that APIC can tell which subnets for these EPGs need to be leaked:

*   Enable “Shared between VRFs” on the BD/EPG subnet (pervasive gateway).
*   Move the BD subnet (pervasive gateway) configuration under the EPG in the provider side.

With these settings, the final configuration will look like this:

![](/images/aciadvanced/aciadvanced_2_5_3.png)

The reason why the subnet needs to be configured under the EPG in the provider side is to avoid unnecessary route leaking. You can see this configuration in the scenario where BD 1 contains two subnets and each leak to different VRFs as EPG X and EPG 1 as the provider respectively. If the subnets are configured under BD 1, both subnets are leaked to both VRFs since “Shared between VRFs” are enabled on both.

![](/images/aciadvanced/aciadvanced_2_5_4.png)

On the consumer side, there is no strict requirement. You can configure the subnet with “Shared between VRFs” under either EPG or BD.

Also please note that even if the subnet is configured under the EPG, the subnet will be deployed as the pervasive gateway for the entire BD. There are no functional differences between EPG and BD subnet except for the leaking part. If there are other EPGs in BD 1 that need to use 10.10.10.1 as its default gateway, they can use it while the subnet is configured only under EPG 1.

**Note:** If there is only one subnet in each BD, and you want to keep the subnet under the BDs in both VRFs for visibility reasons, you could configure an extra contract in the opposite direction, VRF 2 as the provider and VRF 1 as the consumer. Then, both VRFs are consumer for at least one shared contract and the route is leaked even when both VRFs have them under the BD.

The following steps show an example overview of the configuration for the VRF route leaking between EPG 1 in tenant 1 and EPG 2 in tenant 2:

1.  Configure the shared subnet for EPG 1 under the EPG, as opposed to under the bridge domain. This configuration will be the provider EPG. In here, please note that “Private to VRF” scope is not related to the VRF route leaking. “Private to VRF” and “Advertised Externally” scopes are mutually exclusive and are to decide whether the subnet should be advertised via L3Outs.

    ![](/images/aciadvanced/aciadvanced_2_5_5.png)

1.  Configure the shared subnet for EPG 2 under either the EPG or bridge domain. This configuration will be the consumer EPG.

    ![](/images/aciadvanced/aciadvanced_2_5_6.png)

1.  Create a global contract in tenant 1 for the traffic you want to allow. The subject configuration is omitted in this example.

    ![](/images/aciadvanced/aciadvanced_2_5_7.png)

1.  Add that contract as provided to EPG 1.

    ![](/images/aciadvanced/aciadvanced_2_5_8.png)

1.  Export that contract from Tenant 1 to Tenant 2.

    ![](/images/aciadvanced/aciadvanced_2_5_9.png)
    ![](/images/aciadvanced/aciadvanced_2_5_10.png)

1.  Add the imported contract in Tenant 2 as a consumed contract interface to EPG 2. (The imported contract is presented as consumed contract interface in the imported tenant.)

    ![](/images/aciadvanced/aciadvanced_2_5_11.png)

1.  Application profile for Tenant 1. Please note that EPG 1 is now providing the global contract.

    ![](/images/aciadvanced/aciadvanced_2_5_12.png)

1.  Application profile for Tenant 2. Please note EPG 2 is now consuming the global contract.

    ![](/images/aciadvanced/aciadvanced_2_5_13.png)

##  2.7)    Using L3Out VRF Route Leaking for Shared Services

Just like the VRF route leaking between two internal EPGs, the VRF route leaking can be performed with L3Out and EPG, so, L3Out EPG (external EPG) and internal EPG. The L3Out that is configured with the VRF route leaking is called Shared L3Out and is used when a service that needs to be shared across VRFs resides in the external routes learned from the L3Out.

In the shared L3Out, there are two scopes under the L3Out subnet in the L3Out EPG.

*   Shared Route Control Subnet
*   Shared Security Import Subnet

![](/images/aciadvanced/aciadvanced_2_7_1.png)

The basic configuration concept of the shared L3Out is the same as leaking between EPGs. The following is the summary steps for the shared L3Out.

1.  Configure a contract across VRFs with the appropriate scope.

1.  Mark the subnet to leak with “Shared Route Control Subnet”

    1.  This subnet needs to be learned in the L3Out via the routing protocol or configured as a static route.

1.  Mark the subnet with “Shared Security Import Subnet” to let the other VRF know about the subnet to L3Out EPG mapping.

The new part is the last step 3 with “Shared Security Import Subnet.” Unlike an internal EPG, in L3Outs regardless of VRF route leaking, users must define L3Out subnets in each L3Out EPG with “External Subnets for the External EPG” scope to declare which external routes belong to which L3Out EPG. The other VRF needs to be informed of this mapping so that a contract can be applied correctly.

The reason why the scope “Shared Route Control Subnet” and “Shared Security Import Subnet” are separated is for the granularity to apply contracts. For example, the L3Out may be learning the route 10.0.0.0/8 from the external router. But you may want to apply different sets of contracts for 10.0.0.0/16 and 10.1.0.0/16. In such scenarios, you can configure “Shared Route Control Subnet” with 10.0.0.0/8 to leak the route into the routing table on the other VRF. And then configure 10.0.0.0/16 in one L3Out EPG with contract A and 10.1.0.0/16 with another contract. Both contracts can be a shared contract with an appropriate scope. Or only one of them can be a shared contract if you need to leak only one of them, let’s say 10.0.0.0/16. In that case, the routing table on the other VRF still has the entire 10.0.0.0/8 since the route that is learned on ACI is 10.0.0.0/8. But only the 10.0.0.0/16 subnet can communicate due to the contract.

The following figure is a sample configuration diagram for the simplest scenario.

![](/images/aciadvanced/aciadvanced_2_7_2.png)

One additional configuration in here is “Advertised Externally” scope in the BD subnet. The BD subnet needs to be advertised via the L3Out after it is leaked to VRF 1 so that the service behind L3Out can also reach the BD subnet.

With the shared L3Out or the normal shared service (VRF route leaking between EPGs), you can provide a CiscoWorks Common Service from any tenants/VRFs to others. However, please keep in mind that any routes that are leaked should not overlap in the other VRFs and should be considered in any normal routers that perform VRF route leaking.


##  2.9)    Detailed Contract Architecture with pcTag

In Cisco ACI, all EPGs have their own ID called policy control Tag (pcTag). The pcTag is sometimes referred to as class ID or sclass/dclass for source/destination class ID (source/destination pcTag). The pcTag is assigned to an EPG when it is created by the APIC and used when a contract rule is applied for a packet on a leaf switch. The security rules that use pcTag and that are created from contracts on leaf switches are called zoning rules. Each zoning rule has the following parameters:

*   Src EPG (src pcTag)
*   DST EPG (DST pcTag)
*   Filter ID (traffic type such as TCP DST port 3306)
*   Scope (VRF VNID for the src and DST EPGs)
*   Action (permit or deny)

The first three parameters (src/dst pcTag and filter) are the core of the rule that defines what kind of traffic from which EPG to which EPG should use this rule.

The fourth parameter Scope is to define in which VRF this rule should be applied. It is important because pcTag is by default unique only within a VRF. The following is the pcTag types and its range:

*   **System reserved pcTag (1-15):** This pcTag is used for system internal rules. Some examples of such reserved pcTags are:
    *   15: L3Out Subnet 0.0.0.0/0 for "External Subnets for the External EPG."
    *   14: Shared Service (VRF route leaking), is to ensure that a contract is applied on the consumer VRF.
    *   1: To allow traffic such as the one destined to ACI switch CPU implicitly.

*   **Global pcTag (16-16385):** This pcTag is unique across VRFs and used for shared service (VRF route leaking).

*   **Local pcTag (16386-65535):** This pcTag is unique only within a VRF and is the default pcTag for internal EPGs and L3Out EPGs.

By default, EPGs in different VRFs may have overlapping local pcTag, which is not a problem since the traffic should be always within the same VRF. However, when the VRF route leaking is configured, the traffic goes across VRFs and ACI needs to ensure the pcTag of leaked EPGs do not overlap. EPGs with shared service (VRF route leaking) configuration are assigned a new global pcTag as a replacement of the original local pcTag. Precisely speaking, only the provider EPGs with shared service configuration are assigned a global pcTag.

The last parameter Action is to define whether the traffic should be allowed or dropped.

The actual zoning rules on a leaf switch can be checked using the command **show zoning-rule** on the leaf CLI.

![](/images/aciadvanced/aciadvanced_2_9_1.png)

This example shows the names of the contracts with rules that are programmed with filters. Examples are ones with filter IDs 17 to 20 represented by pcTag 49154 and 32772, within FileServices_Ct contract. These rules are defined in the scope 2523136, which is the VRF VNID. The contents of each filter can be checked with the **show zoning-filter** command on the leaf CLI.

![](/images/aciadvanced/aciadvanced_2_9_2.png)

To check the pcTag and VRF VNID (scope), you can also use the APIC user interface. This figure shows the pcTag for an EPG in the APIC user interface:

![](/images/aciadvanced/aciadvanced_2_9_3.png)

In case you need to check the pcTag for L3Out EPGs, you can check it from here:

![](/images/aciadvanced/aciadvanced_2_9_4.png)

You can check all pcTag occurrences for various objects within a tenant by navigating to Tenants > Tenant name > Operational > Resource IDs.

![](/images/aciadvanced/aciadvanced_2_9_5.png)

The VRF VNID (scope for the zoning rules) can be checked in the APIC user interface as well:

![](/images/aciadvanced/aciadvanced_2_9_6.png)

ou can also check the policy objects to obtain the values explained above.

You can use the following command from the Cisco APIC to find the scope for a given VRF:

```shell
apic1# moquery -c fvCtx -f 'fv.Ctx.name=="Presales_VRF"'
```

This example shows the output of this command:

![](/images/aciadvanced/aciadvanced_2_9_7.png)

From the output above, you can double check that "dn" should be indeed the VRF in question and that the scope is 2523136.

You now need to find the pcTag for each EPG. You can use the APIC user interface to find the pcTag for each EPG, as indicated in these figures:

![](/images/aciadvanced/aciadvanced_2_9_8.png)

![](/images/aciadvanced/aciadvanced_2_9_9.png)

Also, you can use the following commands from the Cisco APIC to find the pcTag for each EPG:

*   moquery -c fvAEPg -f 'fv.AEPg.name=="App_EPG"'
*   moquery -c fvAEPg -f 'fv.AEPg.name=="DB_EPG"'

**Note:** The total objects shown count is one. If the count is two or more, then you need to make sure that you copy the pcTag that corresponds to the correct "dn."

These examples show the outputs of the referred commands:

![](/images/aciadvanced/aciadvanced_2_9_10.png)

### Policy TCAM Exhaustion

The physical ternary content-addressable memory (TCAM) in which policy is stored for enforcement is a critical component of switch hardware and important system resource in a Cisco ACI fabric. When an EPG is associated with a contract, zoning rules that are applied on a leaf switch can consume a lot of entries in TCAM, which can lead to TCAM exhaustion.

Options to optimize policy CAM usage and simplify the configuration:

*   Policy Control Enforcement in VRF set to Unenforced
*   Contract with vzAny
*   Contract Preferred Group

VRF provides an option “Policy Control Enforcement” to turn off the allow list security model that is enforced with EPG and contract. By default, it is enforced and no communication between EPGs is allowed without a contract rule. Once it’s unenforced, no contract rules will be applied, and any endpoints can talk to anyone as long as there is Layer 2 or Layer 3 reachability.

The use of vzAny and contract preferred group will be discussed later in the course.

##  2.10)    Detailed Contract Architecture with pcTag

The vzAny managed object provides a way of associating all EPGs in a VRF instance to one or more contracts (vzBrCP), instead of creating separate contract relations for each EPG.

In the Cisco ACI fabric, EPGs can only communicate with other EPGs according to contract rules. A relationship between an EPG and a contract specifies whether the EPG provides the communications that are defined by the contract rules, consumes them, or both. By dynamically applying contract rules to all EPGs in a VRF, vzAny automates the process of configuring EPG contract relationships. Whenever a new EPG is added to a VRF, vzAny contract rules automatically apply. The vzAny one-to-all EPG relationship is the most efficient way of applying contract rules to all EPGs in a VRF.

![](/images/aciadvanced/aciadvanced_2_10_1.png)

While these scenarios illustrate very simple configurations, large production systems can easily have thousands of contract relationships in a single VRF. In such cases, vzAny can simplify the configuration for contracts that are consumed or provided by all EPGs in the VRF. In the zoning-rule, vzAny is represented with pcTag 0, which matches with any pcTags in the same scope (VRF). As a result, vzAny can not only simplify the configuration but also save a lot of space in the policy TCAM on leaf switches by consolidating multiple rules between individual pcTags into one. In the example above, let’s say pcTag of EPG 1 – 4 are pcTag 16401 – 16404. Then, the zoning rules without vzAny look like this:

```bash
    Rule 1 - 16401 -> 16402 (filter FTP)
    Rule 2 - 16401 -> 16403 (filter FTP)
    Rule 3 - 16401 -> 16404 (filter FTP)
```

With vzAny, the rule is consolidated into one:

```bash
    Rule 1 - 16401 -> 0 (filter FTP)
```

The vzAny can be configured in the same way as normal EPGs. You need to consume or provide a contract from “EPG Collection for VRF” under the VRF.

![](/images/aciadvanced/aciadvanced_2_10_2.png)

### vzAny Use Cases

In the first vzAny use case, vzAny is the consumer and EPG-1 is the provider. The EPG-1 provides its resources to all EPGs within a VRF, with access privileges defined by the contract. In the second use case, the roles are reversed, and the EPG-2 is granted access to specific services in all EPGs. The third scenario is used to control access from any EPG to any other EPG to allow everybody to exchange a particular type of traffic with anyone in the same VRF.

![](/images/aciadvanced/aciadvanced_2_10_3.png)

When using vzAny, follow these guidelines and limitations:

*   The vzAny represents everybody in the same VRF, such as internal EPGs under application profiles, external EPGs for L2Outs, L3Outs, and out-of-band or in-band EPGs (External Management Network Instance Profile in the APIC user interface) in tenant mgmt and so on.

*   This “everybody” includes any external IP subnets that may not be defined in any external EPGs.

*   The vzAny represents everybody in the same VRF as a consumer and/or a provider.

*   vzAny is supported as a consumer of a shared service but is not supported as a provider of a shared service.

Be aware that using vzAny as a consumer for a shared service contract means that anybody in the consumer VRF can communicate with the provider application in the provider VRF.

*   If the scope of the contract that is provided or consumed by the vzAny is Application Profile, the leaf switches will not consolidate zoning rules with pcTag 0 but instead program multiple zoning rules with individual pcTag of each EPG, still helping to simplify the configuration but does not help to save the policy TCAM usage.

##  2.11)    Contract Preferred Group

As you have learned so far, in Cisco ACI, any EPGs need to have a contract to communicate with any other EPGs due to the allow list model. As the number of EPGs grow, the contract configuration between all EPGs becomes challenging to manage. With vzAny, certain configurations, such as all EPGs in the given VRF shares the same contract, can be simplified.

What about the configuration like this? It is working fine with the allow list model except for the communication between EPG 1, 2, 3 and 4. The requirement here is that EPG 1 – 4 should be allowed to talk to each other without any security rules, while the rest should follow the allow list model.

![](/images/aciadvanced/aciadvanced_2_11_1.png)

To simplify such a configuration requirement to partially unenforced contract policies in the given VRF, ACI introduced Contract Preferred Group in the APIC release 2.2(1).

![](/images/aciadvanced/aciadvanced_2_11_2.png)

With Preferred Group, ACI defines some EPGs as the “Included” members. All the other EPGs are grouped as “Excluded” members. In the example picture, EPG 1 – 4 are defined as the “Included” members. Within the “Included” members, there are no contracts that are required. They can talk to each other without any security enforcement. The EPGs in the “Excluded” members, on the other hand, still follow the allow list model and requires contracts to talk to any other EPGs within the “Excluded” members or with the EPGs in the “Included” members.

The Preferred Group simplifies the contract configuration tremendously when there are huge number of EPGs that need to communicate to each other freely.

One of the typical use cases for the preferred group is during the migration. You may want to migrate the resources to the ACI fabric only with a network construct without any security enforcement as a first phase, and gradually apply security with EPGs and contracts in the ACI fabric. In such a scenario, all the EPGs for migrated resources can be marked as "Included" first, and change them to "Excluded" once the security rule needs to be applied in the fabric.

### Contract Preferred Group Configuration

To configure Contract Preferred Group, follow these steps:

1.  Enable the Preferred Group under the VRF.
    ![](/images/aciadvanced/aciadvanced_2_11_3.png)

1.  Add EPGs in the “Included” member. By default, all EPGs are defined as the “Excluded” member.
    ![](/images/aciadvanced/aciadvanced_2_11_4.png)

### Zoning Rule with Preferred Group

Without Preferred Group, ACI has default implicit deny for any pcTag (0) to any pcTag (0). In the example below, the Rule ID 4099 is the implicit deny for all.

![](/images/aciadvanced/aciadvanced_2_11_5.png)

With Preferred Group enabled and some EPGs in the “Included” member, ACI switches the default implicit deny for any pcTag (0) to any pcTag (0) with the default implicit permit rule. In the example below, the Rule ID 4099 is the implicit permit for all. However, all the EPGs in the “Excluded” member still need to follow the allow list model. Hence, the zoning rule has additional implicit deny rules for any traffic with those excluded EPGs. In the example below, the Rule ID 4171, 4151, and so on, are the implicit deny for individual excluded EPGs.

![](/images/aciadvanced/aciadvanced_2_11_6.png)

Preferred Group guidelines and limitations:

*   When adding a L3Out EPG in the “Included” member, 0.0.0.0/0 with “External Subnets for the External EPG” scope is not supported. Use 0.0.0.0/1 and 128.0.0.0/1 instead.

For more information about other limitations in the Preferred Group, please visit the Cisco APIC Basic Configuration Guide, Release 5.2(x), available at: https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/basic-configuration/cisco-apic-basic-configuration-guide-52x/m_tenants.html#d18865e9323a1635

For practical details on setting up a Cisco ACI Fabric using advanced ACI policy and tenant configuration, please visit the Setting Up a Cisco ACI Fabric: Initial Deployment Cookbook, available at: https://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/white_papers/Cisco-ACI-Initial-Deployment-Cookbook.pdf.

For additional references, access videos from various CiscoLive! Events on advanced ACI policy and tenant configuration:

*   ACI Troubleshooting: Layer 3 outs – BRKACI-2642 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=BRKACI-2642#/session/1549995145135001xHaj.

*   ACI Under the Hood – How Your Configuration is Deployed – BRKACI-3101 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=BRKACI-3101#/session/1532112832326001tpx1.


#   3)  Implement Traditional Network in Cisco ACI

##  3.1)    Introduction

Traditional networks are built with data center switches, which are connected with high-speed links, using 802.1Q trunking, VLANs, and spanning tree to segment the workloads with routing and security implemented at distribution layer.

Implementing a traditional network in Cisco Application-Centric Infrastructure (ACI) requires connecting those networks to the Cisco ACI fabric and considering their specifics when configuring to achieve proper connectivity and omit issues that are related to loops and intermittent connectivity.

##  3.2)    Integrating Switched Networks with Cisco ACI

Classical switched data center networks use STP to provide a loop-free topology, relying on BPDUs that are exchanged between nodes of the switched topology. The two STP options that are used in contemporary networks are Rapid Spanning Tree Protocol (RSTP) and Multiple Spanning Tree (MST), each with their own specifics. Cisco ACI is high-speed fabric that does not rely on classical Layer 2 mechanisms to achieve loop-free topology between ACI leaf and spine switches. Therefore, when integrating or migrating from classical network into Cisco ACI fabric, specifics of classical networks and Cisco ACI must be observed to properly design and implement Cisco ACI configuration.

![](/images/aciadvanced/aciadvanced_3_2_1.png)

The leaf-spine and Layer 3-based nature of the Cisco ACI fabric defines that non-ACI nodes (servers, routers, firewalls, load balancers, switches, and so on) are connected at the leaf level only.

The existing switched networks where endpoints are connected can be implemented with the following topologies:

*   Network segments that are built with regular switches using STP and 802.1Q trunks where some of the switches in the topology need to be connected to a Cisco ACI fabric.

*   Blade server systems that incorporate Ethernet switches that need to be connected to a Cisco ACI fabric and can have their specifics (failover or load sharing mechanisms, link tracking for servers).

*   Clustered switches that connect to Cisco ACI fabric via multi-chassis port channels.

On the other hand, a Cisco ACI fabric uses a set of constructs and policies to implement desired connectivity—access policies with switch and interface profiles, VLAN pools and domains, VRFs, BDs, and EPGs need to be designed and configured to match requirements of the previously mentioned switched networks.

### Integrating Switched Networks Specifics

The classic network segments may implement the following features and specifics:
*   Multiple endpoints are connected via a single leaf port.
*   Endpoints that are not directly connected to the Cisco ACI fabric.
*   Port channels and virtual port channel (vPCs) aggregating links and switches are used to omit the limitations of STP.
*   Interaction with STP domains and potential Layer 2 loops due to misconfiguration or misconnectivity in the switched topology.

![](/images/aciadvanced/aciadvanced_3_2_2.png)

### Nondirectly connected Endpoints

Endpoints that are not directly connected to the Cisco ACI fabric are still learned in the same way as other endpoints. But it traverses through the external Layer 2 network first and not all the traffic from them may hit the Cisco ACI fabric, which may slow down how quickly the ACI fabric can react to the endpoint information change.

Cisco ACI can optimize traffic by preventing flooding for ARP requests and/or Layer 2 Unknown Unicast packets. This optimization is called Spine-Proxy, which allows the packet to be sent only to the interface connected to the destination such as the target IP of the ARP request. However, for endpoints that are not directly connected to the Cisco ACI leaf switches, this optimization may need to be turned off so that the Cisco ACI fabric floods those packets like a classical Ethernet network does.

The issues with Spine-Proxy and external Layer 2 network may arise upon switched topology changes, for example, a link from one switch that is connected to the Cisco ACI fabric fails. The switched topology converges around that using links to another Cisco ACI leaf switch. At the same time, the endpoints learned through the link that failed are cleared from both the endpoint table on the leaf and the COOP database on spine switches. If other endpoints that are in the same subnet as those cleared endpoints try to send traffic to them through the Cisco ACI fabric, it may fail if the Cisco ACI fabric has not yet re-learned the cleared endpoints on the new converged interface. The packets may be dropped because the other endpoints may have the ARP cache for the cleared endpoint and send unicast bridged traffic to them without broadcasting ARP requests first. This traffic is Layer 2 Unknown Unicast traffic for the Cisco ACI fabric, and it is dropped if the cleared endpoints are not learned yet on the Cisco ACI fabric and Spine-Proxy is used for Layer 2 Unknown Unicast. If flood is used, the Cisco ACI fabric can flood the traffic through leaf, spine, and external Layer 2 network without any problems.

Therefore, the BD Cisco ACI configuration for such segments is recommended to be adjusted with the following:

*   Layer 2 unknown unicast flooding enabled.
*   ARP flooding enabled.

### Port Channels and vPCs

To aggregate links in a regular port channel, port channel Cisco ACI policy that matches the switched network side (for example, using LACP active or passive mode, ability to suspend individual ports) must be configured and applied to the selected interfaces.

A vPC on the Cisco ACI is implemented between two leaf switches designated as vPC peer switches—one being primary and one being secondary joined into a vPC domain. There is a couple of specifics with a Cisco ACI implementation when compared to a non-ACI implementation. There are no direct dedicated peer and peer-keepalive links that are used between the leaf switches that are part of the vPC domain. Instead the Cisco ACI fabric is used for the purpose and vPC pair gets additional TEP virtual IP address. The vPC configuration is implemented in the form of policies. The vPC domain and vPC protection group policies create the vPC domain, vPC interface policy group is used to apply configuration at the interface level.

### Interaction with STP Domains

Although the Cisco ACI fabric does not participate in STP domain with other external Layer 2 switches, it still forwards the STP BDPU from one external switch to another through so that the external switches can maintain their loop free topology even through the overlay network that is provided by the Cisco ACI fabric.

Since the flooding domain in ACI (BD) can contain multiple VLANs, the specifics of how and where BPDUs are forwarded through the Cisco ACI fabric are one of the key topics to learn.

### Cisco ACI Leaf VLAN and VXLAN Usage

Endpoints connected to Cisco ACI leaf switches continue to use and consume VLANs the same way they used them—traffic is either sent via native, that is, untagged or 802.1Q tagged VLANs. Cisco ACI leaf switches then perform mapping of the endpoint VLANs into fabric VXLANs for the traffic to be sent across the fabric.

The internal mapping process uses several VLAN and VXLAN identifiers that are assigned to different Cisco ACI constructs and used to forward different types of traffic (for example, STP BPDU and BUM traffic). Understanding where and why these identifiers are used is important for proper integration of switched networks.

![](/images/aciadvanced/aciadvanced_3_2_3.png)

Access encapsulation VLAN (ACC_ENCAP) is the VLAN that is used for the traffic on the wire between endpoint (or switch, router, firewall, port) and leaf port. In the Cisco ACI configuration, this VLAN is configured as part of a selected VLAN pool and enabled for the selected EPG for the port where the endpoint is connected.

BD_VLAN is a leaf locally significant VLAN assigned to each configured BD. BD_VLAN is translated into VXLAN ID that is the same on every leaf switch for the same BD. On top of bridging traffic in the BD, most of the packets of the traffic that may need to be flooded in a Layer 2 domain (for example, broadcast, multicast, and unknown unicast) are sent to the fabric using multicast through the BD VXLAN.

FD_VLAN is a leaf locally significant VLAN assigned to the individual configured access encap VLAN in an EPG. A BD will typically have multiple access encap VLANs each with its own FD_VLAN ID, which are children to BD_VLAN. FD_VLAN is assigned a VXLAN ID that must be the same across all leaf switches where that particular access encap VLAN for the EPG should be present.

For some flood traffic, the FD_VLAN (that is, its VXLAN ID) is used to forward the traffic instead of VLXLAN ID of the BD_VLAN.

The BD_VLAN and FD_VLAN are the type of PI-VLAN (Platform-Independent VLAN) explained in the section 1. The reason why there is a PI-VLAN between Access Encap VLAN and VXLAN is because the older ACI leaf and spine hardware had Broadcom ASIC that does not support VXLAN and Cisco proprietary ASIC. To communicate between those two ASICs, the middle layer (PI-VLAN) was required.

The presented Cisco ACI fabric is configured with two EPGs each encompassing its own access encapsulation VLAN:

*   EPG1 that is used for endpoints belonging to access encap VLAN 2110
*   EPG2 that is used for endpoints belonging to access encap VLAN 2111

![](/images/aciadvanced/aciadvanced_3_2_4.png)

BD is assigned the locally significant BD_VLAN ID of 33 and fabric encapsulation VXLAN ID 16514969. Since BD_VLAN is not used at any interface, the access type is unknown (that is, the BD_VLAN is not used as access encapsulation VLAN).

The access encapsulation VLANs are mapped to local FD_VLANs and fabric-wide VXLANs:

*   VLAN 2110 is mapped to FD_VLAN 41 and VXLAN ID 9201
*   VLAN 2111 is mapped to FD_VLAN 42 and VXLAN ID 9202.

Both belong to the same BD since they are part of the same Layer 2 domain.

The formula to calculate VXLAN ID for each access encapsulation VLAN is VXLAN ID = base VXLAN ID + (ACC_ENCAP—1st ACC_ENCAP from the VLAN pool).

*   The base VXLAN ID is the offset for VXLAN IDs (that is, the first VXLAN ID assigned is base+1)
*   The configured VLAN pool defines the first ACC_ENCAP.

Each access encap VLAN is translated to the same fabric encap VXLAN ID on any leaf switches regardless of which FD_VLAN ID it is internally mapped to.

The EPG1 that is configured with access VLAN 2110 on multiple leaf switches uses the following identifiers:

*   Different locally significant BD_VLAN/FD_VLAN pairs at different leaf switches (that is, 33/41 on the first leaf and 61/28 on the second leaf)
*   Same fabric encapsulation VXLAN ID 9201 for FD_VLAN
*   Same fabric encapsulation VXLAN ID 16514969 for BD_VLAN

![](/images/aciadvanced/aciadvanced_3_2_5.png)

For the traffic, exchanged between the source and destination endpoints from the same EPG with the same access encap VLAN, the following applies:

*   Packets entering Leaf 1 are tagged with 2110 access encapsulation VLAN.
*   Internal at Leaf 1, the packets are then bound to FD_VLAN 41.
*   The lookup for the destination endpoint MAC address resolves in the endpoint table with the BD VXLAN.
*   When the packets need to be forwarded to another leaf, they are tagged with BD VXLAN ID 16514969.
*   At Leaf 2, the destination lookup resolves to endpoint in FD_VLAN 28.
*   Finally, when packets need to be sent out from the downlink port on the leaf, they are tagged with access encapsulation 2110 VLAN ID.

**Note:** You can observe the VLANs in the Cisco ACI fabric by using the command show system internal epm vlan all, for example:

![](/images/aciadvanced/aciadvanced_3_2_6.png)

You could also use the **show system internal eltm info vlan brief** command.

### BPDU Handling in Cisco ACI

The Cisco ACI fabric does not run and participate in STP. Instead, it is transparent to the external switched network and operates as a shared network.

This way, the BPDUs, used by external switches that participate in the STP domain, are forwarded through the Cisco ACI fabric.

![](/images/aciadvanced/aciadvanced_3_2_7.png)

Cisco ACI handles BPDU flooding differently than data traffic flooding that can be enabled or disabled at the BD level. The BPDU forwarding is done within the EPG (that is, fabric encapsulation VXLAN for the FD_VLAN) when the EPG is configured for the data traffic. This process is due to the nature of Per VLAN Spanning Tree (PVST) and Rapid Per VLAN Spanning Tree (Rapid PVST), where BPDUs are tagged with the VLANs configured.

The potential loops that may be present in the external Layer 2 switched network can still be prevented with the same traditional way with STP. The external switches detect redundant connections towards the ACI fabrics with STP and blocks the port if that may cause a Layer 2 loop.

The external switched networks are typically connected via multiple links from two or more switches terminating at different leaf switches and interfaces for redundancy.

Example of the Layer 2 loop issue:

![](/images/aciadvanced/aciadvanced_3_2_8.png)

This figure is an example of the Layer 2 loop issue with STP BPDU handling. In this case, VLAN 20 is used for two different EPGs on two different leaf switches within the same BD. Layer 2 traffic is flooded between EPGs but STP BPDU will not be flooded. The reason for this is that access encap VLAN 20 in each EPG is assigned different VXLAN VNIDs, which are the STP BPDU flooding domain. This causes a loop through the external switches.

### Integrating MST-Based External Networks

The external switched networks, which use the MST or standard (R)STP, exchange BPDUs in an untagged form. They exchange by using the native VLAN configured on the connected interfaces (the data traffic is carried within other tagged VLANs).

By default, Cisco ACI does not enable a native VLAN on leaf interfaces, which would lead to a loop in such networks due to MST BPDUs (received in a non-provisioned native VLAN) being dropped at the leaf.

![](/images/aciadvanced/aciadvanced_3_2_9.png)

To ensure that the Cisco ACI fabric forwards such BPDUs in a native VLAN, the native VLAN needs to be configured on the interfaces that are connected to the external switches. If the native VLAN is already configured for data traffic, BPDUs can flow on the same native VLAN. It is recommended to use VLAN tagging mode “Access (802.1p)” due to flexibility. See the following link for details on the difference between “Access (802.1p)” and “Access(Untagged).”

https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/aci-fundamentals/cisco-aci-fundamentals-52x/m_policy-model.html#concept_450C8C73C1DE4A4EAF6390E862BD9952

Also, make sure to configure MST region in ACI so that the ACI fabric can react to TCN correctly.

### TCN Handling in Cisco ACI

STP topology changes in the external network are indicated with STP BPDU TCN frames – they indicate that a VLAN had a topology change.

Though Cisco ACI does not participate in STP, it intercepts TCN frames to flush MAC address entries to avoid black-holing traffic upon external network STP topology changes. If PVST or Rapid PVST is used, the TCN frame has the VLAN tag that experienced topology change and ACI can flush MAC addresses for the appropriate EPG mapped to that external VLAN.

With MST, the TCN frame carries MST instance ID that had topology change. The administrator therefore has to configure the Cisco ACI policy that binds MST instance to VLAN mapping for each MST instance.

The configuration is implemented in three steps:

*   Creating the MST region instances under the default STP policy,
*   Creating access switch policy group with default STP policy assigned,
*   Applying the switch policy group to the leaf nodes where external network is connected.

These steps are required so that ACI can flush endpoints in appropriate VLANs when TCN is received.

As part of the Spanning-Tree design, you should also make sure that Spanning-Tree topology change notifications (TCNs) due to changes in the forwarding topology of an external Layer 2 network do not unnecessarily flush the bridge domain endpoints in the Cisco ACI fabric. When Cisco ACI receives a TCN BPDU on a VLAN in a bridge domain, it flushes all the endpoints that are associated with this VLAN in that bridge domain. Within an EPG, the VLANs used for connectivity to a switched network should be different from the ones used for endpoints that are directly attached to the leafs of the fabric. This approach limits the impact of Spanning Tree TCN events to clearing the endpoints learned on the switched network.

More information about TCN handling in Cisco ACI is available at https:/www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c07-732033.html#_Toc395143574.

### VLAN to VXLAN Fabric Allocation

The following Cisco ACI constructs are used in the configuration of the VLAN tagging and interfaces:

*   **VLP:** VLAN pool
*   **PHD:** Physical domain
*   **AAEP:** Application Attachable Entity Profile
*   **IPG:** Interface Policy Group.

![](/images/aciadvanced/aciadvanced_3_2_10.png)

Individual VLAN pool has a dedicated range of VXLAN IDs. The Cisco APIC pre-allocates the VXLAN IDs.

The VLAN to VXLAN mapping depends on the VLAN pool configuration. The VXLAN ID is calculated from the pre-allocated VXLAN ID range with the previously mentioned formula.

As such, the same access encapsulation VLAN may be assigned the same or different VXLAN fabric encapsulation ID:

*   When the same VLAN pool is used and attached to physical domains, the interfaces will use the same fabric encapsulation VXLAN ID for the same access encap VLAN. In the example above, access encap VLAN 10 is assigned the same VXLAN ID on all interfaces.
*   When two different VLAN pools that contain the same access VLANs are used and attached to different domains, different fabric encap VXLAN IDs are assigned to the same access VLAN from each VLAN pool. In the example above, the VXLAN ID for VLAN 10 is different on interfaces from domain 1(PHD-1 > AAEP-1 > IPG-1) and domain 2 (PHD-2 > AAEP-2 > IPG-2).

### Considerations on Overlapping VLAN Ranges in Multiple VLAN Pools

A case in using overlapping VLAN pools may be where two external switched networks using the same VLANs exist in two pods in a multi-pod deployment. The workloads in the two external switched networks would only need to communicate via Cisco ACI fabric. The two external networks would need to be its own STP domain–a split STP BPDU flooding domain is a common in multi-pod environment. Using separate EPGs per pod (with same VLAN from overlapping VLAN pools) prevents BPDUs from being sent across Cisco ACI between those external switched networks.

On the contrary, using overlapping VLAN pools with two different domains and single AAEP may lead to Layer 2 loop.

![](/images/aciadvanced/aciadvanced_3_2_11.png)

In such misconfigurations, the same access encapsulation VLAN is assigned different VXLAN IDs on different ports.

As shown in the diagram, the access encapsulation VLAN 10 on one port is assigned VXLAN ID 8001 while on the other port it is assigned VXLAN ID 9010. Since these ports are connecting links to external switches, it leads to situations where STP BPDU flooding is broken and external switches cannot detect the loop over the overlay that is provided by the Cisco ACI.

When overlapping VLAN pools exist under an EPG, then the FD VNID allocated for the EPG by each switch is non-deterministic and different switches may allocate different VNIDs, causing EPM sync failures between leafs within a vPC domain (causing intermittent connectivity for all endpoints within the EPG). It can also cause bridging loops if user is extending STP between the EPG, as the BPDUs will be dropped between switches due to FD VNID mismatch.

A global feature that is called **Enforce EPG VLAN Validation** prevents association of overlapping VLAN pools on a single EPG. Once enabled, when an attempt to allocate a domain on an EPG is performed, and the domain contains a VLAN pool with a range overlapping with another domain already associated to the EPG, then the configuration is blocked. You can enable the feature by navigating to **System > System Settings > Fabric Wide Setting.**

### Loop Prevention with MCP

There are cases where LLDP and STP cannot detect external Layer 2 loops due to misconfiguration and miscabling. One example of such misconfiguration is an external firewall that is deployed in transparent mode where traffic is bridged between interfaces. The broadcast storm resulting from traffic that is looping causes connectivity issues to the networking equipment and servers that are connected in that broadcast domain.

Look at the example of the Layer 2 loop issue. In this case, VLAN 20 is used for two different EPGs on two different leaf switches within the same BD. This case means that Layer 2 traffic is flooded between EPGs but STP BPDU will not be flooded because access encap VLAN 20 in each EPG is assigned different VXLAN VNIDs, which are the STP BPDU flooding domain. This causes a loop through the external switches.

To detect and break such loops, MisCabling Protocol (MCP) should be used in the Cisco ACI fabric. MCP uses its own Layer 2 packets with custom MAC address that are sent on every configured EPG. If such packet is received on any of the leaf interfaces, that indicates a loop. The action upon that can be an alert with a fault or, better yet put an interface that in errdisable state (which effectively breaks the loop).

![](/images/aciadvanced/aciadvanced_3_2_12.png)

### Using MCP

MCP can be enabled globally and per leaf interface.

![](/images/aciadvanced/aciadvanced_3_2_13.png)

The global MCP configuration defines:

*   Admin state that is disabled (default) or enabled.
*   Whether MCP PDUs are sent for each VLAN or just for untagged ones (default)—this option should be enabled.
*   Action that is taken upon loop that is detected—to break the loop, the Port Disable option must be used.
*   Optional parameters such as various timers defining when and how quickly MCP reacts:
    *   Initial Delay in seconds defining how quickly MCP reacts with a selected action to a detected loop.
    *   Loop Detect Multiplication Factor defining the number of MCP packets received that are used to determine a loop has been formed.
    *   Transmission Frequency defining how often the MCP packets are generated and sent.

By default, the MCP configuration is disabled globally and enabled on each leaf interface. For the MCP to work it has to be enabled globally and on selected interfaces.

##  3.3)    Migrating Existing Switched Networks to Cisco ACI

Migrating an existing switched network to Cisco ACI requires preparing the design of the Cisco ACI solution and, more importantly, a migration plan. A migration plan depends on the state of the existing switched network, which is usually called the “Brownfield” network. It also depends on how the existing switched network is implemented, its size, the services that are deployed, and what the desired final state is once migration is finished. The migration to the Cisco ACI ("Greenfield" network) will be typically gradual since the workloads that are connected to existing switched network must continue to operate with no or minimum downtime.

The general methodology of migrating existing network to Cisco ACI fabric can be divided into three steps: deployment, integration, and migration.

![](/images/aciadvanced/aciadvanced_3_3_1.png)

The deployment step consists of Cisco ACI fabric design and implementation that includes activities such as:

*   Selecting proper leaf and spine switches, Cisco APIC controllers.
*   Bring up and initialize the Cisco ACI fabric with necessary operational and maintenance policies (NTP, DNS, syslog, firmware management, configuration management, and so on).
*   Design how the network constructs should be configured in the Cisco ACI fabric such as:
    *   **BD (Layer 2 domain scope):** How many subnets should one BD contain? How to map the current VLAN design into the ACI BD design and so on.
    *   **VRF (Layer 3 domain scope):** Is VRF separation required? Should VRF route leaking be configured and so on.

*   Design how the policy (security) constructs should be configured in the Cisco ACI fabric such as:
    *   **EPG:** Which applications should be able to communicate without a contract? What is the best EPG grouping to optimize contract configurations? Does EPG need to include applications with more than one subnet (related to how to design BD)? How should VLANs be allocated for each EPG and so on.
*   Design how the multi-tenancy should be configured in the Cisco ACI fabric such as:
    *   **Tenant:** How should VRF, BD, EPG, and so on be logically grouped for the best operation/management.
    *   **Domain:** How should VLANs and interfaces be allocated to each EPG, company organization or customer?

The integration step includes activities that are required to properly connect existing network with Cisco ACI fabric:

*   Selecting and connecting interfaces on the side of the existing network and on the Cisco ACI fabric side.
*   Implementing configuration on the Cisco ACI fabric side that may include extending Layer 2 domains or providing Layer 3 connectivity between the environments.

The migration step includes activities that are required to move the workloads from existing network to Cisco ACI fabric, which is typically the longest part of the migration process that may take several months to complete. The complexity and duration of this step is largely due to the requirement of no or minimum downtime. The activities here may include the following:

*   Migrating whole clusters or individual servers to the Cisco ACI fabric.
*   Migrating virtual workloads from old clusters that are connected to the existing network to new clusters that are connected to the Cisco ACI fabric.
*   Moving default gateway function from existing network to Cisco ACI fabric.
*   Migrating L4-7 network services, that is, service appliances like firewalls and load-balancers.

### Existing Network Aspects

Migrating existing network requires understanding its aspects of physical and logical topology.

The physical topology is defined with switches and the connectivity between those switches, what kind of links are used, what is the throughput of those links, what kind of redundancy and loop prevention mechanisms are used, how those mechanisms are configured and used, where and how workloads are connected to the network, and so on.

The logical topology is defined with the IP subnets and VLAN network segmentation, placement of default gateway, where and how routing between IP subnets is implemented, which and how the mechanisms to implement default gateway high availability are used, security policies in a form of packet filters between VLANs or defined at network service appliances, and so on.

To plan the migration to Cisco ACI at minimum a list of VLANs where workloads are placed (with a list of workloads) must be collected along with corresponding IP subnets and default gateway configuration.

![](/images/aciadvanced/aciadvanced_3_3_2.png)

An existing network may be implemented with different physical topologies—a classical tier core-distribution-access with distribution implementing Layer 3 boundaries.

Or as the example shows a Layer 2 leaf-spine topology using vPCs to address some of the STP shortcomings.

*   The VLANs 10 and 20 in the example are trunked on all links towards spines and between leaf switches (as any other VLANs that may be used in that network).
*   Default gateway for both VLANs is defined at the spine switches. To achieve redundancy, HSRP is deployed for each VLAN with one spine switch being active router and the other being standby.

### Virtual Infrastructure Aspects

Many of the workloads that will be migrated will be in virtualized (most typically on VMware vSphere virtual infrastructure) which is why understanding the aspects and specifics of virtual infrastructure is important. Virtual infrastructure is built with hosts that are grouped in clusters that have virtual network layer configured. The virtual network layer is typically built with distributed virtual switches. A cluster may have a single or multiple virtual switches.

![](/images/aciadvanced/aciadvanced_3_3_3.png)

![](/images/aciadvanced/aciadvanced_3_3_4.png)

Typically, there will be two DVSs used to achieve better robustness of the virtual infrastructure:

*   Management DVS that is used for the VLANs and IP subnets that are used for management and operations of virtual infrastructure. For instance, VMware ESXi management vmkernel port, VMotion vmkernel port, IP SAN vmkernel for when iSCSI or NFS storage is used with the VMware vSphere environment.

*   VM DVS that is used for all VLANs and IP subnets that workload VMs are using for their communication.

Individual DVS will have at minimum two NICs configured as uplinks to redundantly connect to the physical network. The infrastructure DVS NICs trunk all infrastructure VLANs and the VM DVS NICs trunk all VM VLANs. It is very important to check the load balancing and NIC teaming configuration of these redundant uplink connections. It’s important because ACI learns the IP address from data plane that may cause IP flapping depending on the NIC configuration. For example, when a Microsoft Hyper-V is configured with Switch-Independent Teaming Mode (such as, without a port-channel) with Address Hash or Dynamic load-balancing mode, the packets with the same source IP address are sent to the ACI fabric from multiple uplink NICs with MAC address of each NIC at the same time. This will cause IP flapping between the two MAC addresses of uplink NICs due to data-plane IP learning of ACI. In this case, the load-balancing mode of Microsoft server should be changed to Hyper-V mode to ensure that one IP is always tied to one MAC.

### Connecting Existing Network to Cisco ACI

Once the Cisco ACI fabric is initialized and operational, the first step in the migration is to connect the existing network to the Cisco ACI fabric.

The existing network should be connected in redundant manner with at least two separate links originating from different switches terminating at different Cisco ACI leaf switches.

The exact number of links that are used depends also on the bandwidth that may be required between existing network and Cisco ACI fabric—the amount of bandwidth depends on how much traffic will need to be exchanged between the two networks and how long and quickly the migration will be done.

At the existing network, the interconnecting links should be placed either at the dedicated border switches or at the switches that are used to aggregate the connectivity of other switches in the topology. At the Cisco ACI fabric, the interconnecting links are connected to a pair of Cisco ACI leaf nodes. Such connectivity will be used to extend the VLAN segments from existing network. Therefore, the interconnecting links will be configured as 802.1Q trunks for the required VLANs.

![](/images/aciadvanced/aciadvanced_3_3_5.png)

In the example, the interconnection is implemented in a fully redundant manner:

*   Four interconnecting links are used and configured in a double-sided vPC—this ensures that all configured links are utilized and no STP loop is created via Cisco ACI fabric.
*   In the existing network, a pair of links terminates at one or another aggregating spine switch.
*   At the Cisco ACI fabric, two pairs of links terminate at two leaf switches.
*   The vPC is configured as 802.1Q trunk for VLANs 10 and 20 (and other VLANs that need to be migrated from the existing network).

### Migrating Workloads

To be able to migrate the workloads from existing network to Cisco ACI, the network segments where workloads are connected must also be implemented at the Cisco ACI fabric.

The simplest approach is to extend the existing segments using the same VLAN IDs as used in the existing network.

In the Cisco ACI, where endpoints are grouped based on EPGs and not VLANs, EPG for each segment to be migrated should be created. The interconnecting links should have all the EPGs statically mapped to the relevant existing VLANs. The interconnecting links must have EPGs and static mapping that is configured for all VLANs that need to be migrated. The individual ports at Cisco ACI leaf switches, where workloads are meant to be migrated, should also have required EPGs statically mapped to proper VLANs. Individual port may have multiple EPGs with static VLAN mapping assigned when it is connecting a virtualization host.

![](/images/aciadvanced/aciadvanced_3_3_6.png)

In the example, workloads from VLAN 10 and 20 need to be migrated to the Cisco ACI fabric: the workloads from VLAN 10 belong to EPG-VLAN10 and those from VLAN 20 to EPG-VLAN20.

The mapping in the Cisco ACI is done by creating BD and EPG per VLAN. You create BD-VLAN10, EPG-VLAN10, BD-VLAN20, and EPG-VLAN20. The reason for separate BDs is to keep separate Layer 2 domains for each segment as in the existing network. This approach is called Network-Centric Design, which will be discussed later on.

Next, the mapping of VLANs to EPGs is implemented. First at the vPC connecting the existing network with Cisco ACI—both EPG-VLAN10 and EPG-VLAN20 are applied to vPC with VLAN 10 statically mapped to EPG-VLAN10 and VLAN 20 statically mapped to EPG-VLAN20. EPG-VLAN10 is then applied to the leaf ports that are selected for connecting App1 workloads upon migration with VLAN 10 statically mapped to it. Likewise, EPG-VLAN20 is applied to the leaf ports where App2 workloads upon migration will be connected with VLAN 20 statically mapped to the EPG.

The VLANs 10 and 20 are extended over to the Cisco ACI fabric from the existing network via vPC as EPG-VLAN10 in BD-VLAN10 and EPG-VLAN20 in BD-VLAN20.

With this configuration, VLAN 10 and 20 are extended to the Cisco ACI fabric and workloads can be easily migrated to the selected leaf ports while preserving connectivity and not having to do any readdressing.

Cisco ACI by default optimizes the traffic forwarding in the BD—Layer 2 unknown unicast flooding and ARP flooding are disabled since endpoints that are connected to the Cisco ACI fabric are supposed to be well known. The endpoints MAC and IP addresses should be present in the local endpoint table on leaf switches, if not then in the COOP database on spine switches so that the traffic towards the MAC or IP can be sent directly to the endpoint instead of flooding.

During migration, some workloads will be migrated while the others are still located in the existing network. Therefore, the Cisco ACI fabric may take long time to learn MAC and IP addresses of the nondirectly connected workloads so their MAC addresses may not be present even in the COOP database on spine switches. In such cases, Layer 2 unknown unicast traffic will be discarded when Layer 2 Unknown Unicast in the BD is set to hardware-proxy, which results in communication issues between Cisco ACI and non-ACI connected workloads.

Like that, ARP in the existing network is used to resolve MAC addresses to IP addresses and relies on broadcasts. Since non-ACI connected workloads may not be known to Cisco ACI fabric when Cisco ACI connected workloads issue ARP requests, the ARP request cannot be directly sent to the destination when ARP Flooding is disabled. If the BD was already configured with the BD subnet and Unicast Routing is enabled, this would not be a big issue since ACI has the silent host detection algorithm that is called ARP glean. In this phase, the BD should not have Unicast Routing enabled because the default gateways of the Layer 2 segment (VLAN 10 and 20) are in the existing external network.

![](/images/aciadvanced/aciadvanced_3_3_7.png)

To avoid any issues due to Layer 2 flooding optimization, the following parameters of BDs must be set to the following values:

*   Layer 2 Unknown Unicast should be set to Flood to prevent traffic to an unknown MAC from being dropped.

Migrating workloads to Cisco ACI can also be taken as an opportunity to separate application tiers and implement more strict communication control.

When this is desired, the same VLANs from the existing network do not need to be used in the Cisco ACI fabric like the example in the previous picture. Rather than that, EPGs should be defined so that the instances of the same application tiers are placed in the same EPG. This approach is called Application-Centric Design, which will be discussed in detail in the next topic. Still, ACI leaf interfaces connecting to the existing network are typically configured with the same VLAN as the existing network (VLAN 10 or 20 in the example picture). These existing VLANs belong to separate EPGs dedicated for the external network.

The communication between different EPGs is by default that is blocked (that is, allow list mode). Therefore, communication between workloads in the existing network and those already migrated to Cisco ACI fabric with Application-Centric Design is blocked if configuration is not adjusted. Users can configure the contracts between each EPG like it should be in the final state.

Or users can put off the contract configurations at this phase and focus on the migration of workloads by allowing all the communication, which users can accomplish in three ways:

*   Enabling the communication without enforcing contracts for all EPGs by setting the Policy Control Enforcement mode in the VRF to Unenforced. This approach is not recommended when contracts will be used in the future since flipping the Policy Control Enforcement mode is disruptive.
*   Using the vzAny object to provide and consume a contract allowing all IP traffic to all EPGs.
*   Using the Preferred Group Member option for EPGs for which communication to each other should not be enforced with contracts.

When putting off the contract configurations, the Preferred Group is the most recommended way because users have a control to take out one EPG at a time from the Preferred Group to fully enforce the contract on it.

![](/images/aciadvanced/aciadvanced_3_3_8.png)

In the example, VLAN 10 with subnet 10.1.1.0/24 is populated with two EPGs (that is, EPG-VLAN101 and EPG-VLAN102). The migration to Cisco ACI is used to separate these tiers to start implementing more strict communication control. The Cisco ACI configuration and connectivity to the existing network are designed in the following way:

*   A single BD is used for the VLAN 10 network segment from the existing network with all EPGs placed in this BD.
*   Two separate EPGs are defined for the two application tiers—EPG-VLAN101 and EPG-VLAN102.
*   New EPGs are mapped to different VLANs at the Cisco ACI leaf ports, that is, EPG-VLAN101 to VLAN 101 and EPG-VLAN102 to VLAN 102.
*   The workloads that are still connected to the existing network are placed in EPG-Out10.
*   EPG-Out10 is mapped to VLAN10 at the vPC connecting the two fabrics.
*   The contracts allow communication between EPG-Out10 and internal EPGs.

### Default Gateway Placement

So far, the migration from the existing network has been implemented for the communication within the Layer 2 network segments. The bridging (intrasubnet) traffic between the workloads can be completed within the Cisco ACI fabric or seamlessly flow through the connections between the ACI fabric and the existing network.

At this stage of migration, the Cisco ACI fabric is used as Layer 2 fabric only. The default gateways for IP subnets are still at the existing network. Any routing (intersubnet) traffic needs to flow to the default gateway in the existing network where it is routed between the subnets regardless of whether the workload has migrated to the ACI fabric.

![](/images/aciadvanced/aciadvanced_3_3_9.png)

The example shows the default gateways for networks 10.1.1.0/24 and 10.2.2.0/24 implemented at the distribution layer of the existing network. VLANs 10 and 20 have already been extended over to the Cisco ACI fabric via vPC as EPG-VLAN10 in BD-VLAN10 and EPG-VLAN20 in BD-VLAN20. These BDs should have the following parameters:

*   Layer 2 Unknown Unicast set to Flood to prevent traffic to an unknown MAC from being dropped.
*   ARP Flooding enabled to prevent ARP requests from being dropped due to incomplete ARP glean (silent host detection).
*   Unicast Routing is disabled to make the BD behave as pure Layer 2 as routing is implemented in the existing network.

Migration of default gateway will be typically implemented per network segment that is one gateway at a time. Therefore, during migration, some gateways, and connectivity to external networks (that is WAN, internet) will be in the existing network and some in the Cisco ACI. These networks need to be accessible during migration from those already moved to Cisco ACI.

To enable connectivity between migrated and non-migrated networks for the time of migration, a routed connectivity between existing network and ACI with L3Out is implemented (it can be implemented over routed interfaces, subinterfaces, or SVI over vPC). The L3Out is associated with the BDs where default gateways will be migrated. Since L3Out is an EPG, by default a contract is needed between L3Out and individual EPG, this allows communication and exchange of routing information.

It is recommended that the L3Out is configured with dynamic routing (for example using OSPF or BGP) so that the networks in the existing network are no longer announced to the ACI once the subnet is moved over.

![](/images/aciadvanced/aciadvanced_3_3_10.png)

Gateway migration is implemented by removing the default gateway configuration in the existing network and applying the IP address of the default gateway at the BD level for the Gateway Address field under the Layer 3 configurations tab. If BD has not yet been associated with the L3Out and contract that is assigned to the EPG, this has to be implemented now for the communication with the networks outside of the ACI.

Along with that such BD also has to enable the Unicast Routing. Layer 2 unknown unicast flooding and ARP flooding must still be enabled at the BD level since not all workloads are yet migrated to the Cisco ACI. The BD should be configured with the MAC address of the default gateway from the existing network. This approach makes the gateway migration more seamless for the endpoints, since they do not need to relearn their gateway MAC address by refreshing their ARP cache entries.

With these configuration changes, the Cisco ACI is enabled with the pervasive gateway (anycast gateway) functionality, that is, any leaf switch will route traffic between configured networks for the Cisco ACI connected endpoints.

The workloads that are still connected to the existing network will be using the two border leaf switches connecting Cisco ACI fabric to the existing network as gateways.

![](/images/aciadvanced/aciadvanced_3_3_11.png)

In the network from the example, the VLAN 10 and 20 gateways are moved to the Cisco ACI fabric. Both are configured at two distribution switches in the existing network under Layer 3 VLAN interfaces with HSRP for gateway high availability. The procedure of moving the gateway presents short disruption to the connectivity since configuration has to be removed at the existing network and applied at the Cisco ACI.

![](/images/aciadvanced/aciadvanced_3_3_12.png)

You can then remove the connections between existing L2/L3 switches and core routers. The remaining connections are the Layer 3 extension between the core routers and the ACI fabric.

The migration for the VLAN 10 gateway 10.1.1.1 is done with the following steps:

*   The HSRP MAC address for VLAN 10 is verified at one of the distribution switches.
*   BD-VLAN10 is configured with 10.1.1.1/24 as the gateway address. (Unicast Routing is still disabled)
*   BD-VLAN10 is applied with the former HSRP MAC address.
*   The Layer 3 VLAN interface at both distribution switches is disabled.
*   The Unicast Routing is enabled at BD-VLAN10 (this will cause a minor service impact).
*   The 10.1.1.0/24 route is checked on border leaf switches–it is now local to ACI and no longer learned via L3Out anymore.

Once these steps are completed, the 10.1.1.1/24 is available as pervasive gateway.

The same procedure is used for the VLAN 20 gateway 10.2.2.1.

### Concluding Workloads Migration

The final step in migrating existing network can be implemented once all workloads are migrated to the Cisco ACI fabric. The migration of the bare-metal devices could be done using dual adapters (one connected to the existing environment and the other connected to Cisco ACI) and making a failover after all the objects and policies in Cisco ACI are prepared and the gateway is migrated to Cisco ACI.

With all the endpoints connected directly to Cisco ACI, the flooding behavior in the Cisco ACI can again be optimized. The flooding optimization is implemented per BD that needs to be reconfigured. Reconfiguring BD with the flooding optimization setting will incur some service impact, thus it is recommended to do that during the maintenance window.

![](/images/aciadvanced/aciadvanced_3_3_13.png)

In the network example, BD-VLAN10 and BD-VLAN20 can be reconfigured as follows:

*   ARP Flooding is disabled.
*   Layer 2 Unknown Unicast forwarding option is changed from Flood to Hardware Proxy.
*   Unicast Routing is enabled.

Apart from flooding optimization, it is also recommended to enable the Limit IP learning to Subnet option at the BD to prevent endpoints being learned incorrectly (that is, learning of endpoints from IP subnets that are not configured at the BD, to prevent issues due to spoofing and misbehaving endpoints).

### Migrating Virtual Workloads

Although the principals are the same for migration of both virtual and physical workloads, this topic explains a specific consideration point for virtual workload migration with a simple example.

Migrating virtual workloads to Cisco ACI fabric consists of the following general steps:

*   Implementing hypervisor hosts in the Cisco ACI fabric.
*   Implementing virtual switching layer at the Cisco ACI connected hosts to provide connectivity for VMs.
*   Migrating VMs from hosts that connected to the existing network to those connected to the Cisco ACI fabric.

The migration of the VMs may be live (hot) or offline (cold). Live migration is preferred since it is seamless for the operation of the applications. Whether migrating VMs live is possible or not depends on the live migration prerequisites that are related mostly to the virtual infrastructure (that is, mutual network connectivity, access to storage where VMs are stored, single vCenter Server that is used or minimum version 6.0 of vSphere used with separate vCenter Servers if there is VMware).

The virtual infrastructure in the existing and Cisco ACI fabric may be implemented in different ways. The most seamless implementation for migration is shown in the example (that is, the VMs can simply be moved between hosts):

*   Same vCenter Server is used to manage hosts in the existing network and Cisco ACI fabric.
*   Hosts may be part of the same or different high-availability vSphere cluster (in the example a different cluster is used).
*   Virtual network layer is extended between the hosts in existing network and Cisco ACI fabric.

![](/images/aciadvanced/aciadvanced_3_3_14.png)

### Implementing Hypervisor Hosts

The hypervisor hosts to which VMs will be migrated can either be existing or new. The migration process differs only in moving existing physical hosts to Cisco ACI fabric. Existing hosts need to be put to maintenance mode so that no VMs are running on the host while it is migrated to Cisco ACI. In both cases, individual host NICs then need to be connected to the selected leaf ports.

### Implementing Virtual Switching Layer

In the sample scenario that was introduced earlier, the hosts are implemented with two VMware VDSes that are simply extended between existing and new cluster since the same vCenter Server manages both.

The dvsMgmt is used for the operations and management. It has two dedicated NIC uplinks per host that carry management, vMotion, and IP SAN VLANs and is simply extended to the new hosts. To use the same management VLANs and IP subnets with new hosts, the Cisco ACI configuration is implemented with the following:

*   Single bridge domain BD-Mgmt where all EPGs for VM management infrastructure are defined (individual EPG could also be placed in its own BD. A single BD is selected to simplify the configuration).

*   EPG-host for the ESXi management vmkernel interfaces from VLAN management.

*   EPG-VMotion for the ESXi VMotion kernel interfaces from VLAN VMotion.

*   EPG-IPSAN for the ESXi kernel interfaces that are used for accessing IP-based storage from VLAN IPSAN.

In this example, these EPGs are statically mapped to the VLANs at the leaf ports where host management NICs are connected and at the leaf vPC connecting the existing network.

The dvsVM1, used to carry the application workload traffic, has also two NIC uplinks per host. At the vSphere level, the dvsVM1 is extended to new hosts connected to the ACI fabric simply by adding them to the DVS and assigning the previously mentioned NICs. There are several application VMs placed in separate VLANs that are configured under port-groups at the DVS level. By adding hosts to dvsVM1, the port-groups are also provisioned at these hosts. The VLANs for the port-groups are extended to the Cisco ACI by creating a BD and EPG pair for each individual VLAN. In this example, each such EPG is then statically mapped at the leaf ports that are connecting host uplink NICs and also at the vPC connecting existing network.

With such configuration, the VMware ESXi hosts are connected in the same way as physical servers, that is, with pre-provisioned static mapping of EPGs to VLANs within physical domain.

With dvsVM1 extended to new hosts with existing port-groups at the vSphere level and Cisco ACI configured with proper EPG to VLAN mapping, the same Layer 2 connectivity is available across existing and new vSphere cluster. No changes to the location of the default gateway are required at this point, that is, it remains at the existing network for the time of migration. The VMs can now be simply moved from existing hosts to new hosts.

### Migrating Virtual Workloads with VMM Integration

However, please be noted that instead of extending the VMware DVS from the existing network, users can also create a new VMware DVS by using ACI VMM integration from the beginning of the migration as long as the EPGs (port-groups) belong to the expected BDs. With such integration, the VLANs do not need to be statically mapped to the EPGs but are rather dynamically assigned upon creating EPGs. The EPGs also get dynamically instantiated at the vSphere level as port-groups.

Look at the simple example of VMM integration. In this example, you have a hypervisor host, part of a cluster that has two DVSes: dvs_Mgmt and dvs_Legacy. Each of them has uplink NICs connected to switches in the legacy environment.

![](/images/aciadvanced/aciadvanced_3_3_15.png)

After VMM integration of Cisco APIC and vCenter Server has been prepared, a new DVS_VMM is created. Next step is to add an uplink NIC to this DVS. In this example, uplink NIC 2 is taken from dvs_Legacy and added to dvs_VMM. After EPG-VLAN10 is associated with VMM domain, a new port group will be created in vCenter server used by dvs_VMM. All VMs are still patched to PG_VLAN10_Legacy port group and all the traffic still flows through the legacy switches.

![](/images/aciadvanced/aciadvanced_3_3_16.png)

Next step is to repatch the VMs (such as, change the network label of a VM) to the new PG_VLAN10_ACI to start using dvs_VMM and uplink NIC 2.

![](/images/aciadvanced/aciadvanced_3_3_17.png)

Final step after all the VMs have been repatched and are already using the new dvs_VMM is to move Uplink NIC 1 to dvs_VMM. Finally, do the same procedure with Mgmt NICs (if the plan is to migrate all the NICs to ACI). You can then remove the dvs_Legacy since all the adapters are now on dvs_VMM.

![](/images/aciadvanced/aciadvanced_3_3_18.png)

To sum up, the migration to dvs_VMM and dynamic port groups is done in the following way:

*   Dynamic VLAN pool is created
*   VMM domain is created at the APIC and associated with the vCenter Server.
*   The dvs_VMM is created at the vCenter Server.
*   New EPGs are created for the VM application groups and assigned to the VMM domain. Or use the same EPGs for dvsVM1 to keep using the same contracts that have been working.
    *   APIC takes care of assigning a VLAN to each newly created EPG from the dynamic VLAN pool.
    *   APIC triggers creation of port-groups at vCenter Server—the name of individual port group is concatenation of tenant, application profile, and EPG name separated with delimiter.
*   Virtual infrastructure administrator adds ESXi hosts to dvs_VMM and assigns NIC 2 as uplink.
*   VMs can be moved to dvs_VMM by simply changing the current port-group to a selected newly created port-group.
*   Once all VMs are migrated to dvs_VMM, the old dvs_Legacy can be decommissioned.

**Note:** Please have in mind that this is just an example of virtual workload migration. It is up to the design, schedule, and so on about whether extending the existing non-ACI-integrated DVS during the migration phase and have another migration between nonintegrated DVS to ACI-integrated DVS, or start the migration with ACI-integrated DVS from the beginning.

##  3.4)    Network- vs. Application-Centric Deployment Models

Cisco ACI is designed and deployed with access policies encompassing switch and interface profiles, groups, VLAN pools, physical domains, and configuration constructs like VRFs, BDs, EPGs. The same network environment can be designed and implemented in multiple ways using those constructs. There are two approaches that can be used to design and deploy the Cisco ACI configuration—network and application-centric deployment model. The differences between the deployment models are which, how and where the Cisco ACI constructs are used.

Using a network- or application-centric deployment model is a choice that is made upon designing the Cisco ACI deployment. There is no config option in the APIC with which one or another model is turned on.

The differences between the two models are in which and how config constructs are used to describe the environment, the number of them used, where the default gateway is placed, where and how security policies are implemented, and so on.

The starting point for the Cisco ACI design and deployment is how the application logical topology that is used in the environment is seen and understood.

An application blueprint or logical topology is description of how deployed application should look like. The application logical topology therefore encompasses the following aspects of an application:

*   Application tiers that comprise a particular application
*   Network segments that are used in the logical topology
*   How and where the application tiers and their instances are connected to the network
*   What traffic flows are used between application tiers (that is, how application tiers communicate between themselves)

![](/images/aciadvanced/aciadvanced_3_4_1.png)

An example shows a layout of an application logical topology for a deployment of a WebSphere Business Process Manager (BPM) application that is used for business process modeling (note that this blueprint is just a subset of the complete logical topology to serve as an example for the Cisco ACI deployment model discussion). Mind that such blueprint is not always available, that is, it may be more or less complete.

The application is composed of four application tiers:

*   Front-end tier is client facing part through which business users access the BPM application.
*   Process Servers tier is the middle tier that is used for application deployment and messaging. The processing in this tier is based on the input from Front-end tier.
*   BPM Managers tier encompasses the management services that are used to monitor, update, scale, and so on other tiers of the application.
*   BPM databases tier is the databases that are used to store all application-related data like information about the architecture of individual business process models, who created that model, what applications the business model requires, monitoring data for the BPM application, and so on.

The instances of application tiers are put in four separate network segments, that is, VLANs and IP subnets.

The traffic flows are defined with arrows indicating client and server part of the communication along with TCP port (for example the Process Servers listen at TCP ports 55025 and 55080).

The blueprint also indicates that the security policy should protect individual tier, allowing only defined communication.

### Network-Centric Design

The network-centric design is based on a topology that is seen as number of network segments. All communication is defined based on VLANs, their respective subnets, and traffic flows between these VLANs. The granularity of communication policies is defined with network segments.

The network-centric approach is typically used as a first step in migrating existing network (that is, brownfield environment) to Cisco ACI because the network construct/topology such as flooding domain is the same as what’s already deployed in the existing network.

It includes connecting external switched segments to Cisco ACI fabric, existing firewall and load-balancing appliances, and little or no changes to the network layout.

![](/images/aciadvanced/aciadvanced_3_4_2.png)

In the network-centric deployment model, the Cisco ACI fabric is deployed as a traditional switching fabric:

*   Individual VLAN (and optionally IP subnet) corresponds to a single BD encompassing single EPG. The endpoints that are part of EPG belong to a single VLAN/IP subnet. This can simply be described as 1 BD = 1 EPG = 1 VLAN.
*   Default gateway for individual IP subnet may be implemented on a routing device external to Cisco ACI fabric (for example, firewall) and not at the BD level.
*   BD may be configured with flooding enabled since existing switched networks are typically connected to Cisco ACI and the default gateway may be implemented there.
*   Layer 4 through Layer 7 services are integrated using bridging (for example, firewall that is implemented in a transparent mode) or routing.
*   Security policies are implemented at Layer 3 boundaries as ACLs that define which VLANs are allowed to communicate.

The implementation of security policies depends on where default gateway is defined:

*   When external device is used for default gateway (such as firewall), the ACLs between VLANs are defined as firewall rules between VLANs attached.
*   When default gateway is defined at the BD, ACI will be routing the traffic and contracts are required to allow the traffic between EPGs. Since 1 BD = 1 EPG = 1 VLAN, the contracts are actually used as ACLs between VLANs even though the filters that are used by contracts are defined only with ports and protocols.

The Cisco ACI deployment that is using a network-centric approach for the previously mentioned BPM application relies on the application blueprint that is presented earlier. Two design options are presented – the differences are in how ACI fabric is used and where security policies are implemented.

![](/images/aciadvanced/aciadvanced_3_4_3.png)

The first design option is defined in the following way:

*   Cisco ACI fabric is implemented as a regular Layer 2 switched fabric.
*   There are four separate BDs using arbitrary names incorporating VLAN number (BD 100 – BD 103).
*   All BDs are put into a single VRF.
*   Each BD is configured with a single EPG (EPG 100—EPG 103) also using arbitrary names incorporating VLAN numbers.
*   The default gateway functionality for all segments is implemented on the external central firewall.
*   All routing and security policies between the VLANs 100 to 103 are implemented via external central firewall.

![](/images/aciadvanced/aciadvanced_3_4_4.png)

The second design is defined with using ACI fabric for all functionality:

*   Cisco ACI fabric is implemented as Layer 3 routed fabric.
*   The same four separate BDs using arbitrary names incorporating VLAN number (BD 100–BD 103) are used.
*   All BDs are put into a single VRF.
*   Each BD is also configured with a single EPG (EPG 100–EPG 103) also using arbitrary names incorporating VLAN numbers.
*   The default gateway functionality for individual segment is implemented at the BD level.
*   Routing between the VLANs 100 to 103 is implemented at ACI fabric.
*   Security policies defining allowed traffic are defined with contracts that are applied to EPGs, which equal to VLANs.

### Application-Centric Design

The application-centric design is based on the layout and requirements of applications. The communication is defined by the endpoint EPG membership and relationships between these EPGs. VLANs and IP subnets are not relevant from the perspective of defining these relationships. The granularity of communication policies is defined with the EPGs.

In the application-centric deployment model, all endpoints will typically be connected directly to the Cisco ACI fabric (note that this is recommended and not required). The approach may be used in new data center implementations or as a second step in implementing Cisco ACI when migration of existing environments needs to be done.

![](/images/aciadvanced/aciadvanced_3_4_5.png)

In the application-centric deployment model, the Cisco ACI fabric is deployed following the application architecture and relationships between application tiers:

*   There will be a single or just few BDs that can be optimized from the forwarding perspective since flooding and extending to the external switched networks is not required.
*   A single BD will be implemented with multiple EPGs.
*   Endpoints will be grouped into EPGs based on their communication requirements (that is, typically the endpoints of particular EPG will belong to the same application tier).
*   An individual subnet may be split into multiple EPGs.
*   Default gateway for individual IP subnet is implemented at the Cisco ACI fabric—single BD will be configured with multiple IP subnets.
*   Security policies are implemented with contracts that are applied to EPGs.

With application-centric mode, the security policies are not limited with VLAN/subnet boundaries as EPGs do not equal IP subnets. The contracts using filters that are based on ports and protocols that are applied to EPGs are used to control the traffic between application tiers regardless of the IP subnet they belong to.

In the application-centric approach, the topology of the previously mentioned BPM application is seen as several application tiers as presented in the application blueprint.

![](/images/aciadvanced/aciadvanced_3_4_6.png)

The design may be defined in the following way:

*   All endpoints are connected directly to the Cisco ACI fabric.
*   A single BD is defined for the BPM application.
*   Four distinct EPGs are defined individual application tier.
    *   EPG-frontend encompasses all servers that are used by users for accessing the BPM application.
    *   EPG-mngr encompasses all BPM manager servers that are used to monitor and manage BPM application.
    *   EPG-procsrv encompasses all BPM process servers that provide messaging and application deployment services.
    *   EPG-DB encompasses all BPM DB servers that are used to store application data and are accessed by the other application tiers.

*   All four default gateways and IP subnets are defined at the BD-BPM level providing pervasive gateway functionality at the Cisco ACI fabric.
*   Three contracts are defined and applied to allow traffic as defined in the application blueprint:
    *   ctr-front is consumed by EPG-mngr and provided by EPG-frontend to allow BPM manager servers to monitor and manage BPM front-end servers
    *   ctr-psrv is used to allow BPM manager servers and front-end servers to access BPM process servers. It is provided by EPG-procsrv and consumed by both EPG-frontend and EPG-mngr.
    *   ctr-db is used to allow BPM manager, process, and front-end servers to access BPM DB servers. It is provided by EPG-DB and consumed by EPG-frontend, EPG-mngr, and EPG-procsrv.
*   In Application-Centric design, a single BD can have multiple EPGs. It is recommended to use different VLANs for different EPGs. You could reuse a VLAN ID on a different EPG if it is on a different leaf. You could reuse the same VLAN in different EPGs on the same leaf with a feature called VLAN scope "local" or per Port VLAN, but it must be on different BDs.

Guidance for Application-centric per Port VLAN design:

![](/images/aciadvanced/aciadvanced_3_4_7.png)

#   4)  Describing Cisco ACI Service Graph PBR

##  4.1)    Introduction

Cisco Application-Centric Infrastructure (ACI) technology provides the capability to insert Layer 4 through Layer 7 service nodes using a service graph. One of the main features of the service graph is the Policy-Based Redirect (PBR).

You will learn about the concept of service graphs with PBR and when to use it in a Cisco ACI deployment and go through the configuration process of service graphs with PBR. While exploring the communication between endpoints when the service graph with PBR is utilized in Cisco ACI fabric, you will understand the PBR end-to-end packet flow, and supported topologies while implementing PBR traffic redirection. Since the PBR influences the packet forwarding process, an unavailable PBR node can introduce traffic interruption in the fabric, and thus you will learn how to utilize various options for tracking to prevent redirection of traffic to a PBR node that is down.

##  4.2)    Service Graph PBR overview

As in traditional network designs, in the Cisco ACI fabric the traffic is routed and bridged based on the destination IP and MAC addresses. You can use service graphs with PBR to redirect traffic and send packets to service nodes connected to the fabric, such as firewall, Cisco Intrusion Prevention System (IPS), or load balancer, while overriding the information available in the forwarding table (endpoint table and RIB).

The following figure illustrates the difference of when ACI routing is used for the east-west traffic between hosts in two endpoint groups (EPGs) as a part of different bridge domains (before service graph is deployed) and traffic redirection with service graph with PBR:

![](/images/aciadvanced/aciadvanced_4_2_1.png)

Before the service graph with PBR is deployed, the Cisco ACI relies on routing in the fabric itself to forward the traffic from the client in the EPG client to the server in EPG web, and allows the traffic that is based on the contract. The default gateway of the client and server is the bridge domain IP address, where these endpoints are deployed. When the service graph with PBR is used, even though the forwarding table points to the destination endpoint directly, the traffic is redirected to the service node (such as firewall) based on the contract the traffic is hitting. In the contract with the PBR service graph, the traffic redirection is defined towards the service node, which operates in Layer 3 mode and is integrated as service node in the Cisco ACI fabric. It routes and inspects the traffic between the client and server, which is deployed in different EPGs.

A service node can be integrated in Cisco ACI using a service graph without PBR. However, without PBR, the packet flow is still based on the forwarding table, so you must ensure that the forwarding table points to the inserted service node without any redirection, which is typically performed by using virtual routing and forwarding (VRF) stitching (or VRF sandwich in other words).

### PBR with a Service Node in Layer 3 Mode

This figure shows the two service graph options for the insertion of a firewall that protects east-west traffic flows between endpoints in the EPG Client and EPG Web. It illustrates the difference between a classic VRF sandwich design (service graph without PBR), and service graph with PBR, using Layer 4 to Layer 7 device deployed in routed mode (referred as Go-To mode).

![](/images/aciadvanced/aciadvanced_4_2_2.png)

The design that uses service graph without PBR requires multiple VRFs (a classic VRF sandwich configuration) and Layer 3 outside peering, which are established between the fabric and the internal and external firewall interfaces. The traffic between the client and web server goes through the firewall, which is a routed Layer 3 hop that has one interface in VRF1 and one interface in VRF 2 instance. The VRF2 encompasses the web subnet and the IP subnet of the firewall internal interface. The firewall outside interface and the Layer 3 interface towards the client are part of a separate VRF1 instance.

The use of service graph with PBR significantly simplifies the configuration using a single VRF, because the previously described VRF sandwich configuration is not required anymore. The traffic instead is redirected to the service node based on the PBR policy. In addition, you can implement selective traffic redirection (using the subjects and filters in the contract), which is an advantage when compared to service graph without PBR.

The following figure depicts three deployment options for insertion of a perimeter firewall in routed mode, which protects north-south traffic flows between the external Layer 3 network and the web EPG:

![](/images/aciadvanced/aciadvanced_4_2_3.png)

The first option uses the firewall as a gateway for the server part of the web EPG. It is a simple design, but it does not take advantage of the Cisco ACI anycast gateway feature. The service node is in bridge domain, and all intersubnet traffic goes through the firewall, which consumes firewall resources.

The second option uses a classic VRF sandwich configuration and the Cisco ACI fabric as a gateway. In this design, not all intersubnet traffic needs to go through the firewall, as it requires additional configurations, such as VRFs and L3Outs to force traffic through the firewall. This design can be beneficial for north-south traffic flows, since you can force all traffic to go through the routed firewall. The web subnet and the IP subnet of the firewall internal interface are associated with the VRF2 instance. The firewall outside interface and the Layer 3 interface facing the WAN edge router are instead part of the VRF1 instance.

The third option uses a service graph with PBR, which can perform selective traffic redirection, using the Cisco ACI fabric as a gateway. The service node interfaces are in bridge domains, while the north-south traffic is forced through the firewall. As shown in the previous example for the east-west firewall in routed mode, the usage of service graph with PBR can be also beneficial for east-west traffic flows, since it does not require multiple VRFs and selective traffic redirection can be easily applied with the service graph PBR.

### PBR with a Service Node in L1/L2 Mode

The following figure depicts two service graph options for an insertion of a transparent perimeter firewall, which protects north-south traffic flows between the external Layer 3 network and the web EPG. It depicts the difference between a classical bridge domain sandwich design (service graph without PBR) and service graph with PBR using Layer 4 to Layer 7, device that is deployed in transparent (Go-Through) mode.

![](/images/aciadvanced/aciadvanced_4_2_4.png)

In the bridge domain “sandwich” design, the default gateway for the server part of the web EPG is deployed in BD1 (pervasive gateway 192.168.1.254). Since, this bridge domain is different from the one in which the web server is located, it forces all traffic that is destined for external destinations from EPG Web to traverse the transparent firewall. Then, the Cisco ACI fabric performs the Layer 3 functionality in BD1.

In the Layer 1/Layer 2 PBR design, the inside interface of the service node is in separate bridge domain from the web server. Unicast routing is enabled for BD2 and BD3, without configured bridge domain IP subnet. The Cisco ACI fabric performs the Layer 3 functionality, while the Layer 1/Layer 2 PBR steers the traffic through the transparent firewall. You can use selective traffic redirection, which is the same advantage as using Layer 3 PBR.

### Service Graph PBR Overview Summary

As indicated in these examples, you can use service graph PBR between EPGs and even between Layer 3 outside EPGs. The service nodes can be both physical and virtual service appliances. In addition, you can use PBR with both bidirectional and unidirectional contracts. Also, when needed, the traffic load can be distributed across multiple Layer 4 to Layer 7 devices (symmetric PBR).

Cisco ACI service graph with PBR can be used in different scenarios while inserting service nodes, including the following:

*   Insertion of firewalls or load balancers in the path between endpoints, while keeping the default gateway on the Cisco ACI fabric.
*   Insertion of a Layer 4 to Layer 7 device in the path between endpoints that are in the same subnet.
*   Selectively separating traffic that is based on protocol and port filtering that is sent to Layer 4 to Layer 7 devices.
*   Using symmetric PBR to horizontally scale the performance of Layer 4 to Layer 7 devices.

### Service Graph PBR Configuration Overview

The PBR redirection configuration in Cisco ACI is almost the same as a normal service graph configuration. The difference is that you need to create and apply the L4-L7 PBR policy to define how to redirect the traffic on top of the normal Service Graph configuration. The configuration items include creation of the Layer 4 Layer 7 service device, a service graph template, Layer 4 to Layer 7 PBR policy for the consumer/provider connector, and application of the service graph template to the EPGs with a contract along with the PBR policies.

More precisely, the service graph with PBR configuration procedure using the Cisco APIC user interface, which redirect the traffic to a service node in Layer tri-mode, follows these summary steps:

1.  On the menu bar, choose **Tenants** > tenant_name.
1.  In the Navigation pane of the chosen tenant, choose **Services > L4-L7 > Devices**
1.  In the Work pane, choose **Actions > Create L4-L7 Devices.**
1.  In the **Create L4-L7 Devices** dialog box, complete the fields as required, such as **Name** and **Service Type**, which can be **Firewall** or **ADC** in the General section, create external and internal concrete interfaces and port connectivity on the corresponding leaf switches, and so on.
1.  In the Navigation pane, choose **Tenant** > tenant_name > **Services > L4-L7 > Service Graph Templates.**
1.  In the Work pane, choose **Action > Create L4-L7 Service Graph Template.**
1.  In the **Create L4-L7 Service Graph Template** dialog box, perform the following actions:
    1.  In the **Service Graph Name** field, enter a name for the service graph template
    1.  For the **Graph Type** radio buttons, click **New Graph**.
    1.  Drag and drop the device that you created from the **Device Clusters** pane to between the consumer EPG and provider EPG. By drag and dropping, you create the service node.
    1.  For the **Firewall** radio buttons, click **Routed.** (**Note:** Choose the **Transparent** option from the Firewall radio buttons, when the inserted firewall performs transparent inspection of the redirected traffic such as Layer 1/Layer 2 PBR.)
    1.  In the Profile drop-down list, select a function profile appropriate to the device. If no profiles exist, create one.
    1.  Check the **Routed Redirect** check box and click **Submit.**
1.  In the Navigation pane, choose **Tenant** > tenant_name > **Policies > Protocol Policies > L4-L7 Policy-Based Redirect.**
1.  In the Work pane, choose **Action > Create L4-L7 Policy-Based Redirect** to create PBR policy for the consumer connector.
1.  In the **Create L4-L7 Policy-Based Redirect** dialog box, complete the fields as required, including **L3 destinations** to specify packet rewrite information, so the leaf node will know to which destination MAC address it should send the packet during traffic redirect.
1.  Create another PBR policy for the provider connector.
1.  In the Navigation pane, choose **Tenant** > tenant_name > **Services > L4-L7 > Service Graph Templates** > service_graph_template_name and choose the service graph template that you just created.
1.  Right-click the service graph template and choose **Apply L4-L7 Service Graph Template.**
1.  In the **Apply L4-L7 Service Graph Template to EPGs** dialog box, perform the following actions:
    1.  In the **Consumer EPG/External Network** drop-down list, choose the consumer EPG.
    1.  In the **Provider EPG/External Network** drop-down list, choose the provider EPG.
    1.  For the **Contract Type** radio buttons, click **New Contract.**
    1.  In the **Contract Name** field, enter a name for the contract.
    1.  Uncheck the **No Filter (Allow All Traffic)** check box.
    1.  On the **Filter Entries** table, click (+) to add an entry, specifying "IP" for the **Name** and choose **IP** for the **Ether Type**, click **Update**, and then click **Next** to finish step 1 and move to step 2.
    1.  For the Consumer Connector **BD** drop-down list, choose the bridge domain that connects to the consumer EPG. The bridge domain must have IP learning disabled.
    1.  For the Consumer Connector **Redirect Policy** drop-down list, choose the redirect policy that you created for the consumer connector.
    1.  For the Consumer Connector **Cluster Interface** drop-down list, choose the consumer cluster interface.
    1.  For the Provider Connector **BD** drop-down list, choose the internal bridge domain that connects to the provider EPG. The bridge domain must have IP learning disabled.
    1.  For the Provider Connector **Redirect Policy** drop-down list, choose the redirect policy that you created for the provider connector.
    1.  For the Provider Connector **Cluster Interface** drop-down list, choose the provider cluster interface, and click **Finish.**

The proceeding figure shows step 14-g to 14-l from the procedure above, indicating the **Apply L4-L7 Service Graph Template to EPGs** dialog box in the Cisco APIC user interface, for definition of the service graph:

![](/images/aciadvanced/aciadvanced_4_2_5.png)

This dialog box enables you to choose the bridge domains for the consumer and provider connector, and the PBR policies that will be used for both connectors.

### Recapitulation for Shadow Service EPG

The Cisco ACI service graph rendering involves allocation of the necessary VLANs between the L4-L7 device and the bridge domains. Cisco ACI creates so called shadow service EPGs to which the L4-L7 device connects, and it creates internal contracts to enable communication to and from the L4-L7 device.

![](/images/aciadvanced/aciadvanced_4_2_6.png)

##  4.3)    PBR end-to-end Packet Flow

When you use service graph with PBR in Cisco ACI, you influence the traffic flow in the fabric, while redirecting the traffic to the PBR node.

Depending on the PBR design, several traffic flows are possible. These figures depict the east-west traffic flow between endpoints in EPG client and EPG web, when a firewall service node in Layer 3 mode is integrated in Cisco ACI fabric using a service graph with PBR.

![](/images/aciadvanced/aciadvanced_4_3_1.png)

In this example, the EPG client is a consumer EPG and the EPG web is a provider EPG, using a contract with service graph PBR. The generated traffic from the client endpoint that is destined for the web endpoint follows this sequence:

1.  Endpoint client, which is connected to Leaf 1, sends traffic to the web.
1.  Assuming that Leaf 1 has not learned the destination endpoint, Leaf 1 forwards the traffic to the spine proxy. Currently, Leaf1 cannot resolve the destination EPG class ID (pcTag) either to apply a contract with PBR policy.
1.  The spine node forwards the traffic to Leaf 3, to which the destination web endpoint is connected. In addition, Leaf 3 learns the source endpoint (client) information from this traffic and populates its endpoint table.
1.  Since Leaf 3 can resolve both, the source and destination EPG class IDs, the contract with the PBR service graph is invoked, which results with PBR traffic redirection on Leaf 3. In this process, the destination MAC address is rewritten to the PBR node MAC address (MAC Ext of the firewall external interface) on the consumer side. Leaf 3 looks up the PBR node MAC Ext in the PBR BD. Since Leaf 3 does not know where this destination MAC address is connected, the traffic goes to the spine proxy, which forwards the traffic to Leaf 2, to which the PBR node is connected. Hence, the traffic is forwarded to the external interface of the PBR node for inspection and routing. Leaf 2 does not learn the client IP address from this traffic like Leaf 3 did, because IP data-plane learning is disabled for the PBR node bridge domain.
![](/images/aciadvanced/aciadvanced_4_3_2.png)

1. The traffic from the PBR node that is destined for the web endpoint follows this sequence:
1.  Traffic is routed on the PBR node that is based on the routing table of the PBR node, and traffic returns to the Cisco ACI fabric from the PBR BD.
1.  Because Leaf 2 does not know the destination endpoint, the traffic goes to the spine proxy again and then to Leaf 3. The source EPG is the shadow service EPG that is created in the background for the PBR node provider connector, and the destination is the provider EPG. This traffic is allowed by a contract that is automatically created for the shadow service EPG of the provider connector and the provider EPG. This shadow service EPG and contract are the basic service graph behavior regardless of PBR. Also, please note that Leaf 3 does not learn the client IP address from this traffic, because IP data-plane learning is disabled for the PBR node bridge domain.
![](/images/aciadvanced/aciadvanced_4_3_3.png)
1.  The returning traffic, from the web to client, follows this sequence:
1.  The endpoint web sends returning traffic to the client.
1.  Since Leaf 3 can resolve both, the source and destination EPG class IDs, the contract with PBR policy is applied and PBR traffic redirection is performed on Leaf 3. The destination MAC address is rewritten to the appropriate PBR node MAC address (MAC Int of the firewall internal interface) in the PBR BD, and the traffic goes to the PBR node on the provider side.
![](/images/aciadvanced/aciadvanced_4_3_4.png)
1.  The traffic from the PBR node to the client follows this sequence:
1.  Traffic is routed on the PBR node, and the traffic returns to the Cisco ACI fabric from the consumer side of the PBR node in the PBR BD.
1.  Because Leaf 2 does not know the destination endpoint, the traffic goes to the spine proxy again and then to Leaf 1. Leaf 1 performs policy enforcement, and the traffic is permitted because the source EPG is the shadow service EPG of the PBR node consumer connector, and the destination is the consumer EPG. Leaf 1 does not learn the web endpoint IP address from this traffic, because IP data-plane learning for the PBR node bridge domain is disabled.

The rest of the traffic will also be redirected on Leaf 3 because Leaf 1 does not learn the web endpoint IP address. Cisco ACI enforces policies depending on whether both source and destination class IDs can be determined, which depends on the traffic flow. Similarly, when the traffic is generated from the web endpoint to the client first, or if other traffic lets Leaf 1 learn the web endpoint IP address, PBR policy can be performed on Leaf 1.

### IP Data-Plane Learning for Service Graph PBR

When you deploy a service graph with PBR, the Layer 4 to Layer 7 device (PBR node) must be connected to a bridge domain. If you are using a Cisco APIC version prior to Cisco APIC release 3.1, you must manually disable the IP data-plane learning for the PBR node bridge domain (that contains a PBR node interface), so the leaf nodes involved in the PBR traffic flow, may not experience unwanted endpoint learning behavior.

For example, since the traffic flows through the firewall, the provider leaf node should not learn the IP address of the endpoint in the consumer EPG through the service node leaf VXLAN tunnel endpoint (VTEP) IP address, since this endpoint is actually under a different leaf node. The same behavior applies for the returning traffic, as depicted in this figure:

![](/images/aciadvanced/aciadvanced_4_3_5.png)

Starting with Cisco APIC release 3.1, if you use the Cisco Nexus 9300-EX and Cisco Nexus 9300-FX platform leaf switches, it is not necessary to manually disable the IP data-plane learning on PBR bridge domains. The IP data-plane learning setting on the PBR node EPG is automatically disabled during service graph instantiation, and not to the entire PBR node bridge domain.

If you need to disable (or verify current settings) the IP data-plane learning for the bridge domains that are used for the PBR service node, navigate to **Tenants** > tenant_name > **Networking > Bridge Domains** in the Cisco APIC user interface and choose the policy of the referred PBR node bridge domain, as shown in this figure:

![](/images/aciadvanced/aciadvanced_4_3_6.png)

**Note:** This IP data-plane learning under the BD is specifically for PBR except for some corner scenarios. In case you need to disable IP data-plane learning in the ACI fabric, please use IP data-plane learning knob under the VRF.

##  4.4)    Service Graph PBR Requirements and Topologies

The insertion of service nodes in Cisco ACI without PBR typically requires complicated logical topology design such as VRF stitching, so the forwarding table (endpoint table and routing table) points to the service node even when the destination IP/MAC of the packet is not the service node itself. Service graph with PBR simplifies service device insertion and removal. Still, there are several requirements and design considerations that you must consider when using Cisco ACI service graph with PBR, and supported topologies that can be utilized.

### Service Graph PBR Requirements

When implementing Cisco ACI service graph with PBR you should consider the requirements and design considerations that are applicable to both Layer 1/Layer 2 PBR and Layer 3 PBR, include the following:

*   The Cisco ACI fabric must be the gateway for the endpoints and for the PBR node.
*   The PBR node bridge domain must belong to the same VRF instance as either the consumer bridge domain (EPG) or provider bridge domain (EPG).
*   Multicast and broadcast traffic redirections are not supported, because the contract is applied to unicast traffic only.
*   PBR is supposed to be used for IP version 4 (IPv4) and IP version 6 (IPv6) traffic. Thus, a common default filter (Permit All) that includes Address Resolution Protocol (ARP), Ethernet traffic, and other non-IP traffic should not be used for PBR. For example, when configuring PBR between EPGs that are in the same bridge domain, you should not use a Permit All contract filter, because this filter would also redirect ARP traffic to the Layer 4 to Layer 7 device.
*   Multinode PBR is supported. The Layer 1/Layer 2/Layer 3 service device can be mixed in a service graph.
*   TCAM policy compression (Enable Policy Compression, formerly known as no stats option in the contract filter) does not take effect on a zoning rule with a redirect rule.

The main requirements for the Cisco ACI PBR with inserted service node in routed mode (Layer 3 PBR) are as follows:

*   The Layer 4 to Layer 7 device must be deployed in Go-To mode (routed mode).
*   Since Cisco APIC release 3.1(x), it is not mandatory to provision service node in a separate bridge domain than the consumer or provider bridge domain (supported on Cisco Nexus 9300-EX and Cisco Nexus 9300-FX platform leaf switches).
*   IP data-plane learning should be disabled in the PBR node bridge domain. For releases later than Cisco APIC release 3.1 with Cisco Nexus 9300-EX and -FX platform (or later) leaf switches, data-plane learning is automatically disabled in the PBR node EPG during service graph deployment.
*   The administrator must enter the destination of redirection (the IP addresses and MAC addresses of the PBR node interfaces) in the PBR “Layer 3 Destinations” configuration from the Cisco APIC.
*   Symmetric PBR (more than one PBR destination per PBR policy) requires Cisco Nexus 9300-EX and -FX platform (or later) leaf switches.
*   Some designs and topologies are supported from specific Cisco APIC releases and above, such as PBR usage for more than one node in a service graph and service graph PBR with a contract and vzAny as provider since Cisco APIC release 3.2, service graph PBR with an intra-EPG contract since Cisco APIC release 4.0, and so on.

The main requirements for Cisco ACI with Layer 1/Layer 2 mode device (Layer 1/Layer 2 PBR) are as follows:

*   Layer 1/Layer 2 PBR requires Cisco Nexus 9300-EX and -FX platform (or later) leaf switches.
*   The Layer 4 to Layer 7 device must be deployed as Layer 1/Layer 2 mode in physical domain (VMM domain is not supported).
*   Layer 1/Layer 2 PBR node interfaces must be in a dedicated bridge domain that cannot be shared with other endpoints. These interfaces also cannot be in a Layer 3 outside.
*   Only active/standby deployment mode of the service node is supported. Unlike Layer 3 PBR, you cannot deploy service nodes in active/active high-availability mode. In addition, PBR node tracking is mandatory when service nodes are in active/standby mode.
*   Layer 1/Layer 2 device must have two interfaces connected to the Cisco ACI fabric (two-arm mode), while the consumer and provider connectors of the Layer 1/Layer 2 device must be in different bridge domains.
*   Layer 1/Layer 2 PBR is supported with unmanaged mode service graph only.
*   PBR with vzAny or intra-EPG contract is not supported, since these options require service node that is connected with one interface to the Cisco ACI fabric (one-arm mode).

### Service Graph PBR Topologies

Supported Cisco ACI service graph with PBR configurations in the same VRF instance include the following:

![](/images/aciadvanced/aciadvanced_4_4_1.png)

These examples illustrate some of the supported topologies when service node is integrated with the Cisco ACI fabric for the east-west traffic flows between EPG Client (consumer) and EPG Web (provider).

The first example shows the typical use case of one-node firewall insertion, which is in Layer 3 mode, with dedicated bridge domains for PBR node interfaces (mandatory prior to Cisco APIC release 3.1). Hence, the consumer and provider bridge domain, containing the consumer or provider EPG from the contract with service graph PBR, are different from the PBR node bridge domains.

The second and third examples are two-node service graphs. Prior to Cisco APIC release 3.2, if you have a two-node service graph, either the first node or the second node can be a PBR node, as shown in the examples. Since this release, a multinode PBR can be utilized, which enables you to use PBR multiple times in a same service graph. In addition, a non-PBR node can be in the same bridge domain as the consumer or provider EPG.

As indicated, some of the supported topologies depend on the Cisco APIC release, due to introduced new support in those releases. The following figure illustrates supported topologies for service graph PBR depending on Cisco APIC release, in the same VRF instance:

![](/images/aciadvanced/aciadvanced_4_4_2.png)

Starting from Cisco APIC release 3.1, you do not need a dedicated service bridge domain for Layer 3 PBR, so in the first example, the PBR node bridge domains (for each firewall interface) are the same as the consumer or provider bridge domain. Hence, you can use this simplified topology while inserting a firewall in Layer 3 mode, with nondedicated service bridge domain, inspecting the east-west traffic in Cisco ACI fabric.

Starting from Cisco APIC release 4.1, PBR can be configured to redirect traffic to a Layer 4 to Layer 7 device that is configured in Layer 1/Layer 2 device mode as well, which is shown in the second example in the figure above. For a Layer 2 PBR node you can use a firewall in transparent mode, IPS device, and so on. The PBR node bridge domains must have IP routing that is enabled, but no bridge domain subnet is required. Similarly, to Layer 3 PBR, data-plane IP learning should be disabled. Although in this example, the consumer and provider EPGs are in different bridge domains, they can be in the same bridge domain and the same bridge domain subnet. This flexibility is one of the advantages of Cisco ACI Layer 1/Layer 2 PBR.

The last example is unidirectional PBR with the other connector in Layer 3 outside. Prior to Cisco APIC release 4.1.2, both consumer and provider connectors of a PBR node must be in a bridge domain and not in a Layer 3 outside, even though PBR is only enabled on one of the connectors. Starting from Cisco APIC release 4.1.2, you can use Layer 3 outside for a connector where PBR is not enabled.

The Cisco ACI service graph with PBR simplifies the design and does not need usage of multiple VRFs with stitching to steer the traffic flow towards the inserted Layer 3 service node. So, the PBR node can be in one VRF instance, but can also be between VRF instances. Still, you cannot put the PBR node in a VRF instance, which is neither a consumer nor a provider VRF instance. The following figure shows the supported topologies with two VRF instances (VRF sandwich design):

![](/images/aciadvanced/aciadvanced_4_4_3.png)

In an inter-VRF design, the EPG subnet must be leaked to the other VRF instance to derive the EPG class ID. To leak the subnet to another VRF instance, the subnet scope must use the Shared between VRFs option. Thus, the subnet under the bridge domain for the consumer EPG and the subnet under the provider EPG must be configured with the Shared between VRFs setting, which is the same as normal VRF route leaking configuration. So, in the first example for an inter-VRF contract in the figure above, the provider and consumer routes are leaked between VRF instances. More precisely, VRF1 must contain provider EPG subnet 192.168.2.0/24 that is leaked from VRF2, and VRF2 must contain consumer EPG subnet 192.168.1.0/24 that is leaked from VRF1.

In the second example, the PBR node subnet needs to be leaked to the consumer VRF instance. Thus, the 172.16.1.0/24 subnet in which the PBR node consumer side is located must be leaked to VRF1. The third examples show a design where the PBR node subnet on the provider side is leaked to the provider VRF instance.

PBR can redirect traffic even if the endpoints are in the same bridge domain (same subnet). Still, the PBR node must be in a different bridge domain. In addition, since Cisco APIC release 4.0, PBR with an intra-EPG contract is supported, which is helpful for inserting service nodes for traffic between endpoints in the same EPG. The following figure illustrates these designs, when the consumer and provider EPGs are in the same subnet, and PBR implementation in intra-EPG contract:

![](/images/aciadvanced/aciadvanced_4_4_4.png)

To implement these designs, you should use a one-arm mode for the service node. In the example shown in the figure above for traffic inspection between endpoints in same subnet, even though the Web and App EPGs are in the same bridge domain and the same subnet, PBR can be enforced. This design requires the use of the same interface on the PBR node, unless the PBR node has a more specific static route.

The example in the figure with an intra-EPG contract uses a contract that is applied to endpoints in the same EPG. This design is useful if you need security enforcement even within an EPG, while the traffic is redirected to the integrated service node.

Cisco ACI service graph with PBR can be used with vzAny, which is useful if you have a security requirement that needs to be applied to all EPGs in the same VRF. Prior to Cisco APIC release 3.2, you could only associate a service graph with PBR with a contract with vzAny as a consumer. It is helpful for inserting service nodes for traffic between common providers and all EPGs as consumers in a VRF.

Since Cisco APIC release 3.2, you can use service graph with PBR with a contract with vzAny as provider, which is helpful for inserting service nodes everywhere, all EPGs to all EPGs, in a VRF.

This figure shows the Cisco ACI PBR with vzAny as consumer only, and PBR with vzAny as both consumer and provider design:

![](/images/aciadvanced/aciadvanced_4_4_5.png)

The example with a contract with PBR between vzAny as consumer and a Network File System (NFS) EPG as provider in the VRF1, the NFS access from all endpoints in VRF1 to NFS can be inspected by the firewall. It also benefits from the vzAny feature to reduce policy TCAM consumption for the multiple consumer EPGs. In addition, if you use vzAny as both consumer and provider for a contract with service graph PBR, all traffic between endpoints (in different EPGs) within the VRF can be inspected by the firewall.

### Service Graph PBR Unsupported Topologies

Based on the Layer 1/Layer 2 PBR and Layer 3 PBR requirements, some topologies when implementing Cisco ACI service graph with PBR are not supported. The following figure depicts examples of unsupported service graph PBR topologies:

![](/images/aciadvanced/aciadvanced_4_4_6.png)

Since the PBR node must be in a Layer 3 bridge domain, the first example in the figure above shows an unsupported topology, in which the PBR node interfaces are connected to Layer 2 bridge domain (without assigned IP subnet). The second and the third examples show usage of a service graph with a combination of PBR (load balancer) and non-PBR nodes (firewall in Go-To or Go-Through mode), as unsupported topologies. Thus, the PBR node cannot be in the same bridge domain as the next service node.

Since Cisco APIC release, 4.1.2 Layer 3 outside can be used for a connector where PBR is not enabled. Still, not all topologies with Layer 3 outside are supported. This figure illustrates the service graph PBR unsupported topologies, when Layer 3 outside is utilized.

![](/images/aciadvanced/aciadvanced_4_4_7.png)

The first example in the figure above, shows that Layer 3 outside cannot be used for a connector where PBR is enabled. So PBR cannot be performed, and traffic can be destined for a service node that is based on the routing table. The second example shows that the support, which was added in the APIC release 4.1(2), for using a L3Out on the PBR node interface is only on the provider connector of the last PBR node in the service graph when the PBR policy is applied only on the consumer connector.

### Symmetric PBR

Cisco ACI PBR in can load-balance traffic to more than just one PBR destination in the PBR policy. For example, if you have four PBR destinations, which IP and MAC address pairs are configured in the PBR policy, the traffic is redirected to one of the four PBR nodes that are based on hashing. The hash tuple is the source IP address, destination IP address, and protocol type by default. Because Layer 4 to Layer 7 devices keeps track of the connections through the PBR node, they must see both directions of a flow. Therefore, you need to make sure that incoming and return traffic is redirected to the same PBR node, which the symmetric PBR feature facilitates in Cisco ACI fabric.

Since Cisco APIC release 2.2(3j) and 3.1, the hash tuple that is used for load-balance is user configurable. You can use the source IP address only; the destination IP address only; or a combination of the source IP address, destination IP address, and protocol type (default). If you use the source IP address only or the destination IP address only option, you need to configure options for both directions to keep traffic symmetric. For example, if you use the source IP address only option for incoming traffic, you must use the destination IP address only option for returning traffic to keep traffic symmetric, as depicted in this figure:

![](/images/aciadvanced/aciadvanced_4_4_8.png)

In this example for symmetric PBR, the incoming traffic from each of the four different users always goes through the same PBR node using the source IP only option, while the destination IP only option ensures that the returning traffic is kept symmetric for each user flow.

##  4.5)    Service Graph PBR Tracking Options

Since Cisco APIC release 2.2(3j) and going forward with Cisco APIC release 3.1(1) (not supported in Cisco APIC release 3.0(x)), a PBR node tracking feature was introduced, which enables you to prevent redirection of traffic to a PBR node that is down. If a PBR node goes down/fails, a specific mechanism, referred as hashing is used, so existing traffic flows are redirected to another node. This feature requires Cisco Nexus 9300-EX or -FX platform (or later) leaf switches.

The following figure shows how PBR node tracking functions:

![](/images/aciadvanced/aciadvanced_4_5_1.png)

The tracking option follows this sequence:

1.  The service leaf node to which the PBR node is connected periodically sends keepalive messages, using Internet Control Message Protocol (ICMP), or Transmission Control Protocol (TCP) information to the local PBR node.
1.  Then, the service leaf node periodically announces availability information to all the other leaf switches through a systemwide broadcast message. This information allows all the leaf nodes to know whether they can still use that specific PBR node when applying the PBR policy locally.

### Health Groups

The PBR service node may be unusable for different reasons. For example, the PBR node can be unreachable as a device, or only the consumer or the provider connector of the PBR node can be down. To prevent traffic from being black-holed due to failed interface for the consumer or provider connector, Cisco ACI must avoid using the PBR node for traffic in both directions. Some Layer 4 to Layer 7 devices can bring down an interface if another interface is down or even failover to a backup service node, so you can use this capability on the Layer 4 to Layer 7 device to avoid black-holing. However, if the Layer 4 to Layer 7 device does not have this capability, you should use the Cisco ACI health group feature to completely avoid using the PBR node, if either the consumer or provider connector is down.

You can use each PBR destination IP and MAC address in a health group. The following figure depicts an example with two PBR nodes that are integrated in a Cisco ACI fabric, while the IP addresses configured on their external and internal interfaces are assigned to two health groups.

![](/images/aciadvanced/aciadvanced_4_5_2.png)

In this example, there are two PBR node destinations, which are deployed in two-arm mode. The external and internal interface of the PBR node should be in the same health group, to disable the remaining interface if single interface fails. Hence, FW1 has 172.16.1.1 as the consumer connector and 172.16.2.1 as the provider connector, and are in Health-group 1. FW2 has 172.16.1.2 as the consumer connector and 172.16.2.2 as the provider connector, and are in Health-group 2. If either of the PBR destinations in the same health group is down, that node will not be used for PBR, which avoids black-holing if this node is further utilized.

### Threshold

You must make sure that a Layer 4 to Layer 7 device does not become a bottleneck in the network, and that you have enough of available Layer 4 to Layer 7 devices to handle the traffic. If the utilized PBR nodes introduce bottleneck, the PBR action should be automatically disabled.

To determine whether PBR should or should not be enabled, PBR tracking offers configurable minimum and maximum threshold values that are based on the percentage of available PBR destinations in a PBR policy. If the number of available PBR destinations falls below the minimum percentage, the traffic is permitted or dropped rather than redirected, based on the configured down action, such as permit, deny, and bypass. To revert to the original state and redirect the traffic again, the number of available PBR destinations must reach the maximum percentage.

This figure illustrates an example with five PBR destinations with the threshold feature enabled, including 20 percent set as the minimum percentage and 80 percent set as the maximum percentage.

Initially, all the PBR destinations are up and the traffic is load-balanced across all PBR nodes (1 through 5). If PBR nodes 1 through 4 become nonoperational (down), PBR is disabled because the percentage is lower than or equal 20 percent. Even if PBR node 4 comes up again and the percentage of available nodes rises to 40 percent, PBR still is disabled because it is still lower than 80 percent. When PBR node 2 through 5 are up, PBR is enabled again, because the percentage is higher or equal to 80 percent

When the number of available PBR destinations in the PBR policy falls below the configured minimum percentage as the threshold, a threshold down action is invoked, which can be:

Threshold Down Action Options

| Down Action      | Behavior                                                     | Use Case                                                     |
|------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| Permit (default) | Traffic directly goes to destination without PBR.            | Skip over optional service node in one node service graph.   |
| deny             | Traffic is dropped.                                          | Mandate service insertion.                                   |
| Bypass           | Traffic is redirected to next PBR node in the service graph. | Skip over optional service node in multinodes service graph. |

The permit option in the down action was available since this feature was introduced in Cisco APIC release 2.2(3j). The deny option was introduced in Cisco APIC release 3.1, while the bypass option is available since Cisco API release 4.1.2.

When permit is used, which is the default down action, the traffic will be permitted between endpoints when the PBR node is down. The use cases for down action permit include scenarios in which PBR is used for a traffic optimizer or an optional service node that can be skipped, rather than having traffic dropped.

If you set the down action to deny, traffic will be dropped between endpoints. Some use cases for down action deny are PBR for a firewall, IPS, or security service node that must be always included, so the traffic should not pass uninspected.

The bypass option can be beneficial, when you are using a multinode PBR service graph. For example, if you are using a 2-node service graph that has a first function node and a second function node. Each function node can have one or more PBR destinations in a PBR policy, as illustrated in this figure:

![](/images/aciadvanced/aciadvanced_4_5_3.png)

If the number of available PBR destinations in the PBR policy for the first group falls below the minimum percentage, the traffic is redirected to one of the available PBR destinations in the PBR policy for the second group as a backup path, instead of having traffic that is dropped or permitted directly to the destination. However, if the number of available PBR destinations in the PBR policy for the second group also falls below the minimum percentage, the traffic is also bypassed in the second group, which means that traffic is permitted directly to the destination.

To benefit from the threshold down action, you have to enable tracking and threshold. Also, you should use the same down action on both the provider and consumer connectors of a given PBR node. If you do not configure the down action this way, Cisco APIC raises a fault under the tenant when deploying the service graph.

### Resilient Hashing

With symmetric PBR, incoming and returned traffic should be redirected to the same PBR node.

If one of the PBR nodes in a PBR policy is down, and PBR is still enabled, by default the traffic will be rehashed by using the other PBR nodes in the PBR policy, which are still available. As a result, some traffic that has been going through the available PBR nodes could be load-balanced to different PBR nodes and could be affected, even though they have not been going through the failed PBR node, as depicted in this figure.

![](/images/aciadvanced/aciadvanced_4_5_4.png)

This example shows four distinct traffic flows from different users, which are redirected to different PBR nodes. When one of the PBR nodes fails, the traffic will be redirected to other available PRB node. By default, without resilient hashing, existing flow sessions going through available PBR nodes could also be affected due to rehashing and some of them may be load-balanced to different PBR nodes, which do not have existing flow session information, which could be critical for stateful L4-L7 services.

With the resilient hashing PBR feature, which was introduced in Cisco APIC release 3.2, only the traffic that went through a failed node will be redirected to a different available PBR node. Other traffic will still be redirected to the same node as shown in this figure, so that the traffic going through other PBR nodes will not be impacted.

![](/images/aciadvanced/aciadvanced_4_5_5.png)

In the same example with four distinct traffic flows from different users, when one of the PBR nodes fails, the traffic that went through the failed node will be redirected to one of the available PBR nodes. It is a trade-off between resiliency and load-balancing distribution, since it is not redistributed to multiple available PBR nodes. If the capacity of PBR node during PBR node failure is a concern, you can use backup PBR node using a backup PBR policy, which was introduced in Cisco ACI release 4.2, to take care of the traffic that went through the failed node.

If there are multiple failures, the traffic going through the failed PBR nodes can be rehashed to the available ones. The following examples illustrate the traffic redistribution with resilient hashing when three PBR nodes fail sequentially, in a policy that uses six PBR nodes:

![](/images/aciadvanced/aciadvanced_4_5_6.png)

When PBR node A goes down, and node B goes down, and then node D goes down, traffic flows marked as 3, 5, and 6, are hashed to C, E, or F, without being impacted by this multiple PBR nodes failure.

### PBR Node Tracking Configuration

While enabling tracking on each Layer 4 to Layer 7 PBR node, you should use the same settings for the consumer and provider redirect policy. To configure the parameters for the PBR node tracking, navigate to choose Tenant > tenant_name > Policies > Protocol Policies > L4-L7 Policy-Based Redirect in the Cisco APIC user interface and specify the desired action for both the consumer and provider policies, as shown in this figure:

![](/images/aciadvanced/aciadvanced_4_5_7.png)

In the Layer 4 to Layer 7 redirect policy, you can configure the tracking features, such as select IP Service-Level Agreement (SLA) monitoring policy, which was previously created or can be created during the selection, enabled threshold and specify minimum and maximum threshold percentage, as well as chose the desired down action. You should use the same action on both the consumer and provider connectors of the PBR node, or otherwise, a fault is raised under the tenant even though the service graph is rendered.

Within the chosen Layer 4 to Layer 7 redirect policy, you can specify the hashing algorithm (the default setting is sip-dip-protocoltype) and enabled resilient hashing.

The IP SLA monitoring policy allows you to specify the SLA frequency and SLA type, which can be ICMP, Layer 2 Ping (relevant for Layer 1/Layer 2 PBR), and TCP. The Layer 4/Layer 7 redirect policy for Layer 3 PBR tracks the Layer 3 destinations that are used for packet redirect/rewrite information, which should be associated with Redirect Health Groups. Similarly, the redirect policy for Layer 1/Layer 2 PBR tracks the Layer 1/Layer 2 destinations (MAC addresses).

**Note:** If you have multiple PBR policies that have same PBR destination IP in same VRF and tracking is enabled on any of these policies, you need to use same IP SLA monitoring policy and use same health-group for the IP on all of them. It’s because PBR destination uses (VRF, IP) as the key for tracking status. Same PBR destination IP in the VRF cannot have different up/down states.

You can read more about Cisco ACI service graph PBR configuration in the Cisco APIC Layer 4 to Layer 7 Services Deployment Guide, release 5.2(x), available at: https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/layer-4-to-layer-7-services-configuration/cisco-apic-layer-4-to-layer-7-services-deployment-guide-52x.html

For comprehensive information about Cisco ACI service graph PBR, including PBR end-to-end packet flow, service graph PBR requirements, supported and unsupported topologies, and PBR tracking feature information, please visit the Cisco Application-Centric Infrastructure Policy-Based Redirect Service Graph Design White Paper, available at: https://www.cisco.com/c/en/us/solutions/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739971.html.

For additional references, access videos from various CiscoLive! Events on Cisco ACI Policy-Based Redirect and Service Graph:

*   ACI Troubleshooting: Policy-Based Redirect (PBR) – BRKACI-2644 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=BRKACI-2644#/session/1549998460437001xXjj
*   Demystifying ACI Security – BRKSEC-2048 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=ACI%20vzAny#/session/153236928685300122Ht

**Note:** In general, configuration procedures in this section might be slightly different from the referred documentation, because of different minor versions in Cisco APIC releases.

#   5)  Describing Cisco ACI Multi-Pod Deployment

##  5.1)    Introduction

Various business requirements mandate a deployment of stretched or separate data centers that need to be interconnected with each other. While interconnected they need to provide network automation, simplified operational tasks and support for agile workloads in a secure manner. Enterprises often need to distribute members of the same cluster across multiple data center locations. They distribute members to provide continuous availability in the event of a data center failure and to enable highly flexible data center environment positioned in an active-active architecture across sites.

Cisco Application-Centric Infrastructure (ACI) provides different options for fabric extensions and interconnections, which can meet similar business demands for different customers, and provide innovative and open architecture for distributed data center networking for service providers.

You will learn about the Cisco ACI Multi-Pod deployment option, which uses a single Cisco Application Policy Infrastructure Controller (APIC) cluster as single point of management and policy definition for the entire network, independently from the number of ACI pods within a single ACI fabric compounding it. Since from a physical perspective, the different pods in this solution are interconnected by using an Inter-Pod Network (IPN) as an extension of the Cisco ACI infrastructure network, you will learn about IPN requirements and functionality, and packet flow between pods. You will also cover service insertion, such as firewalls and load balancers across different pods, and service graph considerations in Cisco ACI Multi-Pod deployments.

##  5.2)    Cisco ACI Multi-Pod Overview

Cisco ACI Multi-Pod design represents a single Cisco APIC cluster/single domain that interconnects portions of the fabrics (referred as pods) while each one has its own leaf-and-spine architecture.

Why do you need the Cisco ACI Multi-Pod?

*   Deployment of active-active disaster recovery solution for business continuity.
*   Data center deployed in multiple server rooms.
*   Infrastructure for a virtualization solution that supports live VM mobility across Layer 3 domains, etc.
*   Use a single administration domain.

The Cisco ACI Multi-Pod design offers full resiliency at the network level across pods, even if the deployment remains functionally a single fabric, with all the nodes deployed across the pods under the control of the same Cisco APIC cluster. The main advantage of the Cisco ACI Multi-Pod design is hence operational simplicity, with separate pods managed as if they were logically a single entity. It enables you to manage all pods from a single point of configuration, while all Cisco ACI building blocks, such as tenants, virtual routing and forwarding (VRF) instances, bridge domains, endpoint groups (EPGs), and so on are deployed and useable on all pods in a Cisco ACI fabric.

One of the typical examples of Cisco ACI Multi-Pod is deploying a pod (spine and leaf switches) per floor or per building. The connectivity between pods is established through IP connection between spines in each pod. This IP connection is called Inter-Pod Network (IPN).

The following figure illustrates the Cisco ACI Multi-Pod deployment.

![](/images/aciadvanced/aciadvanced_5_2_1.png)

Cisco Multi-Pod provides a fault tolerant fabric, since each pod has isolated control plane protocols.

The main infra control plane protocols that run individually in each pod are as follows:

*   **Intermediate System-to-Intermediate System (IS-IS):** For infra tunnel endpoint (TEP) reachability within a pod.
    
    Even when IS-IS stopped working in one pod, it does not affect IS-IS in other pods because IS-IS runs only between leaf and spine switches in each pod. For TEP reachability towards nodes in other pods, spines learn a TEP range of other pods, instead of individual TEP IPs, via Open Shortest Path First (OSPF) through IPN. This TEP range is advertised to local leaf switches via IS-IS within each pod.

*   **Council of Oracles Protocol (COOP):** For endpoint information within a pod.
    
    Even when COOP stopped working in one pod, it does not affect COOP in other pods because COOP runs only between leaf and spine switches in each pod. However, endpoint entries in one pod are shared and stored in COOP database in other pods as well via Multiprotocol Border Gateway Protocol (MP-BGP) Ethernet VPN (EVPN) between spine switches in each pod through IPN. Hence, users still need to pay attention to endpoint scalability across a fabric.

**Note:** See the Cisco ACI Verified Scalability Guide for detail numbers, available at: https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/verified-scalability/cisco-aci-verified-scalability-guide-521.html

*   **VPNv4/v6 MP-BGP:** For Layer 3 Outside (L3Out) routes distribution within a pod.
    Even when MP-BGP stopped working in one pod, it does not affect MP-BGP in other pods because MP-BGP establishes neighbors only between route reflector spine switches and leaf switches in each pod to distribute L3Out routes within a pod. On top of MP-BGP within a pod, Multi-Pod establishes other MP-BGP VPNv4/v6 sessions between spine switches in each pod through IPN. This MP-BGP is called external MP-BGP compared to internal MP-BGP within each pod, which is used to share L3Out routes in one pod to other pods.

The IPN that provides IP connection between pods must support specific requirements, such as Bidirectional Protocol Independent Multicast (PIM Bidir) support and acceptable latency (IPN requirements are described further in this section in more details), and influences the Cisco ACI Multi-Pod use cases. There are two main use cases for the deployment of Cisco ACI Multi-Pod regarding physical location of different pods:

*   **Multiple pods in the same physical data location:** You can use this approach when you have a very large fabric. You can divide it in smaller pods and benefit from the failure domain isolation, and when you have a specific cabling layout already in place inside the data center. Also, you can use Cisco ACI Multi-Pod when there is a need to scale up a single ACI fabric with above 200 leaf nodes in a single pod, which you can divide in multiple pods, following a requirement to create a very large fabric. The latency in the IPN is not a problem in this use case, since the pods are placed in the same physical location.

*   **Multiple pods across different locations:** Enables you to implement the Cisco ACI Multi-Pod solution between fabrics in different locations. The pods are usually deployed in relative proximity (such as a metropolitan area) and interconnected through point-to-point links (such as dark fiber connections or dense wavelength division multiplexing [DWDM] circuits), or in different geographical locations connected through IPN (which can even be internet) that provides IP reachability, while taking into account the latency between pods, to avoid bottlenecks in the infrastructure.

Based on the use cases described above, Cisco ACI Multi-Pod can be deployed in these topologies:

*   **Intra data center:** Since the pods are deployed in the same data center, you can use a pair of centralized IPN devices to interconnect the different pods. Those IPN devices must potentially support many 40/100/400G interfaces, so you can utilize a couple of modular switches for this role.

![](/images/aciadvanced/aciadvanced_5_2_2.png)

*   **Two directly connected data centers:** The pods are positioned in two data centers in different locations, connected by point-to-point links (dark fibers or DWDM circuits). The maximum supported latency between pods is 50 msec round-trip time (RTT), which roughly translates to a geographical distance of up to 2500 miles (approximately 4000 km). Prior to Cisco ACI Release 2.3(1), the supported latency was 10 msec RTT. Please note that the link between the spine switches and IPN routers is 40/100G because of spine switches, while the interconnecting link between the data centers is typically 10G/40G/100G. Although there are no specific bandwidth requirements between pods, it needs to be designed to accommodate user traffic between pods.

![](/images/aciadvanced/aciadvanced_5_2_3.png)

*   **Three directly connected data centers:** The pods are positioned in three data center, directly connected among each other. Similarly, the interconnection can be established through dark fibers or DWDM circuits offering10/40/100/400G speed. The same requirement and considerations from the previous use case applies here.

![](/images/aciadvanced/aciadvanced_5_2_4.png)

*   **Multiple sites connected by a generic Layer 3 network:** Enables you to connect multiple pods in multiple sites, which are connected by a generic Layer 3 infrastructure (for example, Multiprotocol Label Switching [MPLS] network). The same requirements from previous use cases such as latency apply here as well.

![](/images/aciadvanced/aciadvanced_5_2_5.png)

### Failure Scenarios

Since the database used by the APIC is split into several database units (shards), while each shard is replicated three times with each copy assigned to a specific Cisco APIC, the Cisco ACI Multi-Pod fabric may face different failure scenarios due to the positioning of the APIC nodes in pods. In a three-node Cisco APIC cluster deployment scenario, one replica for each shard is always available on every Cisco APIC node, but if you use a larger model, for example, a five-node Cisco APIC cluster, the three replicas are spread on three of the five nodes. This section provides you with a recap on APIC failure impact along with a scenario specific to Cisco ACI Multi-Pod topology.

### Standby APIC Considerations

Standby APIC functionality enables you to operate one or more APICs in the standby mode. Standby APICs act as a replacement for any of the APICs in an active cluster. The standby APIC does not participate in policy configuration or management.

In a Cisco ACI Multi-Pod deployment, you should assign the standby Cisco APICs to same pod as the active Cisco APICs that it may replace. As a result of the Cisco APIC replacement procedure, the standby Cisco APIC takes over the node identifier (ID) from the replaced (previous active) Cisco APIC, but keeps its previously configured name and pod ID.

### Cisco APIC Nodes Failure Impact

A loss of a single Cisco APIC controller in any pod (in a three APICs design or more) has no operational significance, since the cluster still has a quorum (for example, minimum two out of three APICs). Hence, you can continue to make configuration changes since all shards are in a read/write mode. In a case of a dual nodes failure scenario, some (if you have more than three APICs) or all (with three APICs) shards will be in a read-only mode. This read-only mode is due to missing replicas that were on the failed APICs. Therefore, if you have a three-node cluster, you cannot make any configuration changes until at least one of the APICs is restored. A best practice is to use standby APICs, so Cisco APIC cluster is restored to full health.

### Split-Brain Failure Scenario

A split-brain failure scenario represents a situation in the Cisco ACI Multi-Pod when the connectivity between the pods is interrupted.

![](/images/aciadvanced/aciadvanced_5_2_6.png)

When the connection between pods has an issue in the above example, APICs will be in split-brain situation where all APICs are up and running but clustering is not formed. In such situation, the majority side (two APICs in Pod 1) is still in read/write mode and can perform configuration changes. However, the configuration change is limited to the local Pod 1 because the two APICs in Pod 1 do not have reachability to either switch or APIC nodes in the other pod. On the other hand, the minority side (one APIC in Pod 2) is in read-only mode and cannot perform configuration changes even for its local Pod 2.

In case the APIC cluster has 5 APIC nodes (ex. 3 APICs in Pod 1, 2 APICs in Pod 2), the read/write, or read-only mode will be indeterministic because of shard replica distribution (3 replicas of each object). Replicas of some objects may be on 1 APIC in Pod 1 and 2 APICs in Pod 2, where Pod 2 APICs are majority and in read/write mode, but others may have majority in Pod 1, where Pod 1 APICs are in read/write mode.

In general, it is recommended to fix the split-brain situation prior to any configuration changes regardless of read/write mode to avoid database inconsistency. Also, it applies to APIC cluster with more than 3 nodes, because it is difficult to understand which exact objects are in read/write mode in which side and perform configurations only for those objects in read/write mode.

### Pod Failure Scenario

A pod failure scenario may occur when an entire site goes down because of a disaster (fire in the data center or a whole building, flood, earthquake, and so on).

![](/images/aciadvanced/aciadvanced_5_2_7.png)


In this case, significant behavioral difference between a three- or five-node Cisco APIC cluster stretched across pods. If you use a three-node Cisco APIC cluster deployment, the hard failure of the pod with one controller has no effect, similar to a loss of a single Cisco APIC controller for a fabric in one location. If the pod with the most nodes fails, all shards on the Cisco APIC node in the other pod go into a read-only mode. If the failed pod is out of service and may take longer to recover, you may use a standby controller in the second pod, which you can promote to active to ensure re-establishment of the quorum for the Cisco APICs. The specific procedure required to bring up the standby node, so it can join the cluster, is identical to the procedure to activate the standby node in a single pod ACI fabric.

In a five-node Cisco APIC cluster deployment in two pods (three nodes in the first and two in the second pod), the hard failure of the pod with three APICs would leave only two nodes connected to the other pod. Similar to the three-node scenario, you can prepare a standby Cisco APIC node in the second pod, which will provide cluster majority and re-establishment of the quorum. The main difference is that this failure scenario may lead to the loss of information for the shards that were replicated across the three nodes in the failed pod. This difference is because shards have only three replicas. There is a specific fabric recovery procedure that you can use to recover the lost shard information from a previously taken snapshot (configuration backup), but it is mandatory to contact Cisco Technical Assistance Center (TAC) or Advanced Services for assistance in performing such ID recovery procedure.

With that said, APIC cluster with 3 APIC nodes may be more resilient in 2 pods scenario. However, APIC cluster with 5 APIC nodes provides more scalability, such as number of leaf switches it can manage. If you are facing scalability demands in your infrastructure that require the deployment of a five-node Cisco APIC cluster, follow these deployment guidelines:

![](/images/aciadvanced/aciadvanced_5_2_8.png)

As indicated, a pod can be part of the Cisco ACI Multi-Pod fabric even without having a locally connected Cisco APIC. Hence, you can follow this approach when deploying six (or more) pods using a five-node Cisco APIC cluster.

In addition to three- or five-node clusters, Cisco APIC Release 4.0(1) introduced support for a four-node Cisco APIC cluster, which supports higher number of leaf switches than a three-node cluster.

##  5.3)    Inter-Pod Network Overview

The IPN connects different Cisco ACI pods providing pod-to-pod communication (east-west traffic) as an extension of the Cisco ACI fabric underlay infrastructure. Therefore, the IPN must support several specific functionalities to perform those connectivity functions, such as:

*   Multicast support (PIM Bidir with at least /15 subnet mask), which is needed to handle Layer 2 broadcast, unknown unicast, and multicast (BUM) traffic.
*   Dynamic Host Configuration Protocol (DHCP) relay support.
*   Open Shortest Path First (OSPF) support between spine and routers.
*   Increased maximum transmission unit (MTU) support to handle the Virtual Extensible LAN (VXLAN) encapsulated traffic.
*   Quality of service (QoS) considerations for consistent QoS policy deployment across pods.
*   Routed subinterface support, since the use of subinterfaces on the IPN devices is mandatory for the connections toward the spine switches (the traffic originated from the spine switches interface is always tagged with an 802.1Q VLAN 4 value).
*   LLDP must be enabled on the IPN device.

**Note:** The VLAN 4 subinterface is hardcoded for zero touch provisioning so that new spine switches in the new pod can send DHCP discovery in the fixed VLAN even before Cisco APIC can push any policies to them. This VLAN is local only to the link between the spine switches and IPN devices. It does not have to be extended across IPN.

You have to separately configure and manage the IPN network, since it is not part of Cisco ACI policy model.

### Multicast Support

Besides the unicast communication, the east-west traffic in the Cisco Multi-Pod solution contains Layer 2 multi-destination flows belonging to bridge domains that are extended across pods. This type of traffic is referred to as BUM, which is encapsulated into a VXLAN multicast frame and transmitted to all the local leaf nodes inside the pod.

For this purpose, a unique multicast group is associated to each defined bridge domain, using the name of Bridge Domain Group IP–outer (BD GIPo). This multicast group is dynamically picked from the multicast range configured during the initial startup script of APIC. Once received by the leaf switches, it is forwarded to the connected devices that are part of that bridge domain or dropped depending on the specific bridge domain configuration.

The architecture must achieve the same behavior for endpoints that are connected to different pods, while being part of the same bridge domain. To flood the BUM traffic across pods, the same multicast group that is used inside the pod is also extended through the IPN network. Those multicast groups should work in Bidirectional Protocol Independent Multicast (PIM Bidir) mode and must be dedicated to this function (such as, not used for other purposes, applications, and so on). Layer 3 multicast (VRF GIPo) is also forwarded over the IPN.

The main reasons for using PIM Bidir in the IPN network are:

*   **Scalability:** Since BUM traffic can be originated by all the leaf nodes that are deployed across pods, the use of a different PIM protocol would result in the creation of multiple individual (S, G) entries on the IPN devices that may exceed the specific platform capabilities. An example of a PIM protocol is PIM any-source multicast (ASM). With PIM Bidir, a single (*, G) entry must be created for a given bridge domain, independently from the overall number of leaf nodes.

*   **No requirement for data-driven multicast state creation:** The (*, G) entries are created in the IPN devices when a bridge domain is activated in the ACI Multi-Pod fabric, independently from the fact there is an actual need to forward BUM traffic across pods for that given bridge domain. The implication is that when the need to do so arises, the network will be ready to perform those duties, avoiding longer convergence time for the application caused. An example is in PIM ASM by the data-driven state creation.

*   **It represents Cisco prescriptive:** It is well-tested and hence recommended design option.

### DHCP Relay Support

The IPN must support DHCP relay, which enables you to auto-provision configuration for all the Cisco ACI devices deployed in remote pods. The devices can join the Cisco ACI Multi-Pod fabric with zero-touch configuration (similar to a single pod fabric).

Therefore, the IPN devices connected to the spine switch of the remote pod need to be able to relay DHCP requests generated from a new starting Cisco ACI spine toward the Cisco APIC nodes.

Cisco APICs use the first pod TEP address range regardless of which pod it's physically connected to.

### OSPF Support

OSPFv2 is the only routing protocol (in addition to static routing) supported for connectivity between the IPN and the spine switches. It is used to advertise the TEP address range to other pods. As long as IPN devices and spines are OSPF neighbors, any protocols can be used to deliver the TEP information from one IPN device to another. For example, IPN devices in Pod 1 and Pod 2 can be MPLS as long as it advertises OSPF routes they learned from spines in each pod.

Therefore, the routers that are used for IPN have to exchange routing information with the connected Cisco ACI spine switches using OSPF routing protocol.

### Increased MTU Support

The IPN must support increased MTU on its physical connections, so the VXLAN data-plane traffic can be exchanged between pods without fragmentation and reassembly, which slows down the communication. Before Cisco ACI Release 2.2, the spine nodes were hardcoded to generate 9150 bytes full-size frames for exchanging MP-BGP control plane traffic with spine switches in remote pods. This mandated support for that 9150 bytes MTU size on all the Layer 3 interfaces of the IPN devices.

From Cisco ACI Release 2.2, a global configuration knob has been added on Cisco APIC to allow proper tuning of the MTU size of all the control plane packets generated by ACI nodes (leaf and spine switches), including inter-pod MP-BGP control plane traffic.

Implied is that the MTU support required in the IPN becomes solely dependent on the maximum size of frames that the endpoints generate that are connected to the Cisco ACI leaf nodes. However, packets would be encapsulated with VXLAN (50 bytes) and possibly other depending on the protocols used (for example, IPsec encryption, which adds an extra header) to deliver packets end to end. Hence, it is generally recommended to support 9150 bytes MTU throughout the IPN.

### QoS Considerations

Typically, you will deploy Cisco APIC cluster across pods, and you must ensure that the intracluster communication between the Cisco APIC nodes is prioritized across the IPN infrastructure.

Since the IPN is not under Cisco APIC management and may modify the 802.1p (class of service [CoS]) priority settings, you need to take additional steps to guarantee the QoS priority in a Cisco ACI Multi-Pod topology.

![](/images/aciadvanced/aciadvanced_5_3_1.png)

When the packet leaves the spine switch in one pod towards the IPN, the outer header of the packet has CoS value to keep the prioritization of variety of traffic across pods. However, IPN devices may not preserve the CoS setting in 802.1p frames during transport. Therefore, when the frame reaches the other pod, it may lack the CoS information that is assigned at the source in the first pod.

To preserve the 802.1p frame information in the Cisco ACI Multi-Pod topology, you have to configure on APIC a differentiated services code point (DSCP) policy to preserve the QoS priority settings while mapping CoS to DSCP levels for different traffic types. You also have to ensure that IPN devices will not overwrite the DSCP markings, so the IPN will not change the configured levels. With a DSCP policy enabled, Cisco APIC converts the CoS level in the outer 802.1p header to a DSCP level in the outer IP header and frames leave the pod according to the configured mappings. When they reach the second pod, the mapped DSCP level is mapped back to the original CoS level, so the QoS priority settings are preserved.

The following examples show a CoS-to-DSCP mapping that is configured on the APIC using the **Tenant > infra > Policies > Protocol > DSCP class-cos translation policy for Layer 3 traffic**, which modifies the default behavior. Hence, when traffic is received on the spine nodes of a remote pod, it is reassigned to its proper class of service before being injected into the pod based on the DSCP value in the outer IP header of inter-pod VXLAN traffic.

![](/images/aciadvanced/aciadvanced_5_3_2.png)

The DSCP class-cos translation policy in the example above marks the **Policy Plane Traffic** (that is, communication between APIC nodes that are deployed in separate pods) as Expedited Forwarding (EF), whereas **Control Plane Traffic** (that is, OSPF and MP-BGP packets) is marked as CS4. Thus, you can configure the IPN devices to prioritize those two types of traffic to ensure that the policy and control plane remains stable also in scenarios where a large amount of east-west user traffic is required across pods.

### IPN Control Plane

Since the IPN represents an extension of the Cisco ACI infrastructure network, it must ensure that the VXLAN tunnels can be established across pods for endpoint communication.

Inside each Cisco ACI pod, IS-IS is the infrastructure routing protocol that is used by the leaf and spine nodes to peer with each other and exchange IP information for locally defined loopback interfaces (usually referred to as TEP addresses). During the auto-provisioning process for the nodes belonging to a pod, the APIC assigns one (or more) IP addresses to the loopback interfaces of the leaf and spine nodes part of the pod. All those IP addresses are part of an IP pool that is specified during the bootup process of the first APIC node and takes the name of "TEP pool." The IPN should facilitate communication between the local TEPs and the remote TEPs.

In a Cisco Multi-Pod deployment, each pod is assigned a separate and not overlapping TEP so the subnet route redistribution works correctly and each side learns the proper routes to the remote TEPs across the IPN, as shown in this figure:

![](/images/aciadvanced/aciadvanced_5_3_3.png)

Spine switches in each pod advertise the TEP IP prefix of its own pod towards IPN via OSPF in VRF overlay-1 so that IPN can forward VXLAN encapsulated traffic and communications between each ACI node across pods. It is required that IPN propagates the learned TEP IP prefix to other spines as well so that each pod knows where to send the traffic towards other pods. Once spine switches learn other pod’s TEP IP prefixes via OSPF, it redistributes them into IS-IS to share the information with all switch nodes.

It is a best practice to create a dedicated VRF for IPN functionality in IPN devices so that these infra TEP IP prefixes and also infra multicast group IP addresses will not conflict with any other route information in the IPN devices.

**Note:** The spine switches also send few individual TEP IP addresses to the IPN, associated to specific loopback addresses defined on the spine switches. It is required to ensure that traffic that is destined to those IP addresses can be delivered from the IPN directly to the right spine where they are defined (such as not following equal-cost paths that may lead to a different spine). No TEP IP addresses for leaf nodes loopback interfaces should ever be sent into the IPN, which ensures to keep the routing table of the IPN devices very lean independently from the total number of deployed leaf nodes.

Even though OSPF peering is required between the spine switches and the IPN devices, you are not limited to use OSPF across the entire IPN infrastructure. As depicted in the following figure, the IPN can use a generic Layer 3 infrastructure interconnecting the pods (like an MPLS network, for example) and utilize a separate routing protocol inside that Layer 3 network. Mutual redistribution would then be needed with the process that is used toward the spine switches.

![](/images/aciadvanced/aciadvanced_5_3_4.png)

### BUM Traffic Between Pods

Each bridge domain in Cisco ACI fabric associates a separate multicast group (GIPo) to ensure granular delivery of multi-destination frames only to the endpoints that are part of a given bridge domain. Hence, each bridge domain by default makes use of a GIPo part of the 225.0.0.0/15 multicast range, which was configured during the first APIC setup.

In a Cisco ACI Multi-Pod deployment, the bridge domains can be extended access pods, so similar behavior must be achieved. As indicate before, the IPN must support PIM Bidir, so the BUM frames that are generated by an endpoint part of a bridge domain. These frames are encapsulated by the leaf node where the endpoint is connected, can transit across the IPN to reach remote endpoints part of the same bridge domain.

For this functionality, the spine switches must perform two basic functions:

*   Forward received multicast frames toward the IPN devices to ensure they can be delivered to the remote pods.
*   Send IGMP joins toward the IPN network every time a new bridge domain is activated in the local pod. This function is to be able to receive BUM traffic for that bridge domain originated by an endpoint that is connected to a remote pod.

For each bridge domain, one spine node is elected as the authoritative device to perform both functions, using the IS-IS control plane between the spine switches.

The following figure depicts the end-to-end BUM forwarding between endpoints part of the same bridge domain (BD1), which are connected in separate pods.

![](/images/aciadvanced/aciadvanced_5_3_5.png)

The delivery of BUM traffic between pods follows the following sequence:

1.  EP1 belonging to BD1 originates a BUM frame.
1.  The frame is encapsulated by the local leaf node and destined to the multicast group GIPo1 (225.1.1.128) associated to BD1. As a consequence, it is sent along one of the multi-destination trees that are assigned to BD1 and reaches all the local spine and leaf nodes where BD1 has been instantiated.
1.  Spine 1 is responsible for forwarding BUM traffic for BD1 toward the IPN devices, using the specific link connected to IPN1.
1.  The IPN device receives the traffic and performs multicast replication toward all the pods from which it received an IGMP Join for GIPo1. This process ensures that BUM traffic is sent only to pods where BD1 is active.
1.  The spine that sent the IGMP Join toward the IPN devices receives the multicast traffic and forwards it inside the local pod along one of the multi-destination trees that are associated to BD1. All leaf switches where BD1 has been instantiated receive the frame.
1.  The leaf where EP2 is connected also receives the stream, de-encapsulate the packet, and forwards it to EP2.

An important design consideration should be made for the deployment of the rendezvous point (RP) in the IPN. The role of the RP is important in a PIM Bidir deployment, as all multicast traffic in Bidir groups vectors toward the Bidir RPs, branching off as necessary as it flows upstream and/or downstream. The implication is that all the BUM traffic exchanged across pods would be sent through the same IPN device acting as RP for the 225.0.0.0/15 default range used to assign multicast groups to each defined bridge domain. You can also implement a design to balance the workload across different RPs while splitting the range and configuring an active RP for each sub-range on separate IPN devices, as shown in this figure:

![](/images/aciadvanced/aciadvanced_5_3_6.png)

Bidir PIM redundancy is based on a backup model that utilizes phantom RPs. A single RP handles everything, and in the case of a failure, another RP takes over. This model is achieved by configuring the same loopback IP address on each IPN device to act as phantom RPs, but with a different subnet mask (for example, configure IP address 192.168.100.1/30 on the loopback interface on IPN1 device and 192.168.100.1/29 on the loopback interface on IPN2 device), which allows the use of the longest-prefix-match logic in the routing process. Hence, all IPN devices should use this IP address (in this example, IP 192.168.100.1) for the static RP address.

### Spine Switches and IPN Connectivity

If you have multiple spine switches in each pod, it is not mandatory to connect every spine that is deployed in a pod to the IPN devices, even though two should be a minimum for redundancy. For example, when you have four spine switches in a pod, you can connect only two to the IPN, without functional implications for unicast communication across sites. The local leaf nodes that encapsulate the traffic to a remote pod would always prefer the paths through the spine switches that are actively connected to the IPN devices (based on IS-IS routing metric). Similarly, you won’t experience issues with the BUM traffic that needs to be sent to remote pods, since only the spine nodes that are connected to the IPN devices will be responsible for handling such traffic.

However, if you directly connect spine switches belonging to separate pods as depicted in the following figure, there will be implications for the BUM traffic that needs to be sent to remote pods. In this situation, the directly connected spine switches in separate pods cannot be both elected as designated for a given bridge domain, which will lead to the impossibility of forwarding BUM traffic across pods. Therefore, you should always deploy at least one Layer 3 IPN device (or a pair for redundancy) between pods.

![](/images/aciadvanced/aciadvanced_5_3_7.png)

In addition, you have to always ensure that there is a physical path interconnecting all IPN devices to avoid issues with the BUM traffic across pods. When different IPN nodes do not have a physical path between them, the designated spine switches in two different pods can send the BUM traffic and Internet Group Management Protocol (IGMP) join for a specific group to both IPN nodes, as shown in this figure:

![](/images/aciadvanced/aciadvanced_5_3_8.png)

As a consequence, the IPN devices won’t have proper (*, G) state and the BUM communication would fail. To avoid this situation from happening, the recommendation is to always ensure that there is a physical path interconnecting all the IPN devices.

To illustrate the Layer 3 IPN device-specific functionalities, the following example shows an IPN configuration for a Cisco Nexus 9000 Series Switch operating in NX-OS mode, which interconnects two pods (its interface Ethernet2/7 is connected to Pod 1 – Spine 1 and interface Ethernet2/9 to Pod 2 – Spine 1).

![](/images/aciadvanced/aciadvanced_5_3_9.png)

The Cisco Nexus 9000 Series Switch configuration includes the required connectivity functions, such as PIM Bidir multicast support, DHCP relay support, and increased MTU support:

```shell
    feature dhcp
    feature pim
    
    # Enable Jumbo frames
    policy-map type network-qos jumbo
    class type network-qos class-default
        mtu 9216
    
    system qos
    service-policy type network-qos jumbo
    
    service dhcp
    ip dhcp relay
    ip pim ssm range 232.0.0.0/8
    
    # Create a new VRF for Multipod.
    vrf context fabric-mpod
    ip pim rp-address 12.1.1.1 group-list 225.0.0.0/8 bidir
    ip pim rp-address 12.1.1.1 group-list 239.255.255.240/28 bidir
    ip pim ssm range 232.0.0.0/8
    
    interface Ethernet2/7
    no switchport
    mtu 9150
    no shutdown
    
    interface Ethernet2/7.4
    description pod1-spine1
    mtu 9150
    encapsulation dot1q 4
    vrf member fabric-mpod
    ip address 201.1.2.2/30
    ip router ospf a1 area 0.0.0.0
    ip pim sparse-mode
    ip dhcp relay address 10.0.0.1
    ip dhcp relay address 10.0.0.2
    ip dhcp relay address 10.0.0.3
    no shutdown
    
    interface Ethernet2/9
    no switchport
    mtu 9150
    no shutdown
    
    interface Ethernet2/9.4
    description to pod2-spine1
    mtu 9150
    encapsulation dot1q 4
    vrf member fabric-mpod
    ip address 203.1.2.2/30
    ip router ospf a1 area 0.0.0.0
    ip pim sparse-mode
    ip dhcp relay address 10.0.0.1
    ip dhcp relay address 10.0.0.2
    ip dhcp relay address 10.0.0.3
    no shutdown
    
    interface loopback29
    vrf member fabric-mpod
    ip address 12.1.1.1/32
    
    router ospf a1
    vrf fabric-mpod
        router-id 29.29.29.29
```

As a best practice, the Multi-Pod traffic across the IPN is isolated in a VRF instance. In addition, the spine interfaces are connected to the IPN devices through point-to-point routed subinterfaces with VLAN 4. The previously mentioned subinterfaces facing IPN devices use the same MAC address just like all BD pervasive SVIs use the same MAC address by default. Hence, if SVI with VLAN 4 is used on IPN devices that are connected to multiple spines, it will see flaps of spine MAC addresses. To prevent it, IPN devices need to use subinterfaces as well to connect to spine switches.

**Note:** The use of subinterfaces on the IPN devices is only mandatory for the connections toward the Cisco ACI spine switches.

##  5.4)    Multi-Pod Provisioning and Packet Flow Between Pods

One of the important properties of Cisco ACI is the capability of bringing up a physical fabric in an automatic and dynamic fashion, requiring only minimal interactions from the network administrator. It is a huge leap from the traditional network deployments, where you have to provision the devices one-by-one, to form the final architecture.

Similarly, in a Cisco ACI Multi-Pod deployment, the fabric must be provisioned before it can forward endpoint traffic. The use of a common APIC cluster enables auto-provisioning capabilities to a Cisco ACI Multi-Pod deployment. The end goal is adding remote pods to the Multi-Pod fabric with minimal user intervention, so endpoint traffic forwarding can be enabled quickly towards the newly added remote pods.

The Cisco ACI Multi-Pod fabric applies different control and data plane functionalities for connecting endpoints deployed across different pods. Once, the Cisco ACI Multi-Pod is successfully provisioned, the information about all the endpoints stored in COOP database in each spine will be exchanged via BGP EVPN through IPN. External route information from L3Outs is exchanged via BGP VPNv4/v6. These are the control plane between pods. Once the forwarding information such as endpoint and L3Out routes are exchanged via control plane, data plane traffic will be forwarded across pods through IPN with TEP and VXLAN encapsulation like within a single pod. Not only unicast traffic, but also flooding traffic can be forwarded seamlessly.

### Cisco ACI Multi-Pod Provisioning

Cisco ACI Multi-Pod deployment is automated in a lot of ways to offer easy provisioning with less user intervention.

Initially, the first pod (also known as ‘seed’ pod) should be already set up using the traditional ACI fabric bring-up procedure, and the ‘seed’ pod and the second pod should be physically connected to the IPN devices. Before Cisco ACI Multi-Pod provisioning process can start, you should set up the Cisco APIC and the IPN using the following these steps:

1.  **Configure access policies:** Configure access policies for all the interfaces on the spine switches used to connect to the IPN. Define these policies as spine access policies. Use these policies to associate an Attached Entity Profile (AEP) for a Layer 3 domain that uses VLAN 4 (as a requirement) for the encapsulation for the subinterface. Define these subinterfaces in the same way as normal leaf access ports. The subinterfaces are used by the infrastructure L3Out interface that you define.

1.  **Define the Multi-Pod environment:** For the Cisco ACI Multi-Pod setup, you should define the TEP address for the spine switches facing each other across the IPN. This IP address is used as anycast shared address across all spine switches in a pod. You should also define the Layer 3 interfaces between the spine interfaces and the IPN.

1.  **Configure the IPN.** Configure the IPN devices with IP addresses on the interfaces facing the spine switches, and enable the OSPF routing protocol, MTU support, DHCP-relay, and PIM Bidir. The IPN devices create OSPF adjacencies with the spine switches and exchange the routes of the underlying IS-IS network part of VRF overlay-1. The configuration of the IPN defines the DHCP relay, which is critical for learning adjacencies because the DHCP frames forwarded across the IPN will reach the primary APIC in the first pod to get a DHCP address assignment from the TEP pool. Without DHCP relay in the IPN, zero-touch provisioning will not occur for Cisco ACI nodes deployed in the second pod.

1.  **Establish the interface access policies for the second pod:** If you do not establish the access policies for the second pod, then the second pod cannot complete the process of joining the fabric. You can add the device to the fabric, but it does not complete the discovery process. Thus, the spine switch in the second pod has no way to talk to the original pod, since the OSPF adjacency cannot be established due to VLAN 4 requirement, and the OSPF interface profile and the external Layer 3 definition do not exist. You can reuse the access policies of the first pod as long as the spine interfaces you are using on both pods are the same. Hence, if the spine interfaces in both pods are the same and the ports in all the switches also are the same, then the only action you need to take is to add the spine switches to the switch profile that you define.

In the Cisco APIC user interface, you can use a wizard to add a pod to the Multi-Pod deployments, which helps you provision the necessary L3Outs on the spine switches connected to the IPN, MTU on all spine-to-IPN interfaces, OSPF configuration towards the IPN, Anycast TEP IP address, and so on. You can invoke this wizard using **Fabric > Inventory > Quick Start > Add Pod** and choose **Add Pod** from the work plane, as shown in this figure:

![](/images/aciadvanced/aciadvanced_5_4_1.png)

The Cisco ACI Multi-Pod discovery process follows this sequence:

![](/images/aciadvanced/aciadvanced_5_4_2.png)

1.  Cisco APIC node 1 pushes the infra L3Out policies to the spine switches in Pod 1. The spine L3Out policy provisions the IPN-connected interfaces on spine switches with OSPF.

    At this point, IPN has learned Pod 1 TEP prefixes via spine OSPF. And Pod 1 spine switches has learned IP prefixes on IPN facing to new spines in Pod 2.

1.  The first spine in Pod 2 boots up and sends DHCPDISCOVER to every connected interface, including the ones toward the IPN devices.

1.  The IPN device receiving the DHCPDISCOVER has been configured to relay that message to the Cisco APIC nodes in Pod 1. It can be accomplished because the IPN devices learned the Pod 1 TEP prefixes via OSPF with spines.

1.  The Cisco APIC sends DHCPOFFER, which includes the following initial parameters:
    1.  Subinterface IP of the new spine facing towards IPN.
    2.  A static route to the Cisco APIC that sent DHCPOFFER, which points to the IPN IP address that relayed the DHCP messages.
    3.  Bootstrap location for the infra L3Out configuration of the new spine.

    The new spine downloads a bootstrap for the infra L3Out configuration from APIC and configures OSPF and BGP towards IPN. It also sets itself as DHCP relay device for the new switch nodes in the new pod so that DHCP discovery from them can be relayed to APIC nodes in Pod 1 as well.

1.  All other nodes in Pod 2 come up in a same way as a single pod. Only the difference is the DHCP discovery is relayed through IPN.

1.  The Cisco APIC controller in Pod 2 is discovered as usual.

1.  The Cisco APIC controller in Pod 2 joins the APIC cluster.

### Inter-Pods MP-BGP Control Plane

In a single ACI fabric, information about all the endpoints connected to the leaf nodes is stored in the COOP database, which is available in the spine nodes. Every time an endpoint is learned as a local endpoint on a leaf node, the leaf originates a COOP control plane message to communicate the endpoint information (IPv4/IPv6 and MAC addresses) to a spine node. The COOP protocol is also applied by the spine switches to synchronize this information between them.

The COOP database information in each pod is shared via MP-BGP EVPN through IPN so that each pod knows which endpoint is learned in which pod. MP-BGP EVPN runs directly between spine switches in each pod. IPN devices will not participate in this BGP session, but it just provides TEP reachability to establish BGP sessions between each spine switch.

**Note:** BGP in each pod runs in a same BGP AS. This AS number is configured via BGP route reflector policy regardless of Cisco ACI Multi-Pod.

In the inter-pod MP-BGP, ACI uses the following types of TEPs which are used explain details in this section:

*   **BGP Router ID:** A router ID, IP address of each spine used to establish MP-BGP peer sessions. Previously called **CP-TEP (Control plane TEP)** and called EVPN-RID in MultiSite.

*   **DP-TEP (Data plane TEP):** An IP address used as the next-hop representing one pod in the BGP table.

*   **Anycast Proxy TEP:** An IP address used in the outer VXLAN header when packets go across IPN for spine-proxy.

The following figure depicts the inter-pods MP-BGP control plane functionality between two pods:

![](/images/aciadvanced/aciadvanced_5_4_3.png)

The inter-pods MP-BGP EVPN control plane functionality follows the following sequence:

1.  When an endpoint EP1 is learned on Leaf 1 in Pod 1, Leaf 1 sends a COOP message to one of the spine switches.

1.  The receiving spine adds the endpoint information to the COOP database and synchronizes the information to all the other local spine switches. EP1 is associated to the TEP address of Leaf 1.

1.  The endpoint information in the COOP database in Pod 1 is shared with other pods via MP-BGP EVPN.

    **Note:**   The MAC/IP address information for the endpoint is exchanged between spine switches in different pods applying the MP-BGP EVPN address-family (using Type-2 EVPN advertisements), whereas IPv4/IPv6 prefixes such as L3Out routes are exchanged via VPNv4 address-family.

1.  Once the spine in Pod 2 learns endpoint information via MP-BGP EVPN, it adds the information to the COOP database and synchronizes it to all the other local spine nodes. When MP-BGP entries from pod 1 are translated into the COOP entries on Pod 2 spines, the MP-BGP entries have Pod 1 DP-TEP as the next-hop. Currently, the Pod 1 DP-TEP is also translated into Pod 1 Anycast Proxy TEPs which are the IP addresses used on the wire in the outer VXLAN header when the packet goes across IPN. EP1 is now associated to an Anycast TEP address (Proxy A) that represents Pod 1 instead of Leaf 1 TEP. This behavior provides a robust control plane isolation across pods, as there is no requirement to send new control plane updates toward Pod 2 even if EP1 moves many times across leaf nodes part of Pod 1, since the entry would continue to point to the Proxy A next-hop address.

Since the spine nodes in different pods are part of the same BGP Autonomous System, the peering between the spine nodes connected through the IPN can be performed in two ways:

*   **Full-Mesh:** Establishing a full mesh of MP-BGP (IBGP) sessions between spine switches in each pod, which is the default behavior.

*   **Route Reflector:** Defining route reflector nodes in each pod (recommended for resiliency) so that the spine nodes only peer with the remote route reflector nodes and a full mesh of MP-BGP session is then established only between the route reflectors. Those route reflectors are called external route reflectors compared to internal router reflectors between spine and leaf switches within a pod which is to distribute L3Out routes within a pod.

**Note:** The MP-BGP EVPN is enabled on the spine switches in each pod connected through the IPN. The other spine switches deployed in the pod can still apply the inter-pod data-plane communication even if they do not establish EVPN peerings with the spine switches in the remote pods, as they receive remote endpoint information through COOP from the other local spine EVPN enabled switches.

When using the new pod creation wizard from **Fabric > Inventory > Quick Start > Add Pod**, you can choose between full-mesh or route reflector peering type, while preparing the pod for IPN connectivity, as depicted in this figure:

![](/images/aciadvanced/aciadvanced_5_4_4.png)

If you leave the **Use Defaults** checked in the wizard, you will use the default settings, or you can uncheck this option and define the peering type in the wizard. If you choose **Route Reflector** in the **Peering Type** field, you can also define the spine nodes used as external route reflector nodes (in each pod for resiliency).

You can view the BGP information, such as BGP peering type and utilized Anycast Proxy-TEP IP address in each pod, while navigating to **Tenants > infra> Policies > Protocol > Fabric Ext Connection Policies > Fabric Ext Connection Policy default** in the Cisco APIC user interface, as shown in this figure:

![](/images/aciadvanced/aciadvanced_5_4_5.png)

### Inter-Pods VXLAN Data Plane
The deployment of the ACI Multi-Pod fabric allows to seamlessly achieve Layer 2 and Layer 3 communication between endpoints that are connected to separate pods. To establish IP connectivity between endpoints that are connected to separate pods, the first requirement is to be able to complete an Address Resolution Protocol (ARP) exchange. In Cisco ACI Multi-Pod deployment, a slightly different mechanism is implemented depending on the specific bridge domain configuration, specifically with regard with the enablement or not of ARP flooding.

The following figure depicts a scenario where ARP flooding is enabled on the bridge domain, which encompasses both EP1 and EP2 endpoints. This figure illustrates the most complex scenario, where both EP1 and EP2 haven’t been discovered yet (considered as ‘silent hosts’).

![](/images/aciadvanced/aciadvanced_5_4_6.png)

The ARP request with ARP flooding that is enabled in the bridge domain follows the following sequence:

1.  EP1 generates an ARP request to determine EP2’s MAC address (assuming EP1 and EP2 are part of the same IP subnet).

1.  The local leaf (Leaf 1 in Pod 1) receives the packet, inspects the payload of the ARP packet and learns EP1 information (as a locally connected endpoint) and knows that the ARP request is for EP2’s IP address. Since EP2 has not been learned yet, the leaf does not find any information about EP2 in its local forwarding tables, such as the endpoint table. As a consequence, since ARP flooding is enabled, the leaf picks the FTAG associated to one of the multi-destination trees used for BUM traffic and encapsulates the packet into a multicast packet (the external destination address is the GIPo associated to the specific BD). While performing the encapsulation, the leaf also adds to the VXLAN header the pcTag information relative to the EPG that EP1 belongs to.

1.  The designated spine sends the encapsulated ARP request across the IPN, still applying the same GIPo multicast address as the destination of the VXLAN encapsulated packet. The IPN network must have built a proper state to allow for the replication of the traffic toward all the remote Pods where this specific bridge domain has been deployed. This replication is performed through multicast routing with PIM Bidir in IPN.

1.  One of the spine nodes in Pod 2 receives the packet (the specific spine that previously sent toward the IPN an IGMP Join for the multicast group associated to the bridge domain) and floods it along a local multi-destination tree. Notice also that the spine has learned EP1 information from an MP-BGP update received from the spine in Pod1.

1.  The leaf where EP2 is connected (Leaf 4 in Pod 2) receives the flooded ARP request, learns EP1 information (location and Class ID/pcTag) and forwards the packet to all the local interfaces part of the bridge domain.

1.  EP2 receives the ARP request and triggers its reply allowing then the fabric to discover it (EP2 is not a "silent host" anymore).

    ![](/images/aciadvanced/aciadvanced_5_4_7.png)

1.  EP2 generates a unicast ARP reply destined to EP1 MAC address.

1.  The local leaf (Leaf 4 in Pod 2) has now EP1 location information so the frame is VXLAN encapsulated and destined to Leaf 1 in Pod 1. At the same time, the local leaf also discovers that EP2 is locally connected and informs the local spine nodes through COOP.

1.  The remote leaf node (Leaf 1 in Pod 1) receives the packet, de-encapsulates it, learns and programs in the local endpoint table EP2 location and Class ID information and forwards the packet to the interface where EP1 is connected. EP1 is hence able to receive the ARP reply.

Once the ARP exchange is completed, both endpoints EP1 and EP2 are fully discovered and it is hence possible to establish data plane communication. Communication between endpoints belonging to separate pods is achieved with the establishment of end-to-end VXLAN tunnels between the leaf nodes where those endpoints are connected (TEP to TEP). These end-to-end VXLAN tunnels are established through ARP exchange in the above example.

From a security perspective, the contract policy enforcement is always applied at the ingress leaf node (for both Layer 2 and Layer 3 communications) in case remote endpoints are learned and end-to-end VXLAN tunnels are established. For example, EP1 can belong to one EPG and EP2 to another EPG, with a specific contract between them that is configured on the Cisco APIC, and the policy will be enforced across pods.

Cisco ACI fabric is designed to handle the presence of silent hosts even without requiring the flooding of ARP requests inside the bridge domain. Without ARP flooding allowed in the bridge domain, the leaf nodes are not allowed to flood the ARP Request frame along the local multi-destination tree. To ensure that the ARP request can be delivered to a remote endpoint for allowing it's learning, a process named “ARP gleaning” has been implemented.

With ARP gleaning, if the spine does not have information on where the destination of the ARP request is connected, the fabric generates an ARP request that is originated from the pervasive gateway IP address of the bridge domain. This ARP request is sent out all the leaf nodes edge interfaces part of the bridge domain.

In the Cisco ACI Multi-Pod deployment, the ARP glean request is also sent to the remote pods across the IPN. The ARP glean message is encapsulated into a multicast frame before being sent out toward the IPN. The specific multicast group 239.255.255.240 is used for sourcing ARP glean messages for all the bridge domains (instead of the specific GIPo normally used for BUM traffic in a bridge domain).

Since the bridge domain is locally defined in the remote pod, one of the spine nodes has already sent an IGMP join for the 239.255.255.240 group. So, when the spine in the remote pod receives the packet, it performs VXLAN decapsulation and generate an ARP glean message that is sent to all the leaf nodes inside the pod. So, it also reaches the specific leaf to which the ARP target endpoint is connected.

When this host sends the ARP reply, the packet is locally consumed by the leaf (the destination of the ARP reply is the pervasive gateway of the bridge domain on the leaf), which sends a COOP update with host information to the local spine switches. They announce this information to the remote spine switches via MP-BGP, including the spine switches in the pod where original ARP request was made. Hence, the spine switches know the referred host, and the next ARP request towards this host can be sent across pods encapsulated in a unicast VXLAN packet destined to the Anycast Proxy-TEP address identifying the spine switches in the remote pod.

##  5.5)    Connectivity to External Layer 3 Networks

An L3Out is a logical connection that is established between one or more pair of ACI leaf nodes (named border leaf switches) and WAN edge routers. Each VRF deployed inside the fabric can use those L3Out connections for establishing VRF-Lite connectivity with external routers. Alternatively, if all the bridge domains (or VRFs) defined inside the ACI fabric must have access to a common external routing domain, it is possible to define a single L3Out connection that all those entities share and usually define as part of the common tenant.

The routing information that is learned from the external network domain on the L3Outs connections is redistributed inside the ACI fabric by the border leaf switches. The control plane that is used for this function is MP-BGP; using the VPNv4/v6 address-family for sending external routing information to all leaf nodes, for each defined VRF. In a traditional single pod ACI deployment, a pair of spine switches is designated as MP-BGP VPNv4/v6 route reflectors. This designation is so that all the leaf nodes deployed in the fabric peer with the route reflectors to receive external routing information from the border leaf switches.

In a Cisco ACI Multi-Pod fabric, a pair of route reflectors is defined in each pod to perform this route reflector functionality internally to the pod that they belong to, as shown in the figure bellow. Then, MP-BGP between spine switches in each pod allows to exchange those L3Out routes with each pod.

Depending on the distance between each pod, Cisco ACI fabric can be designed in a way where each pod owns and uses its own L3Out to the external network domain instead of applying L3Outs from another pod, as shown in the figure below, which is typical for geographically separated pods. If the same L3Out route is learned from multiple L3Outs in different pods, endpoints deployed in a given pod would always use the local L3Out connection (lower routing metric to local border leaf switches) to send traffic to the L3Out route.

If all border leaf nodes that are deployed inside a certain pod fail, or in WAN failure scenarios in a certain pod, endpoints can still continue to communicate with the external Layer 3 domain, using the L3Out in remote pods.

![](/images/aciadvanced/aciadvanced_5_5_1.png)

When a certain pod loses its connection to the WAN, or in scenarios where a specific pod does not have a WAN connection, it can load-balance traffic on a per-flow basis using the L3Out connections available in other remote pods. By default, the leaf nodes in this pod have ECMP routes for the L3Out routes from remote pods pointing to the TEP of border leaf switches each remote pod.

This will be ECMP regardless of the actual physical location of the remote pods, since the TEP information for leaf switches in other pods is learned by the spine switches and always redistributed into the IS-IS protocol running inside the pod with the same default metric (value of 64), without considering the original OSPF metric of the routes. Therefore, the traffic is equally load-balanced on a per-flow basis using the L3Out connections in remote pods, disregard of the physical location.

The default behavior can be modified by applying a route-map to the external prefixes learned from the L3Outs. This route-map could be, for example, used to tune the local-preference for IP prefixes learned on different L3Out connections.

The following figure depicts an example for a Cisco ACI Multi-Pod connectivity to external Layer 3 networks, where the outbound Layer 3 traffic in specific pod is influenced by tuning of local preference:

![](/images/aciadvanced/aciadvanced_5_5_2.png)

In this example, the local preference is individually increased for IP prefixes that are learned from the L3Out connections in Pod 1 and Pod 2, so that an endpoint in Pod 3 can deterministically use the L3Out in Pod 1 to communicate with the external 198.51.100.0/24 destination, and the L3Out in Pod 2 to communicate with the external 203.0.113.0/24 destination.

In addition, the behavior of endpoints in Pod 3 also applies to the endpoints deployed in Pod 1 and Pod 2. Hence, the outbound traffic that is destined to 198.51.100.0/24 that originated from endpoints that are connected to the ACI Multi-Pod fabric will always prefer the L3Out connection in Pod 1 independently from the specific pod where those endpoints are connected (except from endpoints that are connected to the border leaf switches that always prefer the local L3Out connection to communicate to all the IP prefixes received from the external routers). Similarly, the traffic that is destined to 203.0.113.0/24 will always prefer the L3Out connection in Pod 2.

If endpoints in the same bridge domain are spread across all pods, inbound traffic may be received through any of the deployed L3Out connections. This is the case assuming that the bridge domain IP subnet is announced via all the L3Outs deployed across pods (which is normally a best practice to provide resilient inbound paths), and may lead to the creation of the asymmetric communication. Asymmetric traffic patterns can cause problems when deploying stateful perimeter services (such as firewalls) between the Cisco ACI fabric and the WAN. In such situation, Host Route Advertisement feature can be used. This feature allows L3Outs to advertise a host route (/32 or /128) for endpoint IPs that are locally learned in the same pod, on top of the BD subnet itself. This means only L3Outs that are in a same pod as the destination endpoint advertise the host route for the endpoint IP. With this, traffic from external routed domain will prefer L3Outs that are in a same pod as the destination endpoint.

**Note:** For details on host route advertisement, see Advertise Host Routes section in the Cisco APIC Layer 3 Networking Configuration Guide, Release 5.2(x), available at: https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/l3-configuration/cisco-apic-layer-3-networking-configuration-guide-52x/m_routed_connectivity_to_external_networks_v2.html

##  5.6)    Service Node Integration Considerations

Cisco ACI offers the capability to insert Layer 4 to Layer 7 services, for example, firewalls, load balancers, and intrusion prevention systems (IPSs), using a manual configuration of bridge domains and EPGs, or with a managed-mode service graph or an unmanaged-mode service graph. Similarly, you can integrate service nodes with Cisco ACI Multi-Pod fabrics, using the available deployment options, while the integration options can depend on the chosen design.

Several deployment models are available for integrating network services in a Cisco ACI Multi-Pod fabric. To determine the best options to choose, you should consider all the specific requirements and characteristics of the design, more precisely:

*   Service node location and function
  
    *   North-south service node (or perimeter service node), for controlling communications between the data center and the external Layer 3 network domain. An example is a firewall that protects web servers from an external network.
    *   East-west service node, for applying policies for traffic flows within the data center. For the east-west enforcement, there are two cases to consider:
        *   The service node, such as firewall, is used to apply policies between EPGs that are part of the same VRF.
        *   The service node, such as firewall (or firewall context), is front-ending each tenant/VRF (very commonly deployed), which enables you to apply security policies to all inter-VRF traffic. This option can be used also to apply north-south policies when the external network domain is also reachable by a VRF through a firewall.

*   Service node mode of operation
    *   Transparent (Layer 2 mode)
    *   Routed as default gateway for the endpoints
    *   Routed with L3Out peering
    *   Routed with PBR

*   Service node high-availability model
    *   Active-standby service node pair stretched across pods
    *   Active-active service node cluster stretched across separate pods (supported from Cisco ACI Release 3.2(4d))
    *   Independent active-standby service node pair in each pod

*   Connectivity to the external Layer 3 network domain
    *   Traditional L3Outs deployed on the border leaf nodes
    *   Layer 3 EVPN services over fabric WAN

Cisco Adaptive Security Appliance (ASA) and Cisco Firepower Next-Generation Firewalls (NGFW) clustering allows you to group multiple nodes as a single logical device to provide high availability and scalability. These devices support two main clustering options, active/standby and active/active, which also influence the deployment options when they are integrated as service nodes in Cisco ACI Multi-Pod environments.

When integrating service nodes with Cisco ACI Multi-Pod fabrics, such as firewalls, the following provides more details for the options depending on the chosen high-availability model:

*   **Active-standby firewalls pair stretched across pods:** This model can be applied to both north-south and east-west traffic flows. This fail-safe model does not allow the creation of an asymmetric traffic path that could lead to communication drops. At the same time, because of the existence of a single active service node that is connected to the Cisco ACI Multi-Pod fabric, this option has certain traffic-path inefficiencies, because by design some traffic flows will hairpin across the IPN. Therefore, you should be sure to properly dimension the bandwidth available across pods and consider the possible latency impact on application components that are connected to separate pods.

The active-standby model is supported with service nodes that are deployed in transparent or routed mode with independent L3Out connectivity of the external Layer 3 domain. Both the traditional L3Outs deployed on the border leaf nodes and Layer 3 EVPN services over fabric WAN are supported.

![](/images/aciadvanced/aciadvanced_5_6_1.png)

*   **Active-active firewall cluster stretched across pods:** Beginning with ACI Release 3.2(4d), an active/active firewall cluster can be stretched across pods. When deploying Cisco ASA or Cisco NGFW, this deployment model takes the name of “split spanned EtherChannel” and ensures that all the nodes of the cluster “own” the same MAC/IP values so that the stretched firewall cluster appears as a single logical entity to the Cisco ACI Multi-Pod fabric. This deployment model removes any concern about the possible creation of asymmetric traffic paths for both east-west and north-south traffic flows, as traffic can be dynamically redirected to the specific firewall node owning the connection state for that specific traffic flow. Still, since active/active firewalls can share session state, asymmetric traffic paths are also possible.

![](/images/aciadvanced/aciadvanced_5_6_2.png)

*   **Independent active-standby firewalls pair in each pod:** This model mandates that symmetric traffic flows through the service nodes be maintained because the connection state is not synchronized between independent nodes. This requirement can be achieved with the following approaches:

    *   Use symmetric policy-based routing (PBR) with service nodes that are deployed in routed mode only, for both north-south and east-west security policy enforcement (recommended approach). It consists of defining a PBR policy that specifies multiple active service nodes. The Cisco Nexus 9000 Series Switches (EX platform or newer), used as leaf nodes, would then apply the symmetric PBR policy, selecting one of the available nodes for the two directions of each given traffic flow (based on hashing). Both the traditional L3Outs deployed on the border leaf nodes and the Layer 3 EVPN services over fabric WAN are supported.

    *   If symmetric PBR is not possible, only for perimeter firewalls (north-south traffic flows), it is necessary to keep ingress and egress traffic flows optimized and symmetric. This can be achieved by enabling granular host-route advertisement towards the external Layer 3 domain to ensure that ingress traffic paths are always delivered in the “right pod” where the destination endpoint is connected. Similarly, firewall nodes can be deployed in routed mode. From Cisco ACI Release 4.0 onwards, host-route advertisement is extended to traditional L3Outs deployed on border leaf nodes, so this option is available for traditional L3Outs and Layer 3 EVPN services over fabric WAN.

![](/images/aciadvanced/aciadvanced_5_6_3.png)

The following table summarizes these options and considerations for Cisco ACI Multi-Pod and service node integration:

|                  | Active-standby firewall nodes stretched across pods (north-south) | Active-standby firewall nodes stretched across pods (east-west) | Active-active firewall cluster stretched across pods (north-south or east-west) | Active-standby firewall nodes per pod (north-south)                    | Active-standby firewall nodes per pod (east-west) |
|------------------|-------------------------------------------------------------------|-----------------------------------------------------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------|---------------------------------------------------|
| Transparent mode | Yes                                                               | Yes                                                             | No                                                                              | No                                                                     | No                                                |
| Routed mode      | Yes                                                               | Yes                                                             | Yes (from ACI 3.2(4d)) with PBR                                                 | Yes (symmetric PBR or requirement for ingress/egress traffic symmetry) | Yes (symmetric PBR only)                          |

##  5.7)    Service Graph Considerations

Similarly to standard Cisco ACI deployments, you can use service graph with Cisco ACI Multi-Pod to redirect traffic between security zones to a firewall or a load balancer, without the need for the firewall or the load balancer to be the default gateway for the servers. When designing a service graph for service node integration across pods, you should consider all the specific requirements and characteristics of the design. Even though you can implement specific designs without a service graph, some of them mandate the utilization of service graph for service node integration in Cisco ACI Multi-Pod.

For example, when designing a service graph for insertion of service node, you should take into account traffic flows and associated deployment considerations, such as these three most CiscoWorks Common Service node insertion use cases:

*   **North-south perimeter firewall:** Traffic flows between the external Layer 3 network domain and the web EPG.

*   **East-west firewall (intra-VRF):** Traffic flows from the web EPG to the application EPG and from the application EPG to the database EPG.

![](/images/aciadvanced/aciadvanced_5_7_1.png)

*   **East-west firewall (inter-VRF):** Traffic flows between EPGs that are part of different VRFs when the firewall is front-ending each VRF.

![](/images/aciadvanced/aciadvanced_5_7_2.png)

Service graph option in Cisco ACI Multi-Pod fabric can help you integrate service nodes in the following commonly used high-availability models:

*   Active-standby service node pair stretched across pods

    *   **Transparent firewall:** The inserted service node bridges two bridge domains. The external network traffic (north-south) or between internal endpoints (east-west) is processed by the active service node pod, which operates in transparent mode.

    *   **Routed firewall as default gateway for the endpoints:** Cisco ACI fabric offers Layer 2 connectivity services to allow the endpoints to communicate with their default gateway (the firewall) and for the firewall to route traffic between endpoints belonging to separate IP subnets and between the endpoints and the external Layer 3 network domain.

    *   **Routed firewall with L3Out peering:** Integrates a north-south routed firewall when the firewall nodes are connected to the Cisco ACI fabric via L3Out peering, or provides east-west routed firewall insertion with L3Out peering, where the routed path between the web EPG and app EPG always goes through the active firewall, no matter in which pod it is deployed.

    *   **Routed firewall with PBR:** Facilitates north-south routed firewall or east-west routed firewall insertion with PBR.

*   Active-active service node cluster stretched across pods

    *   **Routed firewall with PBR:** Uses Cisco ACI fabric for Layer 2 and Layer 3 forwarding and only redirects traffic of interest (as defined in PBR) toward the anycast service. Depending on the type of traffic, the service graph with PBR can be used in:

        *   North-south use case, where the endpoints that are connected to the Cisco ACI fabric use the fabric itself as the default gateway. Traffic that is destined to those endpoints and originating from the external Layer 3 domain (or vice versa) is redirected to the firewall cluster with the configured PBR policy.

        *   East-west use case, where communication can be achieved between EPGs that are part of the same VRF or part of different VRFs. For the intra-VRF communication, the PBR policy is usually applied on the inbound leaf where the endpoint sourcing the traffic is connected, while for the inter-VRF scenario, the PBR policy is instead always applied on the consumer VRF.

*   Independent active-standby service node pair that is connected to separate pods
    *   **Routed firewall with L3Out peering:** It is critical to ensure that inbound traffic is always delivered to the “right pod,” meaning to the pod where the destination endpoint is connected, which can be achieved by using more granular host route advertisement into the WAN. This option is suitable for north-south perimeter service node integration.

    *   **Routed firewall with symmetric PBR:** The Cisco ACI PBR can associate multiple instances of service nodes with the same PBR policy and can load-balance different traffic flows across those nodes. Ingress and egress traffic flows can then be redirected to the same PBR node, using the symmetric PBR function. Similarly, this option can be utilized for north-south routed firewall and east-west routed firewall insertion.

### Active-Standby Firewalls Stretched Across Pods

One of the most common options is the deployment of an active-standby service node pair in different pods. All traffic for communication with the external network (north-south) or between internal endpoints (east-west) must be hair-pinned to the pod in which the active service node is located.

A separate physical (or virtual) firewall can be deployed for each tenant (VRF instance) that requires firewall policy enforcement. Each firewall node can be connected in a Virtual Port Channel (vPC) to a pair of Cisco ACI leaf nodes, or to only a single leaf node (with a local port channel).

### Multi-Pod Flow Consideration Scenario 1: Transparent Firewall

You can implement active-standby transparent firewalls pair that is stretched across pods for the north-south or east-west traffic protection.

The following figure depicts the typical Cisco ACI network design for insertion of a north-south transparent firewall, which you can use for all service graph models, and without a service graph. To make traffic go through a transparent firewall, you use a bridge domain “sandwich” design.

![](/images/aciadvanced/aciadvanced_5_7_3.png)

The default gateway for the web server part of the Web EPG is deployed in the Cisco ACI fabric and is available in the bridge domain (BD1) in which the firewall external interface is connected. This bridge domain (BD1) is different from the one (BD2) in which the web server is located. This configuration forces all traffic that is destined for external destinations to traverse the transparent firewall. In addition, the traffic is enforced through the active firewall that is based on Layer 2 lookup operations that are performed by the Cisco ACI leaf nodes either on the traffic that is received from the endpoint (south-to-north traffic) or on the traffic that is routed to the destination IP subnet (north-to-south traffic).

When you use traditional L3Out for external network connectivity, you need to pay attention at which pod may receive the packet from L3Out since the packet may be received on a pod that does not have active firewall. As mentioned in “Connectivity to External Layer 3 Networks” topic, Host Route Advertisement feature should be used in such scenario. The following figure shows Host Route Advertisement feature advertise host routes for endpoint IP on top of BD subnet so that traffic can be received on the pod with active firewall. This prevents packets to go back and forth inefficiently between pods.

![](/images/aciadvanced/aciadvanced_5_7_4.png)

### Multi-Pod Flow Consideration Scenario 2: Routed Firewall with L3Out Peering with the ACI

You can implement routed firewalls that are stretched across pods with L3Out peering for the north-south or east-west traffic protection.

The following figure depicts the Cisco ACI network design for insertion of a north-south routed firewall when the firewall nodes are connected to the Cisco ACI fabric through L3Out peering. You can use any service graph option or no service graph at all for this design.

![](/images/aciadvanced/aciadvanced_5_7_5.png)

The firewall is deployed as a routed Layer 3 hop at the front end of the VRF instance. Also, the anycast gateway function that is offered by the Cisco ACI fabric (bridge domain subnet) is utilized. The communication within VRF instances won’t need to traverse the perimeter firewall, which, instead, applies security policies only on north-south communication to other VRF instances or to the external Layer 3 domain.

In this model, L3Out connections are established between the fabric and the internal and external firewall interfaces. A classic VRF sandwich configuration is hence required to enforce traffic through the routed firewall. The web subnet and the IP subnet of the firewall internal interface are associated with a firewall inside the VRF2 instance. The firewall outside interface and the Layer 3 interface to the WAN edge router are instead part of the VRF1 instance. The traffic is enforced through the active firewall based on Layer 3 lookup operations that the Cisco ACI leaf nodes perform.

You can use two options when creating the L3Out connections between the Cisco ACI leaf nodes and the active-standby service nodes that are deployed across pods:

*   Separate L3Out connections for each pod, each including the local pair of leaf nodes to which the firewall is connected.

*   Single L3Out connection including all four-leaf nodes that are deployed across pods to which the active-standby firewalls are connected (recommended approach).

In this use case, a service leaf implies a border leaf that provides connectivity to a service appliance such as firewall.

The use of a single L3Out connection for the service leaf nodes located in separate pods implies the extension of the external bridge domain that is created internally for an L3Out across pods. In this model, the service leaf pairs in Pod 1 and Pod 2 (Pod1-sLeaf, Pod2-sLeaf) both can establish routing adjacencies with the active firewall and exchange routing information with it, as shown in this figure.

![](/images/aciadvanced/aciadvanced_5_7_6.png)

The peering between the active firewall and the service leaf nodes across pods implies that both pairs of service leaf switches learn the external routes directly from the active firewall and the external routes will be distributed to infra MP-BGP VPNv4 from service leaf switches in both pods. Because of this, computing leaf nodes deployed in the pod with the standby firewall would always first send the traffic to the local service leaf nodes since these are also distributing the external routes directly into ACI fabric. In the pod where the standby firewall is residing, the service leaf switches would then need to bounce the traffic to the service leaf nodes connected to the active firewall in the remote pod. This deployment option improves the convergence time for a firewall failover event, because routing adjacencies with the newly activated firewall node do not need to be reestablished.

Be sure to note that this capability to bounce traffic across pods when the firewall nodes are connected in vPC mode to the Cisco ACI leaf nodes is supported only starting from Cisco ACI releases 2.1(3), 2.2(3), 2.3(1), and 3.0(1) and requires Cisco Nexus 9000 Series EX or FX platform hardware for the leaf switches on which the firewall is connected.

You can deploy an alternative design, supported instead from Cisco ACI releases 2.1(2), 2.2(2), 2.3(1), and 3.0(1), on all Cisco ACI leaf switches by connecting the firewall node to a single leaf node (either with an individual physical interface or with a port channel), as shown in figure below.

![](/images/aciadvanced/aciadvanced_5_7_7.png)

The following figure shows the typical Cisco ACI network design for east-west routed firewall insertion with L3Out peering. The difference from the previous scenario is simply both the ends are EPG this time. You can use this illustration when creating an unmanaged-mode service graph or managed-mode service graph in Cisco ACI Multi-Pod deployments. This design can also be implemented without service graph if preferable.

![](/images/aciadvanced/aciadvanced_5_7_8.png)

The traffic flow and considerations are exactly same as the previous north-south scenario except the destination is EPG instead of another L3Out towards WAN. The traffic from App VM2 first goes to the local service leaf on pod 2 and then it is bounced to the pod with the active firewall. However, the opposite flow (from Web VM1 to App VM2) goes to the active firewall in the same pod and then directly goes to the destination App VM2 without bouncing on the service leaf switches on pod 2.

![](/images/aciadvanced/aciadvanced_5_7_9.png)

### Multi-Pod Flow Consideration Scenario 3: Routed Firewall with Symmetric PBR

The PBR usage with Cisco ACI mandates the use of a service graph. So, you must use an unmanaged-mode service graph or managed-mode service graph with this design option for north-south or east-west traffic.

The following figure shows the typical Cisco ACI network design for north-south routed firewall insertion with PBR. The use of PBR significantly simplifies the configuration, because the previously described VRF sandwich configuration is not required anymore. The traffic is instead redirected to the active service node based on the configured policy.

![](/images/aciadvanced/aciadvanced_5_7_10.png)

The PBR configuration helps ensure that traffic originating from the external Layer 3 domain always is forced through the active firewall node before being delivered to the web endpoint destination. This routing occurs based on the configured policy, independent of the information that is contained in the routing table of the leaf nodes, which instead would mislead you to believe that communication can occur directly because the L3Out and the web bridge domain are part of the same routing domain (VRF1).

When you use a traditional L3Out connection, the web server subnet is advertised through border leaf nodes in all pods. Depending on the routing information in the WAN, incoming traffic will be steered toward one of the pods as mentioned in the Connectivity for External Layer 3 network along with Host Route Advertisement feature. Then the PBR policy will be applied on the leaf node that knows both the consumer and provider EPG class IDs, because the PBR policy is based on the source and destination EPGs and the filter configured in the contract. Then, traffic will be redirected to the active service node, and after that it finally reaches the destination endpoint.

In the following example, the destination Web VM 1 is in the same pod as the active firewall. And traffic comes into the same pod via L3Out. In this case, there is no hair-pinning across the IPN, as shown in this figure:

![](/images/aciadvanced/aciadvanced_5_7_11.png)

**Note:** The PBR policy is applied on a leaf where a contract policy is applied that depends on some parameters and configurations such as Policy Enforcement Direction configuration in VRF. In this example, it is using Ingress Policy Enforcement Direction that caused a contract to be applied on the computer leaf instead of the border leaf.

In scenarios where inbound traffic is instead received in the pod where the destination endpoints are not connected, traffic will be forced to hairpin across the IPN. This is because the firewall service is deployed in active/standby mode across the pods.

For the outgoing traffic (Web VM1 to WAN), the PBR policy can be directly applied on the leaf node to which the web VM1 is connected. The traffic is henceforth redirected to the active service node in the same pod and then goes back to the external Layer 3 domain.

The following figure depicts the typical Cisco ACI network design for east-west routed firewall insertion with PBR when the EPGs are part of the same VRF. This design is similar to that for the north-south routed firewall with symmetric PBR use case, and also mandates the use of a service graph. The internal and external interfaces of the firewall nodes are connected to two dedicated bridge domains, different from the ones in which the consumer and provider endpoints (EPG Web and App) are deployed.

![](/images/aciadvanced/aciadvanced_5_7_12.png)

If the source endpoint, the destination endpoint, and the active service node are in the same pod, traffic remains confined within the pod. However, if either the source or destination endpoints are not in the same pod as the active service node and the PBR policy is applied on the destination leaf, traffic will hairpin twice across the IPN, as depicted in this figure.

![](/images/aciadvanced/aciadvanced_5_7_13.png)

For more information about the Cisco ACI Multi-Pod provisioning and setting up, please visit the Multi-Pod chapter in the Cisco APIC Layer 3 Networking Configuration Guide, Release 5.2(x), available at: https://www.cisco.com/c/en/us/td/docs/dcn/aci/apic/5x/l3-configuration/cisco-apic-layer-3-networking-configuration-guide-52x/m_multipod_v2.html

For more details about Cisco ACI Multi-Pod configuration, please visit the Cisco Application-Centric Infrastructure Multi-Pod Configuration White Paper, available at: https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739714.html.

For comprehensive information about Cisco ACI Multi-Pod, including Multi-Pod use cases and supported topologies, IPN requirements and control plane, spine switches and IPN connectivity considerations, packet flow between pods, as well as connectivity to external Layer 3 networks, please visit the ACI Multi-Pod White Paper, available at: https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-737855.html.

For more information about service node integration and different high-availability models, as well as service graphs considerations during Cisco ACI Multi-Pod deployment, please visit the Cisco ACI Multi-Pod and Service Node Integration White Paper, available at: https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739571.html.

#   6)  Describing Cisco ACI MultiSite Deployment

##  6.1)    Introduction

Cisco Application-Centric Infrastructure (ACI) MultiSite is an architectural approach that enables you to interconnect separate Cisco ACI fabrics with independent Cisco Application Policy Infrastructure Controller (APIC) cluster domains. Therefore, you can create a group of flexible Cisco ACI islands that can be seen and operated as a single logical entity (fabric), while each Cisco ACI fabric represents a separate availability zone. In addition, you can establish multitenant Layer 2 and Layer 3 network connectivity across sites using an end-to-end policy across the entire system.

You will learn about the Cisco ACI MultiSite deployment, which uses a Cisco Nexus Dashboard Orchestrator (NDO), formerly known as MultiSite Orchestrator (MSO) that pushes the cross-fabric configuration to multiple APIC clusters. You will go through the intersite Network (ISN) requirements, to facilitate connection between sites. While exploring the communication in Cisco ACI MultiSite, you will get familiar with how unicast and broadcast, unknown unicast, and multicast (BUM) traffic is handled across sites. In addition, you will go through several Cisco ACI MultiSite use cases to understand the stretched component options across sites. Finally, you will observe the differences between Cisco ACI MultiSite and Multi-Pod deployments and understand when to choose the Cisco ACI MultiSite option according to different requirements.

##  6.2)    Cisco ACI MultiSite Overview

Using Cisco ACI MultiSite architectures, organizations can deploy applications across data center fabrics, representing separate availability zones. This setup is critical to help ensure that any network-level failures and configuration or policy definition errors that occur in one availability zone will not ever be propagated to the application’s workloads running in a separate availability zone, which is essentially guaranteeing business continuance.

This design is achieved by using the following functional components:

*   **Cisco Nexus Dashboard Orchestrator (NDO):** This component is the intersite policy manager. It is an orchestrator that is positioned on the top of the Cisco APICs in different sites, providing a single-pane management. You deploy the Cisco Nexus Dashboard Orchestrator as an application in Cisco Nexus Dashboard.

*   **Intersite control plane:** Endpoint reachability information is exchanged between sites using a Multiprotocol Border Gateway Protocol (MP-BGP) Ethernet VPN (EVPN) connection, which is established between the spine nodes that are deployed in separate fabrics that are managed by the same instance of Cisco NDO. External routes information via L3Outs can be exchanged between sites starting from the APIC release 4.2(1). This feature is called intersite Layer 3 Outside (L3Out). Prior to the intersite L3Out, each site must have its own L3Out locally.

*   **Intersite data plane:** All communication between endpoints connected to different sites is achieved by establishing site-to-site Virtual Extensible LAN (VXLAN) tunnels across a generic IP network that interconnects the sites.

The following figure provides an overview of the three functional components in Cisco ACI MultiSite deployments:

![](/images/aciadvanced/aciadvanced_6_2_1.png)

As a centralized management pane, Cisco ACI NDO is used when defining the intersite policies that you can deploy across separate Cisco ACI fabrics, while each site has a separate Cisco APIC cluster domain with associated configuration and policies.

The MP-BGP EVPN control plane between sites enables the exchange of MAC and IP address information for the endpoints that communicate across sites, while VXLAN data plane is used to allow intersite Layer 2 and Layer 3 communication.

In a Cisco ACI fabric, two values identify the network and policy information, which are locally significant to the fabric. The VXLAN ID (VNID) identifies the bridge domain for Layer 2 communication or the VRF instance of the endpoint sourcing Layer 3 traffic, while the class ID is associated with EPGs, as contract-related information that used during policy enforcement. Class ID is a property that is known in the Cisco ACI Management Information Tree (MIT) as the "pcTag."

The Cisco ACI MultiSite facilitates end-to-end policy definition and enforcement using the Cisco ACI NDO. Since the Cisco ACI MultiSite deployment contains completely separate and independent Cisco APIC domains and fabrics, a translation function must be applied for the traffic between sites. This function is applied to ensure that a Cisco ACI NDO can orchestrate not only a brand-new ACI fabric but also existing ACI fabrics that may be already using overlapping VNIDs and class IDs in each site. The translation logic ensures that locally significant values for the VNID and class ID of each site, which identify the same bridge domain, VRF instance, and source endpoint group (EPG), can still be used within each site without worrying about the conflict with existing IDs in each site. The following figure illustrates the name-space translation in a Cisco ACI MultiSite deployment:

![](/images/aciadvanced/aciadvanced_6_2_2.png)

The diagram shows an example of traffic within the same EPG across two sites. When the traffic is across two EPGs, NDO defines a contract between those two EPGs. If one of the EPGs exists only in one site, the shadow service EPG of the EPG is created in the other site so that the other site can perform the EPG class ID translation for the EPG that doesn't exist in that site, and apply the contract between the EPG in site 1 and another EPG in site 2.

The name-space translation function is performed at line rate to avoid negatively affecting the performance of intersite communication. Cisco ACI NDO instructs the local Cisco APICs to program the translation tables on the spine nodes. This operation is the biggest difference between using Cisco ACI NDO and other automation or orchestration tools (such as Ansible) to manage multiple sites (fabrics).

##  6.3)    Cisco Nexus Dashboard Orchestrator

Cisco NDO enables you to provision, monitor the health status, and manage the full lifecycle of Cisco ACI networking policies and stretched tenant policies across Cisco ACI sites. These policies can be pushed to the different Cisco APIC domains for rendering them on the physical switches building those fabrics. It thus provides a high degree of control over when and where to push those policies, hence allowing the tenant change domain separation that uniquely characterizes the Cisco ACI MultiSite architecture.

### Cisco NDO Deployment

You must deploy the Cisco NDO as an application in Cisco Nexus Dashboard.

Cisco Nexus Dashboard is a central management console for multiple data center sites and a common platform for hosting Cisco data center applications, such as the Nexus Dashboard Orchestrator or Nexus Insights.

While Release 3.2(1) supported only the physical form factor of Nexus Dashboard, Release 3.3(1) and later can be deployed in physical, virtual (VMware ESX), or cloud (Amazon Web Services or Microsoft Azure) Nexus Dashboard clusters.

Each Nexus Dashboard cluster consists of 3 master nodes. For physical Nexus Dashboard clusters, you can also provision up to 4 worker nodes to enable horizontal scaling and up to 2 standby nodes for easy cluster recovery in case of a master node failure. For virtual and cloud clusters, only the base 3-node cluster is supported.

A stable connection must exist between the Cisco ACI MultiSite cluster nodes when they are deployed over a WAN. Although the nodes in a Cisco ND cluster use TCP as a communication protocol that can retransmit a dropped packet over an unreliable media, you should apply the appropriate quality of service (QoS) policy and mark the differentiated services code point (DSCP) value of the node traffic (for example, as Expedited Forwarding [EF]) for proper traffic treatment. The supported round-trip time (RTT) latency between ND nodes is up to 150 msec, which you should take into account when splitting the nodes in different sites. The maximum distance from a Cisco ND cluster to a Cisco ACI fabric site can be up to 500 milliseconds RTT.

There are two main scenarios for the deployment of Cisco ND, which depend on the Cisco ACI MultiSite deployment use cases:

*   **Centralized (local) data center:** Requires the creation of separate availability zones in the same data center, usually in the same building or campus, which enables ultra-high port count for bare-metal server, virtual machine, or container connectivity. It allows you to use a high number of leaf nodes, which you can deploy across separate Cisco ACI fabrics and overcome the scalability limit of a single fabric, while controlling the whole infrastructure though a single Orchestrator.

![](/images/aciadvanced/aciadvanced_6_3_1.png)

*   **Geographically distributed data centers:** Data centers represent separate availability zones that are distributed across cities, countries, or continents, which you can control through a single pane for provisioning, monitoring, and management. As a best practice, you should always deploy the nodes in the MultiSite cluster as part of the same geographical region (United States, Europe, Asia, and so on), even when managing Cisco ACI fabrics that span the world.

![](/images/aciadvanced/aciadvanced_6_3_2.png)

### Cisco NDO Main Functions

Cisco NDO provides these main functions:

*   Use the dashboard to monitor the health, faults, and intersite policies for all the Cisco ACI fabrics interconnected in the Cisco MultiSite architecture. The health-score information is retrieved from each APIC cluster domain and presented in a unified way.

*   You add a Cisco APIC or Cloud APIC site using the Nexus Dashboard GUI and then enable that site to be managed by Nexus Dashboard Orchestrator.

*   Provision day-0 infrastructure to establish intersite MP-BGP EVPN between the spine switches at all Cisco ACI sites to peer and connect with each other. ISN devices are not configured by the NDO and the user needs to ensure OSPF towards spine switches and IP connectivity across sites are deployed correctly on ISN devices.

*   Create new tenants and deploy them in all the connected sites (or a subset of them).

*   Define policy templates via Schema. Each template can be associated with and pushed to a specific set of fabrics (sites).

*   Import tenant policies from an already deployed Cisco ACI fabric and stretch them to another, newly deployed site.

**Note:** Starting with Release 3.2(1), user management has moved to the common user and authentication management in the Nexus Dashboard where your NDO service is running.

The creation of intersite tenant policy profiles and their deployment across sites, is one of the most important Cisco NDO features, together with the capability to define and provision scoped policies for change management. When you define intersite policies, you can use policy templates, which can be associated with and pushed to a specific set of fabrics, as shown in this figure:

![](/images/aciadvanced/aciadvanced_6_3_3.png)

However, Cisco NDO is not responsible for configuring Cisco ACI site-local policies, which allows you to perform similar activities through the Cisco APIC cluster at each site. The NDO can import the relevant Cisco APIC cluster site-local policies and associate them with stretched objects. For example, you can import site-locally defined virtual machine manager (VMM) domains and associate them with stretched EPGs.

A schema in the Cisco NDO represents a collection of templates, which are used for defining policies, with each template assigned to a specific tenant. There are multiple approaches you can take when creating schema and template configurations specific to your deployment use case.

The simplest schema design approach is a single schema, single template deployment. You can create a single schema with a single template within it and add all VRFs, bridge domains, EPGs, contracts, and other elements to that template. You can then create a single application profile or multiple application profiles within the template and deploy it to one or more sites.

However, the supported number of schemas or templates per schema scalability limit may make this approach unsuitable for large-scale deployments, which could exceed those limits. Thus, you can use another approach to schema design that separates the networking objects from the application policy configuration. Networking objects include VRFs, bridge domains, and subnets, while the application policy objects include EPGs, contracts, filters, external EPGs, and service graphs.

When working with schemas, you can create new objects and push them out to one or more sites or you can import existing site-local objects and manage them using the Cisco NDO.

**Note:** When importing objects into Cisco NDO, their names must be unique across all sites. If you import different objects with duplicate names, it will cause a schema validation error and the import will fail. If you want to import objects that have the same name, you must first rename them.

### Cisco ACI MultiSite Day-0 Operations Overview

Before your Cisco APIC fabrics can be added in Cisco ND and managed by the Cisco NDO, you must configure the fabric-specific access policies on each site, so they can be connected to the ISN. Hence, you have to prepare the global fabric access policies (such as VLAN pool, AEP, and domain) for connecting the spine switch interfaces to the ISN for all the sites that will be part of the same MultiSite domain, and interface policies for the fabric's spine switch interfaces that connect to the ISN.

Then, you can add the sites to the Cisco ND to be managed by the Cisco NDO and provision the infrastructure. The day-0 operations of the Cisco ACI MultiSite using NDO follow these summary steps:

1.  Add sites in Cisco ND, while specifying the APIC's IP addresses and assigning one unique site ID for each site, and then enable that sites to be managed by Cisco NDO.

1.  Configure infra to register sites and configure fabric connectivity infra for the sites.

1.  Add tenants, while configuring new tenant definitions or importing a tenant from an existing site.

1.  Add schemas to implement policy templates to specific tenants.

### Cisco NDO User Interface

Cisco ND cluster uses the out-of-band (OOB) management network to connect to the APIC clusters deployed in different sites. It also provides northbound access through Representational State Transfer (REST) APIs or the user interface (HTTPS) for users to interact with the ND and NDO app running on ND cluster.

The Cisco NDO user interface is arranged according to different functionalities as depicted in this figure:

![](/images/aciadvanced/aciadvanced_6_3_4.png)

The Cisco NDO user interface has the following options:

*   **Dashboard:** Displays a list of all your site implementations and their current functionality and health. The dashboard has several functional areas:

    *   Site status, which indicates the status of each site (on a map or in table view), while providing APIC state, BGP sessions operational status regarding connectivity across the ISN, and color-coded state conditions displayed as critical (red), major (orange), minor (yellow), and warning (green) events for each site.
    *   Schema health, which provides a listing of your schemas with locales and health.

*   **Application Management > Tenants:** Lists all tenants that comprise your implementation, and their assignment to tenants, users, and schemas. The Tenants page includes an **Add Tenant** button for creation of a new tenant and an **Audit Log** icon for log details.

*   **Application Management > Schemas:** Lists all the schemas that are associated with your implementation, and provides an **Add Schema** button for creation of a new schema. Use this button to configure or import tenant policies, including the VRF, application profile with EPGs, filters and contracts, bridge domains, and external EPGs. It also provides an **Audit Log** icon to list log details for the Schemas page.

*   **Application Management > Policies:** Contains a table of all policies along with the summary of their types, tenants they're associated with, descriptions, and usage. You can use this page to add new policies or edit existing ones. You can configure DHCP, MPLS QoS, Route Map and Multicast Route Map polices.

*   **Operations:** provides and interface for Operations activities, such as Audit Logs, Backup & Restore, Sites Firmware, Tech Support and Remote Locations.

*   **Infrastructure:** provides infrastructure features, such as **System Configuration** to configure System Alias and Banner, Login Attempts and Lockout Time; **Sites** to display all the sites in your implementation, including their Cisco APIC URLs and managed mode; **Infra Configuration** to set up the control plane BGP over the ISN; and **SD-WAN** to add vManage controller to your Cisco NDO in order to import any configured SLA policies for the purpose of NDO and SD-WAN integration.

##  6.4)    Intersite Network Overview

The ISN can be a generic Layer 3 infrastructure that interconnects different sites in the Cisco ACI MultiSite solution. Still, it must meet several deployments considerations to provide connectivity between sites.

Since the ISN allows establishment of site-to-site VXLAN tunnels, it must support plain IP routing functionality. Therefore, you can use different solutions, from a simple single router device (or two for redundancy) as an ISN, to a complex network infrastructure spanning the world. In addition, starting from Cisco ACI Release 3.2(1), a back-to-back topology between spine switches in two sites is supported.

The spine interfaces are connected to the ISN devices through point-to-point routed subinterfaces with a fixed VLAN 4, which implies the need to define and support Layer 3 subinterfaces on both the spine switches and the directly connected ISN devices. It is hence critical to select ISN routers that allow the defining of multiple subinterfaces on the same device using the same VLAN tag 4 and still functioning as separate point-to-point Layer 3 links.

In addition, the ISN between sites must support these specific functionalities:

*   Increased maximum transmission unit (MTU) support to handle the VXLAN encapsulated traffic.

*   Open Shortest Path First (OSPF) support between spine switches and ISN routers.

*   QoS considerations for consistent QoS policy deployment across sites.

However, in Cisco ACI MultiSite deployments, the ISN does not need to support multicast to facilitate BUM traffic flows across sites. Instead, ACI uses headend replication for the flood traffic where the copy of the packet happens on the destination site. This process is much simpler than the inter-pod network (IPN) in Cisco ACI Multi-Pod deployments that requires Bidirectional Protocol Independent Multicast (PIM Bidir) support. It is very important because the interconnected fabrics may be deployed around the world and it would be difficult to ensure proper multicast support across the entire interconnecting network infrastructure.

In addition, you must use specific hardware for the spine switches that are connected to the ISN in Cisco ACI MultiSite deployment, such as Cisco Nexus EX platform or newer, since they must perform name-space translation function at line rate to avoid performance issues during intersite communication. Note that first-generation spine switches can coexist with this second-generation hardware, as long as the new models are the only ones connected to the ISN. ISN devices for MultiSite could be the same as IPN devices for Multi-Pod in case the site has Multi-Pod as well.

### Increased MTU Support

The ISN must support increased MTU on its links to allow site-to-site VXLAN traffic, since the VXLAN data-plane traffic adds 50 bytes of overhead (54 bytes if the IEEE 802.1Q header of the original frame is preserved).

So, you have to configure the MTU in the ISN to the higher value of these two requirements, while following the generic recommendation to add 100 bytes above that value:

*   MTU of MP-BGP EVPN control-plane communication between spine nodes in different sites.

*   Maximum data-plane MTU of the frames that are generated by the endpoints that are connected to the fabric.

For example, if the endpoints support jumbo frames (9000 bytes) or if you use the default settings on spine nodes that generate 9000-byte packets for exchanging endpoint routing information, you should configure the ISN to support an MTU size of at least 9100 bytes.

The default control plane MTU value can be tuned by modifying the corresponding system settings in each APIC domain, while navigating to **System > System Settings > Control Plane MTU**, as shown in the following figure:

![](/images/aciadvanced/aciadvanced_6_4_1.png)

### OSPF Support

The ISN uses OSPF while establishing peering with the spine nodes deployed in each site. Therefore, the OSPF settings under the OSPF policy in the Cisco ACI domains should match the ISN side. Even though OSPF peering is required between the spine switches and the ISN devices, you are not limited to use OSPF across the entire ISN infrastructure. It can be a generic Layer 3 infrastructure interconnecting the sites (for example, an MPLS network or internet) that can use a separate routing protocol inside that Layer 3 network, as long as mutual redistribution is used so it advertises OSPF routes that are learned from spine nodes in each site.

### QoS Considerations

When you deploy Cisco ACI MultiSite, you should ensure that traffic across sites is consistently prioritized (as it happens intrasite). Without any QoS policies configured at a site, the default DSCP value of the outer IP address of the VXLAN packet in the ISN is set to 0 (zero). Thus, you should configure a QoS DSCP marking policy on the spine nodes (CoS to DSCP) in each site to help ensure proper QoS treatment in the ISN.

The CoS-to-DSCP mappings are configured using the Cisco APIC in each site through **Tenant > infra > Policies > Protocol > DSCP class-cos translation policy for L3 traffic**, which modifies the default behavior. Hence, when the traffic leaves the source site, it can be properly treated inside the ISN (using QoS mechanisms), and it is appropriately re-mapped when it enters the destination site, as depicted in this figure:

![](/images/aciadvanced/aciadvanced_6_4_2.png)

### ISN Control Plane

The OSPF control plane is used to exchange routing information for specific IP addresses defined on the spine nodes between sites, while utilizing the following components:

*   **BGP-EVPN Router-ID (EVPN-RID):** A unique IP address that is defined on each spine node belonging to a fabric, which is used to establish MP-BGP EVPN adjacencies with the spine nodes in remote sites. This BGP Router-ID is the same as the one for Multi-Pod if Multi-Pod is already configured in the same site.

*   **Overlay Unicast TEP (O-UTEP):** A common anycast address that is shared by all the spine nodes in a pod at the same site, which uniquely identifies the site and the pod, and is used to source and receive unicast VXLAN data-plane traffic. This IP address is assigned per pod in each site.

*   **Overlay Multicast TEP (O-MTEP):** A common anycast address that is shared by all the spine nodes in the same site and is used to perform headend replication for BUM traffic (sourced from the O-UTEP address that is defined on the local spine nodes and destined for the O-MTEP of remote sites to which the given bridge domain is being stretched). This IP address is assigned per site.

These three types of IP addresses must be globally routable addresses.

![](/images/aciadvanced/aciadvanced_6_4_3.png)

EVPN-RID, O-UTEP, and O-MTEP IP addresses are the only prefixes that must be exchanged across sites to enable the intersite MP-BGP EVPN control plane and the VXLAN data plane. Therefore, the TEP pool prefixes used within each site, such as TEP pool 1 for Site 1 and TEP pool 2 for Site 2, do not need to be exchanged across sites to allow intersite communication. So there are no technical restrictions regarding how those pools should be assigned. However, the strong recommendation is not to assign overlapping TEP pools across separate sites so that your system is prepared for future functions that may require the exchange of TEP pool summary prefixes. In addition, as best practice you should ensure that those TEP pool prefixes are filtered on the first ISN device so that they are not injected into the ISN network (as they may overlap with the address space already deployed in the backbone of the network).

As previously mentioned, MP-BGP EVPN adjacencies are established between spine nodes belonging to different fabrics by using the EVPN-RID addresses. Both MP Interior BGP (MP-IBGP) and MP External BGP (MP-EBGP) sessions are supported, depending on the specific BGP autonomous system to which each site belongs.

When deploying EBGP sessions across sites, you can create only a full mesh of adjacencies, where each site’s spine switch connected to the ISN establishes EVPN peerings with all the remote spine switches. When IBGP is deployed across sites, you can instead decide whether to use a full mesh or to introduce route-reflector nodes, which should be placed in separate sites to help ensure resiliency. The route-reflector nodes peer with each other and with all the remote spine nodes.

**Note:** Starting from the APIC release 4.2(1), external routes learned from L3Outs can be exchanged between sites on top of endpoint information. This feature is called intersite L3Out. Prior to the inter-site L3Out, each site must have its own L3Out within the site because endpoints in each site can only use the L3Outs locally deployed in the same site to communicate with the outside of the ACI fabric (north-south traffic).

The following figure illustrates the use of route reflectors for MP-IBGP EVPN in Cisco ACI MultiSite deployments, while the route-reflector nodes peer with each other and with all the remote spine nodes:

![](/images/aciadvanced/aciadvanced_6_4_4.png)

**Note:** The RR nodes discussed above are used for the MP-BGP EVPN peerings established between spine nodes deployed in separate sites. They serve a different function from the internal RR nodes, which are always deployed for distributing to all leaf nodes that are part of the same fabric external IPv4/IPv6 prefixes learned on the L3Out logical connections.

You can configure the infra for intersite connectivity using the Cisco ND and NDO user interface while following these summary steps:

1.  Add sites in Cisco Nexus Dashboard first. From the left navigation menu, select **Sites**. In the top right of the main page, select **Actions > Add Site**. For **Site Type**, select **ACI** or **Cloud ACI** depending on the type of ACI fabric you are adding. Specify the Cisco APIC's IP address and user credentials, and unique site ID.

1.  Spine switch data from the registered site is automatically pulled and the BGP AS number is populated with the ACI MP-BGP AS number.

1.  From the Nexus Dashboard's **Services** page, open the **Nexus Dashboard Orchestrator** service. You will be automatically logged in using the Nexus Dashboard user's credentials.

1.  In the Nexus Dashboard Orchestrator GUI, manage the sites. From the left navigation menu, select **Infrastructure > Sites**. In the main pane, change the State from Unmanaged to **Managed** for each fabric that you want the NDO to manage.

1.  In the NDO, select **Infrastructure > Infra Configuration** and click **Configure Infra** to begin the configuration of the fabric connectivity infra for the sites.

1.  In the main list of the **Fabric Connectivity Infra** page, click General Settings to specify the Control Plane BGP parameters, such as BGP Peering Type (full-mesh or route-reflector), Keepalive Interval (Seconds), Hold Interval (Seconds), Stale Interval (Seconds), and so on.

1.  In the main list of the **Fabric Connectivity Infra** page, click on a specific site, to access three different configuration levels: Site, Pod, and Spine level.

1.  At **Site** level, perform the following actions:
   
    * Turn on the **ACI Multi-Site** knob to enable the site for MultiSite connectivity.
    * In the **Overlay Multicast TEP** field, enter the O-MTEP IP address.
    * In the **BGP Autonomous System Number** field, enter the BGP autonomous system number or the IP address, if you want to modify this setting.
    * In the **External Router Domain** choose an external router domain that you have created in the APIC user interface in each site just as for a standard L3Out.
    * In **Underlay Configuration** tab, configure **OSPF Area ID, OSPF Area Type**, and **OSPF Policies**.

1.  At Pod level, perform the following actions:

    *   In the Overlay Unicast TEP field, enter the O-UTEP IP address.

1.  At Spine level, perform the following actions:

    *   Configure the port using Add Port for OSPF connection towards the ISN.
    *   (Optional) Turn on the BGP peering knob to enable MP-BGP EVPN and in the BGP-EVPN ROUTER-ID field, enter the EVPN-RID IP address for that spine switch. The spine switch will automatically try to peer with all the other spine switches with the BGP peering on.
    *   (Optional) Turn on the Spine is route reflector knob if the spine switch acts as BGP route reflector.

1.  Click **Deploy** to apply the configuration.

This example shows a Cisco ACI MultiSite deployment between two sites with indicated EVPN-RID on the spine switch, and O-UTEP and O-MTEP IP addresses in Site 1 (San Francisco):

![](/images/aciadvanced/aciadvanced_6_4_5.png)

The following figure depicts the Sites of the **Fabric Connectivity Infra** page, accessed through the **Infrastructure > Infra Configuration > Configure Infra** option in the Cisco NDO user interface, with the three configuration levels: **Site, Pod,** and **Spine** level. It shows the EVPN-RID, O-UTEP, and O-MTEP IP addresses under site, pod, or spine level accordingly for the HQ site in San Francisco from the example above:

![](/images/aciadvanced/aciadvanced_6_4_6.png)

In the spine level, the **Spine is route reflector** knob is turned on, so the spine switch acts as BGP route reflector.

The deployment of applications (or application components) across different data centers often brings up the requirement of ensuring that all traffic leaving a data center location must be encrypted to ensure privacy and confidentiality to the communication established across sites. You can use a standard approach and deploy an ad-hoc encryption on the devices in the data-path or by enable network-based solutions like IP Security (IPsec) or MAC Security (MACsec). In all those scenarios, to secure the communication between data centers, deployment of additional hardware with specific functionalities is required.

Cisco ACI Release 4.0(1) introduces support for a security solution called "CloudSec," which resembles a "multihop MACsec" functionality allowing the encryption of communication between two VXLAN tunnel endpoint (VTEP) devices that are separated by a generic Layer 3 network. Hence, you can turn on the CloudSec encryption in the context of a Cisco ACI MultiSite architecture in the site level, which enables traffic encryption of all traffic leaving a local site through the local spine and entering a remote spine from the local spine nodes, without having to use additional devices for IPsec and so on.

**Note:** CloudSec encrypts the original packet, including the VXLAN header, which adds an extra 40 bytes for the CloudSec header. Therefore, the MTU settings in the ISN must take into account this increase as well.

##  6.5)    Tenant Configuration Deployment from NDO

Before you can create and deploy a schema as policy template in the Cisco NDO, you must have at least one available tenant that you want to incorporate into your sites.

You can create tenants in Cisco NDO in one of two ways:

*   Create a tenant, while choosing **Application Management > Tenants** in the NDO user interface menu, and in the main pane, click **Add Tenant**. In the **Add Tenant** page, you have to specify the name of the tenant and associate it with sites.

*   Import a fully configured tenant from an APIC site, while choosing **Infrastructure > Sites** in the NDO user interface menu, and in the main pane of a specific site option, choose **Import Tenants**.

The following figure depicts the **Add Tenants** page, for the tenant creation for a specific site:

![](/images/aciadvanced/aciadvanced_6_5_1.png)

When working with schemas as policy templates, you can create new objects in a schema and push them out to one or more sites, or you can import existing site-local objects and manage them using the NDO.

To import one or more existing objects in a schema, follow these steps:

1.  On the **Application Management > Schemas** page in the NDO user interface, select (or create) the schema where you want to import objects.

1.  In the left sidebar, select the template where you want to import objects.

1.  In the main pane, click the **Import** button.

1.  Select the site from which you want to import objects.

1.  In the **Import** window that opens, select one or more objects you want to import from a specific site, as depicted in this figure:

![](/images/aciadvanced/aciadvanced_6_5_2.png)

The chosen site-local objects are populated within the schema in the NDO, which can be further configured according to your needs.

The following networking and application policy objects can be created within a schema:

*   Application profiles and EPGs
*   VRFs
*   Bridge domains with subnets and stretched or site-local settings
*   Contracts and Filters
*   L3Outs
*   External EPGs
*   Service Graphs

This figure shows a basic scenario for Cisco ACI MultiSite deployment, which provides connectivity between EPGs present in two sites:

![](/images/aciadvanced/aciadvanced_6_5_3.png)

Once the sites are added in ND and managed by the NDO, the tenant is configured, and the infra connectivity is established, perform the following summary steps to allow traffic flows between the EPGs in both sites:

1.  Import the existing site-local objects from the sites, including EPGs, BDs, and so on in the schema template.
1.  Assign the template to both sites.
1.  Create a contract between the referred EPGs in the schema template, to control traffic flow across sites.
1.  Deploy the schema to both sites.

##  6.6)    Packet Flow Between Sites

As opposed to the overlay control and data plane within a single ACI fabric, the ACI MultiSite deployment uses different overlay control and data plane functionalities for connecting endpoints that are deployed across different sites. MP-BGP EVPN is used as a control plane between spine switches for exchanging host information for discovered endpoints that are part of separate fabrics to allow east-west communication between these endpoints. After endpoint information is exchanged across sites, the VXLAN data plane is used to allow intersite Layer 2 and Layer 3 communication.

### MultiSite Overlay Control Plane

For the endpoints in different sites to communicate to each other across the ISN, the endpoint information needs to be shared across sites. Consequentially, the ACI MultiSite uses MP-BGP EVPN between spine switches across sites. The endpoint information is shared across sites only when the NDO configuration indicates that the endpoints need to communicate with the other site. For instance, the endpoint belongs to the EPG that is stretched across sites, or the endpoint belongs to the non-stretched EPG with a contract that allows the communication to the EPG on another site. In such scenarios, the endpoints with IP addresses are shared across sites via MP-BGP EVPN. However, the endpoints without IP addresses (such as a Layer 2 endpoint that only has a MAC address) are not shared until Layer 2 STRETCH is enabled on the NDO. These optimizations in endpoint sharing allow the ACI MultiSite to manage the larger number of endpoints in each site.

![](/images/aciadvanced/aciadvanced_6_6_1.png)

The following shows the detailed steps of how MP-BGP EVPN is used to share such endpoint information across sites:

![](/images/aciadvanced/aciadvanced_6_6_2.png)

The overlay control plane events for the exchange of host information across sites follow this sequence:

1.  Endpoints EP1 and EP2 connect to separate Sites 1 and 2.

1.  The endpoints are locally learned by the leaf node in their sites, and the leaf nodes originate a COOP control-plane message for endpoint information to the spine nodes.

1.  Spine nodes in both sites learn about the locally connected endpoints at the leaf nodes. Still, this information is not yet exchanged across sites for EP1 and EP2 EPGs because there is no policy in place that allows communication between them.

1.  An intersite policy is defined in the Cisco MultiSite Orchestrator, which is pushed and rendered in the two sites.

1.  The intersite policy triggers Type-2 EVPN updates across sites to exchange EP1 and EP2 host route information, which is always associated with the O-UTEP address that identifies their site. Thus, when you move an endpoint in a site between leaf nodes, the spine nodes will not generate additional EVPN updates, until the endpoint is migrated to a different site.

The received MP-BGP EVPN information is synced (via COOP) with the other local spine nodes that are not BGP intersite peers.

### MultiSite Overlay Data Plane

Cisco ACI MultiSite enables proper handling of BUM traffic between sites. It also enables intrasubnet unicast and intersubnet unicast communication between sites.

### BUM Traffic Between Sites

The deployment of VXLAN tunnels between the endpoints in different sites creates a logical abstraction over the ISN, which can have multiple Layer 3 hops, so they can communicate as they are part of the same logical Layer 2 domain. Thus, those endpoints can send Layer 2 BUM frames between sites to other endpoints connected to the same Layer 2 segment, regardless of their actual physical location.

Cisco ACI MultiSite uses ingress replication and headend replication for BUM frames, which is the reason why the ISN does not need to support multicast routing. In ingress replication, the spine node in the source site creates copies of the BUM frame, one for each destination site on which the Layer 2 domain is extended. Those copies are sent towards O-MTEP of each site. These packets are handled as unicast traffic in the ISN because the O-MTEP in the outer encapsulation is a unicast IP address. Once the BUM frame encapsulated with a unicast O-MTEP reaches the destination site, the destination site replicates the BUM frame and floods it within the site, which is the headend replication.

The BUM traffic is forwarded across sites in the way mentioned above only when **Intersite BUM Traffic Allow** is enabled on the bridge domain. If the stretched bridge domain has ARP Flooding enabled or Layer 2 Unknown Unicast is set to flood, **Intersite BUM Traffic Allow** needs to be enabled so that the flood traffic can be flooded to the other sites as well.

![](/images/aciadvanced/aciadvanced_6_6_3.png)

There are three types of Layer 2 BUM traffic that are forwarded across sites when **Intersite BUM Traffic Allow** is enabled on the bridge domain:

*   **Layer 2 Broadcast frames (B):** The frames are always forwarded across sites when Intersite BUM Traffic Allow is enabled. The ARP request is an exception because the ARP request is flooded only when ARP Flooding is enabled in the bridge domain regardless of MultiSite.

*   **Layer 2 Unknown Unicast frames (U):** The frames are flooded only when Layer 2 Unknown Unicast is set to flood in the bridge domain regardless of MultiSite. When Intersite BUM Traffic Allow is enabled, Layer 2 Unknown Unicast flooding/proxy mode can be modified via the NDO.

*   **Layer 2 Multicast frames (M):** The same forwarding behavior (ingress and headend replication) applies to intra-bridge-domain Layer 3 multicast frames (that is, the source and receivers are in the same or different IP subnets but part of the same bridge domain) or to "true" Layer 2 multicast frames (that is, the destination MAC address is multicast and there is no IP header in the packet). In both cases, the traffic is forwarded across the sites where the bridge domain is stretched across sites with **Intersite BUM Traffic Allow** is enabled.

The following figure shows Layer 2 BUM traffic flow across sites, for a specific bridge domain that is stretched with **Intersite BUM Traffic Allow** enabled:

![](/images/aciadvanced/aciadvanced_6_6_4.png)

The Layer 2 BUM frame across sites follows this sequence:

1.  EP1, belonging to a specific bridge domain, generates a Layer 2 BUM frame.

1.  Depending on the BUM type of frame and the corresponding bridge domain settings, the leaf node may need to flood the traffic in the bridge domain. As a consequence, the frame is VXLAN-encapsulated and sent to the specific multicast group (Group IP address outer [GIPo]) associated with the bridge domain within the fabric along one of the specific multidestination trees associated to that GIPo, so it can reach all the other leaf and spine nodes in the same site.

1.  One of the spine nodes connected to the ISN is elected as the designated forwarder for that specific bridge domain (this election is held between the spine nodes using IS-IS protocol exchanges). The designated forwarder is responsible for replicating each BUM frame for that bridge domain to all the remote sites with the same stretched bridge domain.

1.  The designated forwarder makes a copy of the BUM frame and sends it to the remote sites. The destination IP address used when the packet is encapsulated with VXLAN is the special IP address (O-MTEP) identifying each remote site and is used specifically for the transmission of BUM traffic across sites. The source IP address for the VXLAN-encapsulated packet is instead the anycast O-UTEP address deployed on all the local spine nodes connected to the ISN.

1.  One of the remote spine nodes receives the packet, translates the VNID value contained in the header to the locally significant VNID value associated with the same bridge domain, and sends the traffic within the site along one of the local multidestination trees for the bridge domain.

1.  The traffic is forwarded within the site and reaches all the spine and leaf nodes with endpoints that are actively connected to the specific bridge domain.

1.  The receiving leaf nodes use the information that is contained in the VXLAN header to learn the site location for endpoint EP1 that sourced the BUM frame. They also send the BUM frame to all (or some of) the local interfaces that are associated with the bridge domain, so that endpoint EP2 (in this example) can receive it.

Depending on the number of configured bridge domains, the same GIPo address may be associated with different bridge domains in the same site. Thus, when flooding for one of those bridge domains is enabled across sites with **Intersite BUM Traffic Allow**, BUM traffic for the other bridge domains using the same GIPo address is also sent across the sites and will then be dropped on the received spine nodes. This behavior can increase the bandwidth utilization in the ISN.

Because of this behavior, when a bridge domain is configured as stretched with **Intersite BUM Traffic Allow** enabled from the Cisco NDO user interface, by default a GIPo address is assigned from a separate range of multicast addresses. It is reflected in the user interface by the **Optimize WAN Bandwidth** flag, which is enabled by default for the bridge domain created by the NDO.

However, if a bridge domain configuration is imported from an APIC domain, by default the flag is disabled, so you will need to manually configure it to change the GIPo address already associated with the bridge domain. Note that doing so will cause a few seconds of outage for the intra-fabric BUM traffic for the bridge domain while the GIPo address is updated on all the leaf nodes on which that specific bridge domain is deployed.

So far, you have seen three bridge domain configurations for the Cisco ACI MultiSite:

*   **Layer 2 Stretch:** Share Layer 2 endpoint information across sites on top of Layer 3 endpoints.

*   **Intersite BUM Traffic Allow:** Forward BUM traffic across sites.

*   **Optimize WAN Bandwidth:** Allocate a GIPo to the bridge domain from a reserved range to avoid unnecessary intersite traffic due to multiple bridge domains sharing the same GIPo.

Although these MultiSite specific configurations could be changed by the APIC in each site, it is recommended to maintain from the NDO like any other configurations that NDO has visibility of.

![](/images/aciadvanced/aciadvanced_6_6_5.png)

In the APIC, the configurations are reflected (and could be modified) in the Advanced/Troubleshooting tab of the bridge domain as the figure shows:

![](/images/aciadvanced/aciadvanced_6_6_6.png)

### Unicast Communication Between Sites

The first requirement before intra-subnet IP communication across sites can be achieved is to complete the ARP exchange between source and destination endpoints. In ACI, Address Resolution Protocol (ARP) request handling (whether ACI floods it or not) depends on the ARP Flooding setting on the bridge domain.

There are two different scenarios to consider:

*   **ARP flooding is enabled in the bridge domain:** When ARP flooding is enabled, the **Intersite BUM Traffic Allow** in the same bridge domain needs to be enabled as well because the ARP request is handled as normal broadcast traffic and is flooded. The previously mentioned is the default configuration for stretched bridge domains created by the NDO with **Intersite BUM Traffic Allow** enabled.

*   **ARP flooding is disabled in the bridge domain:** When ARP flooding is disabled, the ARP request is handled as a routed unicast packet. Hence, the BUM traffic handling across sites (**Intersite BUM Traffic Allow**) does not matter. The previously mentioned is the default configuration for stretched bridge domains created by the NDO with **Intersite BUM Traffic Allow** disabled.

The following example illustrates an intra-subnet IP communication across sites, where ARP flooding is disabled on the bridge domain. This figure shows the ARP request flow from Site 1 to Site 2:

![](/images/aciadvanced/aciadvanced_6_6_7.png)

The ARP request from EP1 in Site 1 to EP2 in Site 2 with ARP flooding that is disabled in the bridge domain follows this sequence:

1.  EP1 generates an ARP request for the EP2 IP address.

1.  Since ARP Flooding is disabled, the local leaf node inspects the ARP payload and checks the target IP address which is of the EP2. Assuming that EP2’s IP information is initially unknown on the local leaf, the ARP request is encapsulated and sent toward the Proxy A anycast TEP address defined on all the local spine nodes (based on the pervasive EP1/EP2 IP subnet information installed in the local routing table) to perform a lookup in the COOP database.

1.  One of the local spine nodes receives the ARP request from the local leaf node.

1.  The capability of forwarding ARP requests across sites in "unicast mode" is mainly dependent on the knowledge in the COOP database of the IP address of the remote endpoint (information that is received via the MP-BGP EVPN control plane with the remote spine nodes).

    If the IP address of the remote endpoint is known (that is, EP2 is not a “silent host”), the local spine nodes know the remote O-UTEP B address identifying the site to which EP2 is connected and can encapsulate the packet and send it across the ISN toward the remote site. The spine node also rewrites the source IP address of the VXLAN-encapsulated packet, replacing the TEP address of the leaf node with the local O-UTEP A address identifying the local site. This operation is very important, because, as previously mentioned, when describing the MP-BGP EVPN control plane exchange across sites, only the EVPN-RID, O-UTEP, and O-MTEP addresses of the spine nodes should be visible in the external IP network.

    **Note:** If the IP address of the remote endpoint was not known in the COOP database in Site 1, from Cisco ACI Release 3.2(1) a new “intersite ARP Glean” function has been introduced to ensure that the remote “silent host” can receive the ARP request, so it can reply and be discovered in the remote site.

1.  The VXLAN frame is received by one of the remote spine nodes, which translates the original VNID and class ID values to locally significant ones and encapsulates the ARP request, then sends it toward the local leaf node to which EP2 is connected.

1.  The leaf node receives the frame, de-encapsulates it, and learns the class ID and site location information for remote endpoint EP1.

1.  The frame is then forwarded to the local interface to which the EP2 is connected, assuming the ARP flooding is disabled on the bridge domain in Site 2 as well.

    At this point, EP2 can reply with a unicast ARP response that is delivered to EP1, as shown in this figure:    

    ![](/images/aciadvanced/aciadvanced_6_6_8.png)

1.  The ARP reply from EP2 in Site 2 to EP1 in Site 1 follows this sequence:

1.  EP2 sends ARP reply to the EP1.

1.  The local leaf node encapsulates the traffic to remote O-UTEP A address.

1.  The spine nodes also rewrite the source IP address of the VXLAN-encapsulated packet, with the local O-UTEP B address identifying the Site 2.

1.  The VXLAN frame is received by the spine node, which translates the original VNID and class ID values of Site 2 to locally significant ones (Site 1) and sends it toward the local leaf node to which EP1 is connected.

1.  The leaf node receives the frame, de-encapsulates it, and learns the class ID and site location information for remote endpoint EP2.

1.  The frame is then sent to the local interface on the leaf node and reaches EP1.

At the completion of the ARP exchange process described above, each leaf node has full knowledge of the class ID and location of the remote endpoints that are trying to communicate. Thus, from this point on the traffic will always flow in the two directions, in which a leaf node at a given site always encapsulates traffic and sends it towards the O-UTEP address that identifies the site to which the destination endpoint is connected.

From a policy perspective, this example considers a scenario where EP1 and EP2 are in two different EPGs, which are part of the same bridge domain and IP subnet, while the communication is dictated by the contract defined between them. The policy is usually applied at ingress on the source leaf node because the source leaf node already learned about the class ID (EPG) of the destination, with an exception if the contract is using a service graph with Policy-Based Redirect (PBR).

If EP1 and EP2 are placed in the same EPG and bridge domain, without microsegmentation (such as without using uSeg EPG) and the EPG is not configured with intra-EPG isolation, EP1 can freely communicate with EP2. If EP1 and EP2 are in the same base EPG and bridge domain, but associated with two uSeg EPGs with a specific contract between them, at steady state the policy is always applied at ingress on the source leaf node.

The inter-subnet communication across sites is similar to the intra-subnet communication. The main difference is the source endpoint will always resolve the ARP information for its default gateway with the local leaf node to which it connects and then send the data packet destined for the remote endpoint to it. Since it is inter-subnet communication, there is no need to consider the Layer 2 flooding scenario. It is unicast traffic same as the ARP reply scenario mentioned in the intra-subnet communication example.

##  6.7)    MultiSite Stretched Components

Cisco ACI MultiSite architecture can be used in various scenarios to meet different business requirements, including disaster avoidance and disaster recovery solutions. Each specific use case essentially uses different bridge-domain configuration settings, to facilitate different connectivity options.

### Layer 2 Connectivity Across Sites Without Flooding

This Layer 2 connectivity use case is probably the most important case that is supported by the Cisco ACI MultiSite architecture, because this architecture provides IP mobility across sites without requiring any Layer 2 flooding. In this Cisco ACI Multi-Site use case, the tenant, VRF, bridge domains, and EPGs are stretched across sites, while the Layer 2 BUM flooding is localized at each site and it is not forwarded between sites. Similarly, their provider and consumer contracts are also stretched between sites, as shown in this figure:

![](/images/aciadvanced/aciadvanced_6_7_1.png)

The chosen bridge domain in the Cisco NDO user interface should be configured as depicted in this figure:

![](/images/aciadvanced/aciadvanced_6_7_2.png)

This use case enables you to implement IP mobility across sites without BUM flooding. Even when the endpoint relocated to a different site, it can still communicate with endpoints that are part of the same (or different) IP subnet that may still be connected in the original site. If a new endpoint in the original site wants to talk to the migrated endpoint, the ARP requests from the new endpoint for the migrated endpoint can still be delivered even without BUM flooding, assuming the ARP flooding is disabled on the bridge domain in each site and the migrated endpoint is already learned in the new site. In case the migrated endpoint has yet to be learned, ARP glean process will kick in even across sites.

A unique capability that is offered by the Cisco ACI MultiSite architecture that is critical to allow flexible connectivity across sites without jeopardizing the overall resiliency of the architecture. An issue in a given bridge domain in a site (as, for example, a broadcast storm) can in fact be confined in that specific site without affecting other connected fabrics.

The IP mobility support across sites (when Layer 2 flooding is not required) is needed in these two main scenarios:

*   For disaster-recovery scenarios that use cold migrations, in which you can move an application, initially running inside Fabric 1, to a different site in Fabric 2. While it can be achieved by changing the IP address used to access the application and applying a DNS-based mechanism to point clients to the new IP address, often the desire is to maintain the same IP address the application had while running in the original site.

*   For business continuity scenarios that use hot migrations, in which you can temporarily relocate workloads across sites without interruption in the provided service that is being migrated. A typical example of functionality that can be used for this purpose is VMware vSphere vMotion for live VM migration.

### Layer 2 Connectivity Across Sites with Flooding

This Cisco ACI MultiSite design provides traditional Layer 2 stretching of bridge domains across sites, including the capability to flood Layer 2 BUM frames. In this use case, the tenant and the VRF are stretched across sites, including bridge domains, EPGs, and their provider and consumer contracts. BUM traffic is forwarded across sites, using the headend replication capabilities of the spine nodes that replicate and send the frames to each remote fabric where the Layer 2 bridge domain has been stretched.

![](/images/aciadvanced/aciadvanced_6_7_3.png)

To stretch a bridge domain in the Cisco NDO user interface with flooding enable, use the following option:

![](/images/aciadvanced/aciadvanced_6_7_4.png)

The need to flood BUM traffic across sites is driven by specific requirements, such as:

*   Deployment of same application hierarchy on all sites, which enables you to spread workloads belonging to the various EPGs across different fabrics, and use common and consistent policies.

*   Active/active high availability implementations between the sites.

*   Layer 2 clustering, which requires BUM communication between cluster nodes.

*   Live VM migration.

Although you can use Cisco ACI Multi-Pod for deployments that require full Layer 2 flooding between Cisco ACI islands, this Cisco ACI MultiSite use case enables you to tightly control which bridge domains are extended and which are kept local (different flooding behavior on a per-bridge domain level). Thus, you can undertake similar approach when flooding must be supported only in a subset of the stretched bridge domains.

### Layer-3-Only Connectivity Across Sites

In many deployment scenarios, the fundamental requirement is to help ensure that only routed communication can be established across sites. In this use case, no Layer 2 extension or flooding is allowed across sites, while different bridge domains and IP subnets are defined in separate sites. As always in Cisco ACI, communication between EPGs can be established only after applying a proper security policy using contract between EPGs. There are two main types of Layer 3 connectivity across sites:

*   Intra-VRF communication
*   Inter-VRF communication

For both options, the chosen bridge domain in the Cisco NDO user interface should be configured as depicted in this figure:

![](/images/aciadvanced/aciadvanced_6_7_5.png)

### Layer 3 Intra-VRF Communication Across Sites

Unlike the previous MultiSite use cases, this scenario provides intersite communication between endpoints that are connected to different bridge domains that are part of the same stretched VRF instance (the same tenant). Therefore, you can manage non-stretched EPGs across sites and the contracts between them, while the MP-BGP EVPN allows the exchange of host routing information, enabling inter-site communication. This use case is depicted in the following figure:

![](/images/aciadvanced/aciadvanced_6_7_6.png)

When deploying Cisco ACI MultiSite for Layer-3-only connectivity across sites, this use case provides multiple benefits compared to simply interconnecting separate Cisco ACI fabrics via L3Out logical connections established from the border leaf nodes, such as:

*   There is no need to re-classify the packet when it enters the destination fabric because the EPG classification (class-ID) is carried in the VXLAN header through the ISN. When connecting two ACI fabrics with L3Outs (such as without ACI MultiSite), the VXLAN header is de-encapsulated when the packet goes out from the source fabric, which implies that the destination fabric needs to have an extra configuration to re-classify the packet when it enters the destination fabric via the L3Out.

*   Contract configuration becomes simpler and easier to maintain. When connecting two ACI fabrics with L3Outs (such as without ACI MultiSite), separate contracts need to be crated and applied between the source EPG and the L3Out in the source fabric, and between the destination EPG and the L3Out in the destination fabric. With ACI MultiSite, contracts are stretched and it allows to configure the contract directly between the source and destination EPG as if they are the entities in the same fabric.

*   The same IP subnet can be easily used on two different fabrics. When connecting two ACI fabrics without ACI MultiSite, which requires the deployment of a separate Layer 2 Data-Center Interconnect (DCI) technology in the external network while ACI MultiSite only requires a simple IP connectivity of a few IP addresses such as O-UTEP, O-MTEP.

*   Extending multiple VRFs across fabrics is simpler and easier to maintain. When connecting two ACI fabrics with L3Outs (such as without ACI MultiSite), the external network between the L3Outs in each site also needs to support VRF separation (or L3VPN) while delivering the packet and routing protocol database between each site while ACI MultiSite only requires a simple IP connectivity of a few IP addresses such as O-UTEP, O-MTEP.

### Layer 3 Inter-VRF Communication Across Sites

This use case scenario enables you to create provider EPGs in one group of sites that offer shared services, which can be consumed by EPGs in another group of sites. The source and destination bridge domains belong to different VRF instances (part of the same or different tenants), which requires route-leaking function to allow this communication, which is driven by the creation of a contract between the source and destination EPGs.

This figure depicts this use case scenario where multiple source IP subnets are placed in a separate VRF instance (tenant) that require access to a shared service offered in a separate VRF instance (tenant):

![](/images/aciadvanced/aciadvanced_6_7_7.png)

The provider contract C2 for the shared service must be set to global scope, because it needs to be used between EPGs deployed across tenants, such as Web and App EPGs in Tenant 1 and BigData EPG as shared service in Tenant BigData. The communication across the VRFs is established through VRF route leaking.

This design provides communication across VRFs and tenants while preserving the isolation and security policies of the tenants. Still, the shared service is supported only with nonoverlapping and nonduplicate subnets.

##  6.8)    MultiSite Vs Multi-Pod Comparison

The Cisco ACI MultiSite and Multi-Pod architectures can be used to meet different use cases and business requirements. Despite some similarities between these two solutions, they have some fundamental differences.

The following figure illustrates both solutions providing summary of differences between them:

![](/images/aciadvanced/aciadvanced_6_8_1.png)

The Cisco ACI MultiSite architecture uses the Cisco NDO, as an orchestrator positioned on the top of Cisco APICs in different sites, which is a complementary solution to the APICs. It enables you to define intersite policies that you can deploy across the separate Cisco ACI fabrics. However, the Cisco ACI Multi-Pod uses a single Cisco APIC cluster as central management pane, which enables operational simplicity.

The Cisco ACI MultiSite provides complete fault and change domain separation between the interconnected fabrics, which represent separate availability zones. This design is highly scalable, allowing you to scale the number of Cisco ACI nodes and endpoints that can be connected across separate sites. Finally, the Cisco ACI MultiSite design also eliminates the need to deploy multicast in the Layer 3 infrastructure that interconnects the fabrics, because headend replication is performed on the spine switches to allow BUM traffic to be sent across fabrics as unicast packets through the ISN for all stretched bridge domains that require it.

On the other hand, the Multi-Pod design uses few Cisco APIC nodes, all part of a same domain, and ensures that all the Cisco ACI functions supported in a single-pod fabric are also available in a Multi-Pod fabric (service graphs, shared L3Out connections, creation of a single VMM domain extending across pods, and so on).

The ISN in the Cisco ACI MultiSite does not need to support multicast to facilitate BUM traffic flows across sites. This process is a much simpler routed infrastructure than the IPN in Cisco ACI Multi-Pod design. The IPN has to support PIM-Bidir while connecting different pods, which are part of the same Multi-Pod fabric.

In addition, from Cisco ACI software release 3.2(1), it is possible to deploy one or more Cisco ACI Multi-Pod fabrics as "sites" of a MultiSite architecture. The combination of those two architectural options provides flexibility and feature-richness to meet the requirements for interconnecting data center networks, as depicted in this figure:

![](/images/aciadvanced/aciadvanced_6_8_2.png)

As indicated in this example, you can have geographically distributed data centers deployed as sites representing separate availability zones, while each site has its own Cisco ACI Multi-Pod fabric and the NDO is positioned on the top of the Cisco APICs in different sites.

The following figure depicts Cisco ACI MultiSite and Multi-Pod deployment examples, to show practical use cases for each architecture.

![](/images/aciadvanced/aciadvanced_6_8_3.png)

The Cisco ACI MultiSite can be used to provide a scale-up model that can connect number of data centers in a single large fabric, while each data center is positioned in different site with complete fault and change domain separation. Hence, it can facilitate scalability and integration of multiple fabrics into one solution. In addition, the sites can be deployed around the world because there is no latency requirement between sites (the latency requirement between pods for Multi-Pod is 50 msec RTT), so the Cisco ACI Multi-Site design can provide data center interconnect functionality for these sites.

The Cisco ACI Multi-Pod can be deployed in the same physical data center location, which may help divide the large fabric that is deployed in different locations in the same building, in smaller pods to benefit of the failure domain isolation provided by the Multi-Pod approach. The pods can be also deployed in different geographical locations, but usually in relative proximity (metro area), with an interconnection network that must support PIM Bidir.

For more information about the Cisco NDO installation and deployment, please visit the Cisco Nexus Dashboard Orchestrator Deployment Guide, available at: https://www.cisco.com/c/en/us/td/docs/dcn/ndo/3x/deployment/cisco-nexus-dashboard-orchestrator-deployment-guide-341.html

For more information about the Cisco NDO user interface and configuration procedures regarding different functionalities, please visit the Cisco Nexus Dashboard Orchestrator Configuration Guide for ACI Fabrics, available at: https://www.cisco.com/c/en/us/td/docs/dcn/ndo/3x/configuration/cisco-nexus-dashboard-orchestrator-configuration-guide-aci-341.html

For more information on how to enable CloudSec encryption in a Cisco ACI MultiSite deployment, please visit the Cisco ACI MultiSite Configuration Guide, available at: https://www.cisco.com/c/en/us/td/docs/dcn/mso/3x/configuration/cisco-aci-multi-site-configuration-guide-311/mso-configuration-aci-infra-cloudsec-31x.html

For comprehensive information about Cisco ACI MultiSite deployment, including MultiSite architecture, Cisco NDO deployment options and functionalities, ISN requirements and control plane, packet flow between sites, as well as stretched components options and Cisco ACI MultiSite and Multi-Pod comparison and integration, please visit the Cisco ACI Multi-Site Architecture White Paper, available at: https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/white-paper-c11-739609.html.

For additional references, access videos from various CiscoLive! Events on Cisco ACI MultiSite:

*   ACI MultiSite Architecture and Deployment – BRKACI-2125 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=2125#/session/1573153536059001JPM1

*   How to Set up an ACI MultiSite with Single POD and Multi-Pod – BRKACI-2291 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=2291#/session/1573153536176001JlK5

*   Cisco ACI Multi-Pod Design and Deployment – BRKACI-2003 On-Demand, available at: https://www.ciscolive.com/global/on-demand-library.html?search=brkaci-2003#/session/1564527368297001cZrJ

