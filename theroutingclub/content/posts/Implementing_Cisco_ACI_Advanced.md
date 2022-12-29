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


#   2)  Using Advanced Cisco ACI policy and Tenant Configuration

##  2.1)    Introduction

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



