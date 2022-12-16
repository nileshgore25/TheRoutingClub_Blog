---
title: "Nexus OS Programmability and Automation"
date: 2022-11-25
draft: false
tags: [Nexus, NXOS, Automation, NX-OS Automation, Cisco, Programmability]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "This Post is to document the learnings from - Nexus OS programmability and automation capabilities"
# AuthorName: "Nilesh Gore"
---

This Post is to document the learnings from - Nexus OS programmability and automation capabilities

The following table summarizes the most important areas and respective technologies available on the Cisco Nexus switches.

|                Area                |                 Technology                |
|:----------------------------------:|:-----------------------------------------:|
| Boot and Provisioning              | POAP, PXE                                 |
| Package and Application Management | RPM                                       |
| Open Interfaces                    | Bash, Broadcom Shell, Python, Guest Shell |
| Extensibility                      | Containers                                |
| Configuration Management Tools     | Puppet, Chef, CFEngine, Ansible           |
| Programmable Interface Agents      | NETCONF, RESTCONF, gRPC                   |
| NX-OS APIs                         | NX-API                                    |
| Event Management and Notification  | Embedded Event Manager                    |


# Booting and Provisioning with POAP and PXE

In order for these hundreds of devices upon their initial booting, to download their disk images and configuration files from a preconfigured server, the Cisco Nexus switches have two technologies as mentioned below:

* Power-On Auto Provisioning (POAP)
* Preboot Execution Environment (PXE)

##  Power-On Auto Provisioning (POAP)

POAP enables a switch to upgrade its image on startup without intervention by an administrator. This helps eliminate errors that can happen during a manual configuration process. POAP happens when, upon booting, the switch does not find its startup-config file. At that point, the device searches for a Domain Host Configuration Protocol (DHCP) service on the network. DHCP assigns the device the IP address, default gateway, and IP address that are tracked by the Domain Name System (DNS) server. Then, POAP gets the IP address of a script server, downloads the correct script for the switch, and runs the script on the device. The script usually instructs the system to download and install the correct image and running-config file.

You can also deploy Puppet or Chef agents with POAP. During the time, the router downloads the images and running-config file, it also runs the Puppet or Chef agent executable file. When the switch comes up, the appropriate agent runs in a Linux container. Then, the agent establishes a connection with the controller Puppet or Chef server.

![](/images/NXOS_Programmability/poap_1.jpg)

##  Preboot Execution Environment (PXE)

Another advanced boot loader available on the Cisco Nexus Series switches is the Preboot Execution Environment (PXE) boot tool. PXE is a common, well-understood framework that should be familiar to the server team. It also fetches an image from the network and enables automated installation and boot-up.

##  Package and Application Management

Cisco Nexus switches no longer run a proprietary operating system. They use a 64-bit Yocto-based Wind River Linux kernel. This enables any team with Linux skills to access a device's bash environment and manage a switch as they would manage their Linux servers.

![](/images/NXOS_Programmability/pam_1.jpg)

The newer and more open Cisco NX-OS enables the loading of other software packages using Red-Hat Package Managers (RPMs). Using the Yellowdog Updater and Modifier (YUM), an administrator can update or upgrade software on the Nexus series switch as they would a Linux server. For example, the administrator uses the yum update bgp command to update the BGP protocol on the switch.


#  Programmable Interface Agents

Programmable interface agents provide an interface between the Device and clients. They specify the transport, the protocol, and the encoding of the communications with the Device. NX-OS Programmable Interfaces support three agents: NETCONF, RESTCONF, and gRPC. Each provides different interfaces for configuration management of the Device using YANG models.

## NETCONF

NETCONF provides consistent mechanisms to install, manipulate, and delete the configuration of network devices. Its operations rely on a simple Remote Procedure Call (RPC) layer. NETCONF uses a secure transport protocol, such as SSH, and Extensible Markup Language (XML)-based data encoding for configuration data as well as protocol messages.

The following example shows how to use the Python NCClient module to initiate a NETCONF session with a host and send a get_config request. The following Python code should be run from your local system, and requires that you install ncclient first.

```python
    from ncclient import manager
    nxv = manager.connect(host="131.226.217.151", port=830, username="admin", password="Admin_1234!", device_params={'name':'nexus'}, hostkey_verify=False)
    print(nxv.get_config(source='running', filter="<filter><System xmlns='http://cisco.com/ns/yang/cisco-nx-os-device'></System></filter>").data_xml)
```

This Python code will return the details of the Yang model in XML format, which will look like the following output:

