---
title: "MPLS Label Operations"
date: 2022-03-27T23:11:32+05:30
draft: false
tags: [MPLS VPN, Labels]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "In this post, we will check various operations that can be performed by the MPLS LSR (Label Switching Router) on the MPLS label stack of a packet."
# AuthorName: "Nilesh Gore"
---

In this post, we will check various operations that can be performed by the MPLS LSR (Label Switching Router) on the MPLS label stack of a packet.

# MPLS Label operation types

Mentioned below are the different types of MPLS label operation.

1. **PUSH** - Impose a new label. Can be a new label on an IP packet or an additional label on an already labeled packet.

1. **POP** - Removing the top label from the MPLS label stack of a packet. The router preceding the egress router (known as Penultimate Hop router) pops the top label in the label stack and transmits the  packet to the next hop router.
Example: An egress router announces an Implicit NULL label for its loopback interface IP prefix (which is the BGP next hop for the VRF prefixes those are advertised by this egress router) to its LDP peers.
The LDP peer (PH router) in turn announced its own label (for example 25) to the loopback interface prefix received via IGP from the egress router.

1.  **SWAP** - swap the top label.

1.	**Untagged** - All labels removed and forward the packet unlabeled.

1.	**Aggregate** - All labels removed, and an IP lookup is done on the packet.

Out of the above label operations, the **Untagged** and the **Aggregate** operation need more detailed study as their operation mechanism will be clear only when we see them in action.

## Untagged

An untagged label will be listed as an outgoing label against an IP prefix in the LFIB (Label Forwarding Information Base) when the next-hop router has not advertised the label for that IP prefix.
The router has no output label associated with the forwarding equivalence class (FEC ... usually an IP prefix). Since there is no output label, the router cannot perform a label swap (or pop) but has to remove the whole MPLS shim header.
The outbound interface is not mpls enabled and therefore all remaining labels must be removed before forwarding the packet to the outbound interface.

### When would the next-hop router not advertise a label ? 
The reasons a next-hop router would not advertise a label for an IP prefix include:

*	It's not running MPLS.
*	It's running MPLS but not CEF (MPLS labels are assigned to IP prefixes in CEF table).
*	It's not reachable across an MPLS-enabled interface (both routers could be running MPLS, but the transit interface does not have the mpls ip configuration).
*	The LDP session has not been established yet.
*	There is a mismatch in LDP protocol (one router is running Cisco's proprietary TDP, the other one standard LDP).
*	The next-hop router uses an access-list to filter the IP prefixes for which the MPLS labels are advertised.

#### Example 1
Prefixes received from CE routers will be untagged in the MPLS forwarding table (LFIB) of the PE router.
#### Example 2
Assume that a VPNv4 route is being sent to the iBGP neighbor. The address of the next hop is a loopback interface that does not have a /32 mask defined. OSPF is being used on this loopback interface, and the OSPF network type of this interface is LOOPBACK. OSPF advertises this IP address as a host route (with mask /32), regardless of what mask is configured. This advertising conflict with TDP, which uses configured masks, so the TDP neighbors may not receive a tag for the prefix with configured subnet mask. This condition could break connectivity between sites that belong to the same VPN.

## Aggregate

Aggregate label means that a layer3 lookup is needed to forward the packet.
Aggregate labels are assigned by routers (PE's) in MPLS VPN context to all the local IP addresses for a given vrf.
They are assigned to locally originated prefixes and are also assigned to the aggregate prefixes to (i.e. BGP summarization)

When traffic destined to that label (either destined to the router or to the PE-CE subnet) reaches the PE, it needs to perform an extra lookup in the FIB (CEF) to find the next hop and the outgoing interface.

Aggregate Label is also assigned to aggregated prefixes

When you perform an aggregation (or summarization) on an LSR, it advertises a specific label for the aggregated prefix, but the outgoing label in the LFIB shows “Aggregate.” Because this LSR is aggregating a range of prefixes, it cannot forward an incoming labeled packet by label-swapping the top label. The outgoing label entry showing“Aggregate” means that the aggregating LSR needs to remove the label of the incoming packet and must do an IP lookup to determine the more specific prefix to use for forwarding this IP packet. 

## Topology
![MPLS VPN topology](/images/mpls_vpn/mplsvpn_1.JPG)

In this topology, the MPLS core is running OSPF as IGP. PE1 and PE2 are MPLS provider edge routers which has vrf VPN_A configured on them. The router in the MPLS core is P router (Provider router) which does not have any VRFs configured. However the P router is a BGP route reflector and router PE1 and PE2 are its clients.
In case of BGP sessions between the PE1, PE2, P routers, we have enabled only the BGP VPNv4 address family and have disabled the IPv4 address family (which is enabled by default if not manually disable).