```xml
    <?xml version="1.0" encoding="UTF-8"?><data '
    xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" '
    xmlns:if="http://www.cisco.com/nxos:1.0:if_manager" '
    xmlns:nfcli="http://www.cisco.com/nxos:1.0:nfcli" '
    xmlns:nxos="http://www.cisco.com/nxos:1.0" '
    xmlns:vlan_mgr_cli="http://www.cisco.com/nxos:1.0:vlan_mgr_cli">\n'
            <System>\n'
                <ac-items>\n'
                    <adminSt>enabled</adminSt>\n'
                </ac-items>\n'
                <acct-items>\n'
                    <adminSt>enabled</adminSt>\n'
                    <sessionId>0</sessionId>\n'
                    <sessionNum>0</sessionNum>\n'
                    <sourceId>0</sourceId>\n'
                </acct-items>\n'
                <actrl-items>\n'
                    <adminSt>enabled</adminSt>\n'
                    <inst-items>\n'
                        <adminSt>enabled</adminSt>\n'
                    </inst-items>\n'
                </actrl-items>\n'
                <address>0.0.0.0</address>\n'
                <adjacency-items>\n'
                    <adminSt>enabled</adminSt>\n'
                    <inst-items>\n'
                        <adminSt>enabled</adminSt>\n'
                        <dom-items>\n'
                            <Dom-list>\n'
                                <name>management</name>\n'
                                <db-items>\n'
                                    <Db-list>\n'
                                        <type>ipv6</type>\n'
                                    </Db-list>\n'
                                    <Db-list>\n'
                                        <type>ip</type>\n'
                                    </Db-list>\n'
                                </db-items>\n'
                            </Dom-list>\n'
                            <Dom-list>\n'
                                <name>default</name>\n'
                                <db-items>\n'
                                    <Db-list>\n'
                                        <type>ipv6</type>\n'
                                    </Db-list>\n'
                                    <Db-list>\n'
                                        <type>ip</type>\n'
                                    </Db-list>\n'
                                </db-items>\n'
                            </Dom-list>\n'
                        </dom-items>\n'
                    </inst-items>\n'
                </adjacency-items>\n'
                <arp-items>\n'
                    <adminSt>enabled</adminSt>\n'
                    <inst-items>\n'
                        <adjLoggingLevel>error</adjLoggingLevel>\n'
                        <adminSt>enabled</adminSt>\n'
                        '
    <allowStaticArpOutsideSubnet>disabled</allowStaticArpOutsideSubnet>\n'
                        <cacheLimit>174080</cacheLimit>\n'
                        <cacheSyslogRate>1</cacheSyslogRate>\n'
                        <dom-items>\n'
                            <Dom-list>\n'
                                <name>management</name>\n'
                                <encap>unknown</encap>\n'
                                <if-items>\n'
                                    <If-list>\n'
                                        <id>mgmt0</id>\n'
                                        <adminSt>enabled</adminSt>\n'

    # Omitted For Brevity#
```

##  RESTCONF

The RESTCONF protocol provides a REST-like interface to network devices and extends the idea of NETCONF. It uses HTTP(S) as the transport layer to carry JSON or XML-based data encoding to interface with network devices exposing data modeled in YANG. Both RESTCONF and NETCONF enable access to the same YANG modeled data, but they use different API bindings.

**Note:** RESTCONF does not replace NETCONF. RESTCONF provides an additional way to expose the same data, modeled using YANG (such as configuration or operational data). Programmers or designers can choose which API to use based on experience, skillsets, and requirements.

##  gRPC

gRPC is an open-source remote procedure call (RPC) system that was initially developed at Google. It uses HTTP/2 for transport, Protocol Buffers as the interface description language, and provides authentication, bidirectional streaming and flow control, blocking or nonblocking bindings, and cancellation and timeouts. It generates cross-platform client and server bindings for many languages. (source: Wikipedia)

![](/images/NXOS_Programmability/netconf_1.jpg)

When devices receive a request using NETCONF, RESTCONF, or gRPC, it is converted into an abstract message object that is distributed to the underlying model infrastructure based on the namespace in the request. The appropriate model is selected using the namespace, and the request is passed to it for processing. The model infrastructure runs the request (read or write) on the device data store, and returns the results to the agent of origin, which transmits the response back to the requesting client.

![](/images/NXOS_Programmability/netconf_2.jpg)

# NX-OS APIs

You can configure a Cisco Nexus device by using the device's own command-line interface (CLI). To make the Nexus configuration more accessible, Cisco introduced the NX-API REST interface through HTTP/HTTPS APIs. NX-API REST makes specific CLIs available outside of the switch, and combines multiple configuration actions in fewer HTTP/HTTPS operations.

Not only does NX-API REST support commands and switch configurations, it can also run Linux Bash commands.

NX-API REST uses HTTP/HTTPS for transport. Configuration commands are encoded into the HTTP/HTTPS body and use POST as the delivery method. In the backend, NX-API REST uses the Nginx HTTP server. You can instruct the Nginx server to return requested data either in XML or JSON format.

![](/images/NXOS_Programmability/api_1.jpg)

Here is an example of NX-API in action.

![](/images/NXOS_Programmability/nx_api_1.jpg)

