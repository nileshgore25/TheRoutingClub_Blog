---
title: "Implementing Automation for Cisco Data Center Solutions"
date: 2022-11-17
draft: false
tags: [DCAUI, ACI Automation, NX-OS Automation, Cisco]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "This Post is to document the learnings from - Implementing Automation for Cisco Data Center Solutions"
# AuthorName: "Nilesh Gore"
---

This Post is to document the learnings from - Implementing Automation for Cisco Data Center Solutions

# 1 Describing the Cisco ACI Policy Model

## 1.1 Introduction

Cisco Application Centric Infrastructure (ACI) is designed to address the challenge of translating application developer concepts into network infrastructure implementations. Cisco ACI is based on a combination of Cisco Nexus 9000 Series Switches, which is the network fabric, and the Cisco Application Policy Infrastructure Controller (Cisco APIC). Cisco ACI is a programmable data center solution, with a single point of visibility and control—the Cisco APIC. The programming model is based on a common policy and operations framework for deploying and monitoring applications in a data center.

This section briefly covers data center network evolution and the Cisco ACI fabric, its components, and functionality. It then reviews the policy and object model, which you will explore in the discovery lab through the web-based user interface that is provided by the Cisco APIC. Finally, after performing configuration tasks manually, you will consider the benefits of and necessity for automation in the context of managing a Cisco ACI deployment.

## 1.2  Cisco ACI Overview

A spine-and-leaf network architecture (such as Cisco ACI) is a data center fabric that is optimized for east-west communications patterns between applications that run on many different servers across different racks. This architecture contrasts with the traditional three-tiered network architecture, which is optimized for north-south traffic patterns; that is, traffic from destinations that are located outside the fabric that enter via the core layer toward the servers in the access layer.

![](/images/dcaui/dcaui_1_2_1.png)

A traditional three-tiered network design is illustrated on the left in the figure. The core network layer, typically at least two Layer 3 switches, provides redundant connectivity between switches in the aggregation layer and external networks. The aggregation switches are redundantly connected to access switches, which are connected to servers in racks. The aggregation layer also contains the Layer 4 to Layer 7 services such as firewalls and load balancers.

Segmentation is achieved in such an architecture at Layer 3, at either the aggregation layer or the core layer. VLAN-based segmentation, typically applied at the aggregation layer, allows traffic to flow within the VLAN, with policy applied at Layer 3 as traffic crosses from one VLAN to another via the default gateway.

The three-tiered network architecture is somewhat optimized for north-south traffic that originates outside the data center and is directed to one of the servers in a rack. The three-tiered architecture is less well-suited for east-west communications between servers in the racks.

Because the Layer 3 boundary can even be at the core layer, east-west traffic from one server to another in a different subnet must sometimes traverse the core network layer. Even when the Layer 3 boundary is at the aggregation layer, not every aggregation switch is connected to every access switch, so traffic must sometimes traverse multiple aggregation switches. Furthermore, the traffic between servers in a data center requires additional network capacity for which the network may not have been designed. Therefore, traffic patterns in such an architecture can be inconsistent and subject to capacity constraints.

From an administration perspective, the policy boundaries, that is, different VLANs and routing domains and Layer 4 to Layer 7 services, must be traditionally configured via CLI. Typically, multiple devices are configured for the path between the different servers that represent the layers in an application. When servers change, these devices must often be reconfigured.

Such changes are difficult to coordinate at the individual device level when they are done via CLI, and risk interrupting existing services. Therefore, changes in such an environment are difficult to implement and are made infrequently. The automation techniques that are introduced in this course make it easier to make such changes. However, there is the implied need to have a central coordinating system to provide end-to-end service context and encourage the automation, that is, a controller or orchestrator.

In contrast to the three-tiered architecture is the Cisco ACI spine-and-leaf architecture, which is illustrated on the right in the figure. In Cisco ACI, servers are connected to leaf switches. Leaf switches are connected via high-capacity links to each spine. These links are nonblocking and have fast failover, so traffic patterns are consistent and predictable. This situation is especially important for supporting east-west traffic patterns between servers on different racks, which is an important aspect of supporting the microservice architectures that underpin many modern application designs.

In effect, the Cisco ACI fabric functions as a uniform, high-capacity switching environment, and acts as one physical switch. The Cisco APIC cluster manages and distributes policies to the leaf switches from a central point of administration. Unlike the three-tiered architecture, there is no need to consider individual devices or policy boundaries. The fabric, together with the Cisco APIC, considers these devices and boundaries for you.

![](/images/dcaui/dcaui_1_2_2.png)

### Cisco ACI Components

Cisco ACI operates as an object-based model, so the switches that run in Cisco ACI mode are only programmable via an object-based policy engine that operates in the Cisco APIC. The controller becomes an integral part of the network, and holds the profiles that contain the policies for programming the switches centrally. The switches themselves do not maintain a CLI configuration file as previously used in Cisco Nexus Operating System (NX-OS) based systems. The configuration is held on the APIC and is an object-oriented schema that is written in XML or JavaScript Object Notation (JSON) and stored in a profile to implement application-driven, network-driven, and security-driven policies.

The core components of an Cisco ACI deployment include the following:

* **Two-tiered spine-and-leaf topology:** With a spine-and-leaf network, every server on the network is same distance away from all other servers. Therefore, latency across the network is predictive, even when using numerous paths for a single traffic flow.

* **Leaf switches:** The leaf switches provide external connectivity into the fabric. For connected devices, 1, 10, and 25 Gigabit Ethernet is offered, with 40 and 100 Gigabit Ethernet uplinks to the spines. Leaf switches serve as the policy enforcement points, and spines provide a high-throughput fabric with fast failover. To achieve high degrees of scale, usage of required expensive memory resources (such as ternary content addressable memory [TCAM]) may be effectively limited to the applications and endpoints that are locally pertinent to each individual leaf.

* **Spine switches:** These switches use 40 or 100 Gigabit Ethernet to provide a nonblocking fabric with rapid failure detection and rerouting. Spine switches aggregate data, such as the location of endpoints across the fabric, and can forward traffic to remote devices that were previously unknown to a leaf switch.

* **Cisco APIC:** APICs provide the centralized point of management for fabric configuration and observing the summary operational state. The APIC is implemented using a distributed framework across a cluster of appliances. From a policy perspective, the APIC is the primary point of contact for configuration and acts as the policy repository.

* **External connectivity:** This component includes the Layer 2 or Layer 3 connections to devices that are outside of the Cisco ACI fabric, which is connected to the leaf switches.

* **Services:** Service nodes are physical or virtual appliances that provide specialized network functionality that the traditional bridging and routing network elements do not or cannot provide. Examples of service nodes include stateful firewalls, load balancers, and Network Address Translation (NAT) devices.

![](/images/dcaui/dcaui_1_2_3.png)

At the network layer, Cisco ACI changes the approach to IP addresses, subnets, and VLANs, among other network constructs. Cisco ACI focuses on applications, the types of policies needed (security or quality of service [QoS] policies, for example), and the hosts to which those policies should be similarly applied, which in the Cisco ACI world are called endpoint groups (EPG)s.

An application in Cisco ACI has these attributes:

* It is a collection of endpoints. Not necessarily hosts or applications, an endpoint can be a network device, a firewall, a router, or it could provide Layer 4 to Layer 7 services.
  
* Endpoints do not change the behavior of traffic flows, so you use policies. Policies are configured to match one or more types of traffic, and provide an action that the fabric will take regarding allowing, blocking, or prioritizing.
  
* Relationship definitions are achieved with contracts. One or more policies are assembled in an object that is called a contract. This contract is provided by the group of endpoints that offers the services; the client of the services consumes the contract.

These objects may be created individually based on application requirements or, as in any object-based model, may be reused numerous times across the architecture as desired.

Cisco ACI brings things closer to application terms: in the figure, traffic enters the Cisco ACI fabric where it is directed through various services and filters before it hits the web tier. More filters and actions are applied as the packets travel from the web tier to the application tier, and again from the application tier to the database.

![](/images/dcaui/dcaui_1_2_4.png)

Using the object model, Cisco ACI can accomplish the same QoS, security, and business requirements without the need for cumbersome traditional infrastructure-level provisioning of new IP addresses, VLANs, and so on. Once the necessary configuration is performed through the APIC, the application is deployed across the Cisco ACI spine-and-leaf Clos topology, which also offers numerous technical enhancements in addition to traditional data center architectures.


## 1.3  Cisco ACI Object Model Hierarchy

Everything that is managed and subject to policies in Cisco ACI is represented by an object. The Management Information Model (MIM) fully describes these objects, data classes, and properties, and forms the basis on which the whole system is built.

![](/images/dcaui/dcaui_1_3_1.png)

The Cisco ACI fabric is composed of the physical and logical components in the system. Those components are defined in the MIM, which has a hierarchical representation as the Management Information Tree (MIT), which is summarized at a high level in the diagram in the figure. The Cisco APIC stores and manages the MIT.

A reference for the MIM may be found at the APIC Management Information Model Reference page at Cisco DevNet. In that reference, you will see that the classes are organized in packages. For example, fv:Tenant is in the fv, or fabric virtualization, package. Another package is fabric, which is the physical fabric; for example, fabricLeafP. The object model is large and complex, but the main classes you need to work with are limited to a few key concepts that are focused on the tenant, applications, EPGs, and contracts.

The Cisco APIC allows you to control managed resources by presenting the object properties that can be set to define the system behavior. Like object models in general, such properties can be inherited according to the location of an object in the MIT.

Each node in the MIT represents a managed object or group of managed objects. A managed object is an abstract representation of the resources in the fabric. Such resources can be concrete, such as a switch or interface, or logical, such as an application profile, EPG, or contract.

The root of the MIT represents the policy universe (uni). Descending from the root, each node is a managed object with a unique distinguished name (DN). The name identifies the object, and the path in the name identifies its place in the MIT. The hierarchical nature of the policy model is suitable for use with a Representational State Transfer (REST) application programming interface (API) interface. A REST URL maps to the managed object containment path and DN.

The policies that govern how the system functions are grouped in the following areas:

* **Cisco APIC controller policies:** These policies address the functions of the APIC cluster.
  
* **Tenant policies:** These policies allow an administrator to define most of the Cisco ACI system activities for application delivery purposes. There are four groups of tenant policies.
  
  * **User tenant policies:** These policies control resources like applications, databases, web servers, network-attached storage, virtual machines, and, in general, the types of elements that are most typically associated with the main functions of the system.
  
  * **Common tenant policies:** These policies address the operation of common resources, such as firewalls, load balancers, Layer 4 to Layer 7 services, intrusion detection appliances, and so on.
  
  * **Infrastructure tenant policies:** These policies govern infrastructure resources such as the fabric Virtual Extensible LAN (VXLAN) overlay, and enable a fabric provider to deploy resources to one or more user tenants.
  
  * **Management tenant policies:** These policies govern the fabric management functions that are used for in-band and out-of-band configuration of fabric nodes using a management network outside of the fabric data path.

* **Access policies:** These policies govern switch access ports that provide connectivity to resources such as storage, compute, Layer 2 and Layer 3 (bridged and routed) connectivity, virtual machine hypervisors, Layer 4 to Layer 7 devices, and so on.
  
* **Fabric policies:** These policies govern the switch fabric ports, including such functions as Network Time Protocol (NTP) server synchronization, Intermediate System-to-Intermediate System (IS-IS), Border Gateway Protocol (BGP) route reflectors, Domain Name System (DNS), and so on. The fabric-managed object contains objects such as power supplies, fans, chassis, and so on.
  
* **Virtual machine (VM) domains:** These domains group VM controllers with similar networking policy requirements.
  
* **Layer 4 to Layer 7 service policies:** These policies enable the system to dynamically respond when a service comes online or goes offline, by providing an integrated life-cycle automation framework. Policies provide service device package and inventory management functions.
  
* **Authentication, authorization, and accounting (AAA) policies:** These policies govern user privileges, roles, and security domains of the Cisco ACI fabric.

### Tenant Model Hierarchy

Most of the functions of Cisco ACI that support applications are related to the tenant concept. The tenant and related managed objects are reviewed here.

![](/images/dcaui/dcaui_1_3_2.png)

The diagram in the figure summarizes the tenant and related managed objects. The classes are shown in the diagram with their full names. Each class also has a package and class name, which are discussed in the following paragraphs. For example, the bridge domain managed object is fvBD.

Tenants can represent a customer, an organization or a domain, or simply a grouping of related policies. A tenant class is a logical container for application policies that exercise domain-based access control. The tenant class concept is the basis for segmentation in the system for the purposes of visibility, administrator access control, resource utilization, and so on.

There can be multiple tenants in the fabric. At least one tenant must be configured before any Layer 4 to Layer 7 services can be deployed.

Tenants generally contain filters, contracts, outside networks, bridge domains, and application profiles that contain EPGs. A tenant can contain one or more virtual routing and forwarding (VRF) contexts, and each VRF context (private network in the Cisco APIC GUI) can contain multiple bridge domains.

A VRF context is a tenant private network. A tenant can have multiple VRF contexts. A context is a unique Layer 3 forwarding address domain and application policy domain. One or more bridge domains are associated with a context.

A bridge domain represents a Layer 2 forwarding construct within the fabric. A bridge domain must be linked to a VRF context and have at least one subnet that is associated with it. There are conceptual similarities between bridge domains and VLANs, but the bridge domain is more usefully seen as a broadcast boundary.

An application profile contains one or more EPGs (EPG)s, which represent the resources that the application requires. For example, an e-commerce application could have an EPG that contains a web server, an application server, a database server, data that is located in a SAN, and access to outside resources that enable financial transactions. The EPG is the source of the application resources, and is an important concept.

Endpoints are devices that are connected to the network directly or indirectly. Endpoints have an address (identity), a location, attributes (such as version or patch level), and can be physical or virtual. Endpoint examples include servers, virtual machines, network-attached storage (NAS), or clients on the Internet. Endpoint membership in an EPG can be dynamic or static.

An EPG contains endpoints that have common policy requirements such as security, virtual machine (VM) mobility, QoS, or Layer 4 to Layer 7 services. Rather than configure and manage endpoints individually, they are placed in an EPG and are managed as a group.

EPGs can be categorized according to the application they provide, the function they provide (such as infrastructure), where they are in the structure of the data center, or another organizing principle that a fabric or tenant administrator chooses to use. Regardless of how an EPG is configured, EPG policies are applied to the endpoints they contain.

If you look at the formal name of each managed object, you can see how the package name is combined with the object name to create the full managed object name. The following is a mapping of the friendly name to the actual resource name within a Cisco ACI fabric.

* **Tenant:** fvTenant
* **Bridge Domain:** fvBD
* **Application EPG:** fvAEPg
* **Filter:** vzFilter

![](/images/dcaui/dcaui_1_3_3.png)

In this figure, you can see an explicit object and view it within the MIM documentation, which can be found here: https://developer.cisco.com/media/mim-ref/

You can notice five important attributes as you browse each object.

1.  The name of the object as documented is the package, such as “fv,” and the name of the specific object, such as “Tenant.”
1.  Is the object configurable?
1.  What is the specific object? The definition of the object.
1.  The relative name
1.  The distinguished name

##  1.5 Reasons for Automating Cisco ACI

Traditionally, networks have been built and operated manually. Indeed, designs revolved around devices that were individually managed and maintained. To make changes or troubleshoot, network engineers had to connect to each device and use its CLI interface to enter commands.

With network growth, this process became more challenging, because complexity, scale, and chances for errors increased beyond the capabilities of the methods, tools, and processes that apply to a static and manually managed infrastructure.

More specifically, because a CLI is an interface that is built for human interaction, it is not ideal for programmatic access in an automated environment. Cisco ACI brings an object model with a full REST API to facilitate the operation of a data center in a modern and scalable way.

The ability to access configuration, monitoring, telemetry, and troubleshooting tools programmatically becomes increasingly powerful as the infrastructure grows from one local data center to multiple locations, and inevitably, to the public cloud.

With the Cisco Cloud ACI solution, organizations can use the same operating model on public cloud instances as they do with their on-premises data centers. This solution provides a single management control point to automate intercloud connectivity, view the health of various cloud deployments, and consistently stretch policies into multiple cloud locations. The Cisco ACI Multi-Site Orchestrator acts as a single manager for multicloud deployments and multiple managed on-premises Cisco ACI data centers. The orchestrator provides a normalized view of cloud sites and an integrated view of application policy and health across a multicloud deployment in a unified dashboard or its REST API.

![](/images/dcaui/dcaui_1_5_1.png)

A host of libraries, tools, and integrations have been built around the Cisco ACI single point of control, the APIC. You now have many powerful tools at your disposal: You can interact with the web GUI on the APIC, inspect and navigate the object model (API Inspector, Visore), and directly call the REST API (Postman, Python, or any other programming language). You can also use abstraction layers to help interact with the API (Cobra SDK, Arya, Cisco ACI Toolkit), or modules built into third-party tools such as Ansible.

![](/images/dcaui/dcaui_1_5_2.png)

The Cisco ACI fabric is orchestrated and maintained via its controllers (APIC)s, using a uniform and standardized design for managing a collection of switches, their configuration, operational data, routing, overlays, security policies, and much more. Given the many features of Cisco ACI and its growth from single fabric to multipod, multifabric, and now multicloud deployments, even with the fabric level abstractions in place, deploying and managing configuration on Cisco ACI is ideally performed in an automated way.

Consider a company that has 20 different applications (with many components) to deploy across its data centers. Traditionally, one would provision servers and their connectivity, then build the necessary infrastructure and traffic policies, and then deploy them, one by one, in each location over several weeks, or perhaps even months.

But what if these applications require identical development, testing, staging, and production environments across all data centers? With four environments, that translates into 80 application instances to deploy. To multiply the work even further, consider that the application developers work on quarterly release models where they require development, testing, staging, and production environments for each major version of their software. In just a year, a team would have to manage 320 application instances across 16 different environments. These numbers snowball quite quickly and become impossible to manage manually with a small team of people.

The ability to automate such operations quickly becomes a critical business requirement, and identifying repeatable, error-prone, but important workflows that are the keys to success and growth. Standardizing and automating the deployment of new application environments shifts the focus from short-term busy work and reduces risk. It also frees up engineering time to focus on other challenges, such as improving designs, optimizing performance, or improving security.

In the coming lessons you will familiarize yourself with the various libraries, tools and integrations, from learning how to interact with the REST API directly, to using Python and its software development kits (SDK)s, then Ansible, and finally running pre-packaged third-party applications on the APIC.


# 2 Describing the Cisco APIC REST API

## 2.1  Introduction

The Cisco Application Centric Infrastructure (ACI) fabric is managed through the Cisco Application Policy Infrastructure Controller (APIC), a central gateway for all operations. You will review the Cisco APIC REST APIs, examine their structure and functions, and practice interacting with the APIs using the API inspector, Visore, and Postman.

## 2.2  Introduction to the Cisco ACI REST API

All the functions of the Cisco APIC are exposed via a REST API. Everything that the APIC web GUI does is implemented via the REST APIs. You will review the functions of the API and learn how the system works by using examples.

The REST API is the main interface into the MIT and it is used by all the various clients you will encounter, such as the APIC CLI, the APIC web interface, and the SDKs.

The APIC API supports the standard REST methods GET, POST, and DELETE. It is important to mention that the POST and DELETE operations are idempotent, which means that there is no additional effect if they are called once or multiple times with the same parameters (thus their result is deterministic).

To create or update a managed object or execute a method, you should use a POST message. You should use GET to query the properties and status of a managed object or discover objects. To delete a managed object, you can use a POST or DELETE message. With POST, you usually delete a managed object by setting its status to “deleted,” but there are exceptions. If you encounter problems, learn by example from the web GUI using the API inspector (described later).

You can choose to use XML or JSON in all operations by specifying the data type in the URL. You do not need to use content headers.

The information in this section summarizes the main points of the REST API. For further details, you can consult the Cisco APIC REST API Configuration Guide, Release 4.0(1) at https://www.cisco.com/c/en/us/td/docs/switches/datacenter/aci/apic/sw/4-x/rest-api-config/Cisco-APIC-REST-API-Configuration-Guide-401.html.

**Note:** Always ensure that you are browsing the documentation pages for your relevant Cisco APIC software release.

### Operations on a Managed Object

You can access managed objects via the API by using the following URL syntax to target a managed object that is a specific instance of an object class.

Syntax:

```bash
  {http|https}://host[:port]/api/mo/dn.{json|xml}[?options]
```

Example:

```bash
  GET https://apic/api/node/mo/topology/pod-1/node-201.json?query-target=self
```

The following are the components of the URI for a request:

* **http:// | https://:** By default, only HTTPS is enabled.

* **host:** This component is the hostname or IP address of the APIC controller, for example, “apic.”

* **:port:** This optional component is the port number for communicating with the APIC controller if a nonstandard port is configured.

* **/api/:** This component specifies that the message is directed to the API.

* **mo | class:** This component specifies the target of the operation as a managed object or an object class.

* **dn:** This component is the DN of the targeted managed object, for example, topology/pod-1/node-201.

* **className:** This component is the name of the targeted class, concatenated from the package and the class in the context of the package, for example, dhcp:Client is dhcpClient. The className can be defined in the content of a DN, for example, topology/pod-1/node-1.

* **json | xml:** This component specifies the encoding format of the command or response body as JSON or XML.

* **?options:** This component includes optional filters, selectors, or modifiers of a query. Multiple option statements are joined by the ampersand (&).

In the example in the figure, the target of the GET request is the managed object with the DN topology/pod-1/node-201. The format of the response data will be JSON, and the query target is self, which means “return only the properties of the managed object itself.”

The following is an example of the response to this request, with the DN highlighted text:

```json
  {
      "totalCount": "1",
      "imdata": [
          {
              "fabricNode": {
                  "attributes": {
                      "adSt": "on",
                      "address": "10.0.48.65",
                      "annotation": "",
                      "apicType": "apic",
                      "childAction": "",
                      "delayedHeartbeat": "no",
                      "dn": "topology/pod-1/node-201",
                      "extMngdBy": "",
                      "fabricSt": "active",
                      "id": "201",
                      "lastStateModTs": "2019-09-07T10:20:57.236+00:00",
                      "lcOwn": "local",
                      "modTs": "2019-09-07T10:21:18.912+00:00",
                      "model": "N9K-C9336PQ",
                      "monPolDn": "uni/fabric/monfab-default",
                      "name": "spine",
                      "nameAlias": "",
                      "nodeType": "unspecified",
                      "role": "spine",
                      "serial": "FDO2102289J",
                      "status": "",
                      "uid": "0",
                      "vendor": "Cisco Systems, Inc",
                      "version": ""
                  }
              }
          }
      ]
  }
```

### Operations on an Object Class

Querying object classes via the API is achieved using the following URL syntax, whereby you query the API for all managed objects that are of a particular class under the given DN. The parts of the URL are the same as the URL components that were described previously for operations on managed objects.

Syntax:

```bash
  {http|https}://host[:port]/api/class/classname.{json|xml}[?options]
```

Example:

```bash
  GET https://apic/api/node/class/topology/pod-1/fabricNode.json?query-target-filter=or(eq(fabricNode.role,"leaf"),eq(fabricNode.role,"spine"))
```

In this example, which was taken from the API Inspector during fabric initialization, the query is for the class fabric:Node in the context of pod-1, contained in the topology. This path returns all fabric nodes that the APIC discovers.

The query-target-filter is an OR statement that is defined to choose clients that are either a leaf or a spine.

The effect of the query is to return all the switch nodes that are known to the system. The following is an example of the response to this request with a lab topology fully discovered—two leafs and one spine—thus three total results, all the class fabricNode. Make note of the individual DN for each managed object that is returned and its role attribute, which has been used as a filter.

```json
  {
      "totalCount": "3",
      "imdata": [
          {
              "fabricNode": {
                  "attributes": {
                      "adSt": "on",
                      "address": "10.0.48.66",
                      "annotation": "",
                      "apicType": "apic",
                      "childAction": "",
                      "delayedHeartbeat": "no",
                      "dn": "topology/pod-1/node-102",
                      "extMngdBy": "",
                      "fabricSt": "active",
                      "id": "102",
                      "lastStateModTs": "2019-09-07T10:22:57.571+00:00",
                      "lcOwn": "local",
                      "modTs": "2019-09-07T10:23:18.948+00:00",
                      "model": "N9K-C9372PX-E",
                      "monPolDn": "uni/fabric/monfab-default",
                      "name": "leaf-b",
                      "nameAlias": "",
                      "nodeType": "unspecified",
                      "role": "leaf",
                      "serial": "FDO2116030V",
                      "status": "",
                      "uid": "0",
                      "vendor": "Cisco Systems, Inc",
                      "version": ""
                  }
              }
          },
          {
              "fabricNode": {
                  "attributes": {
                      "adSt": "on",
                      "address": "10.0.48.64",
                      "annotation": "",
                      "apicType": "apic",
                      "childAction": "",
                      "delayedHeartbeat": "no",
                      "dn": "topology/pod-1/node-101",
                      "extMngdBy": "",
                      "fabricSt": "active",
                      "id": "101",
                      "lastStateModTs": "2019-09-07T06:06:56.832+00:00",
                      "lcOwn": "local",
                      "modTs": "2019-09-07T06:07:14.617+00:00",
                      "model": "N9K-C9372PX-E",
                      "monPolDn": "uni/fabric/monfab-default",
                      "name": "leaf-a",
                      "nameAlias": "",
                      "nodeType": "unspecified",
                      "role": "leaf",
                      "serial": "FDO211602YF",
                      "status": "",
                      "uid": "0",
                      "vendor": "Cisco Systems, Inc",
                      "version": ""
                  }
              }
          },
          {
              "fabricNode": {
                  "attributes": {
                      "adSt": "on",
                      "address": "10.0.48.65",
                      "annotation": "",
                      "apicType": "apic",
                      "childAction": "",
                      "delayedHeartbeat": "no",
                      "dn": "topology/pod-1/node-201",
                      "extMngdBy": "",
                      "fabricSt": "active",
                      "id": "201",
                      "lastStateModTs": "2019-09-07T10:20:57.236+00:00",
                      "lcOwn": "local",
                      "modTs": "2019-09-07T10:21:18.912+00:00",
                      "model": "N9K-C9336PQ",
                      "monPolDn": "uni/fabric/monfab-default",
                      "name": "spine",
                      "nameAlias": "",
                      "nodeType": "unspecified",
                      "role": "spine",
                      "serial": "FDO2102289J",
                      "status": "",
                      "uid": "0",
                      "vendor": "Cisco Systems, Inc",
                      "version": ""
                  }
              }
          }
      ]
  }
```

### Create and Modify Managed Objects

With the previous GET operations, you saw how to query the API for information about the fabric. To add or make configuration changes to the APIC, you need to use a POST request.

The body of a POST operation must contain a JSON or XML document that provides the information necessary to execute the command that the Uniform Resource Identifier (URI) defines. The contents of the document must provide at least the minimum set of properties or parameters that are necessary to identify the managed object, regardless of the data that the command requires.

The following example shows a new tenant managed object that is called “Support,” only if it does not exist already. The URL hierarchy identifies the managed object and the body of the request specifies the class name and its attributes.

Syntax:

```bash
  POST https://apic/api/mo/uni/tn-Support.json
  Body (request payload)
```

```json
  {
      "fvTenant": {
          "attributes": {
              "descr": "The Support Organization Tenant",
              "dn": "uni/tn-Support",
              "name": "Support",
              "rn": "tn-Support",
              "status": "created"
          }
      }
  }
```

The data structure in the document must be a single tree in which all child nodes have a unique DN, with no duplicates. Class names are represented by concatenating the package and class name, so that, for example, "fv:Tenant" becomes "fvTenant."

For JSON documents, the “attributes” element must precede the “children” array or other elements. If an XML data structure contains no children or subtrees, the object element can be self-closing. The API is case-sensitive, so you must use "fvTenant," not "fvtenant."

The maximum size of a body document for commands with /api/ in the URI is 1 MB. When uploading a device package file with "ppi" in the URL, the maximum size is 10 MB.

To create, modify, or delete a managed object, you need to create a JSON or XML data structure that describes the essential properties and children of the object class by using the class description in the Cisco APIC Management Information Model Reference.

In practice, the easiest way to accomplish this task is to query for an instance of the managed object that you want to create, using Visore, for example, and then use the returned document as a template. If there is no instance, then you can use the web GUI to create one and copy the document from the API Inspector.

### Scoping Filters

Scoping filters allow you to limit the scope of the response to an API query. You can scope the response to the first level of an object, or to one or more of its subtrees or children based on the class, properties, categories, or qualification by a logical filter expression.

![](/images/dcaui/dcaui_2_2_1.png)

The target-subtree filter specifies the object classes that are considered when the query-target option is used with a scope other than self. The desired object types are a comma-separated list without spaces and can include multiple object types.

The query-target-filter statement specifies a logical filter to apply to the response. This statement can be used alone or applied after the query-target statement.

The rsp-subtree option specifies whether child objects are included in the response, selects the child objects of a specified object class, and a logical filter to apply to the results. When the value is “children,” the rsp-subtree-class option specifies that only child objects of the specified object class are included in the response. The rsp-subtree-filter expression specifies a logical filter to apply to the child objects.

When you want the rsp-subtree filter to return child objects, the **rsp-subtree-include** option specifies additional contained objects or options to be included in the response. You can specify one or more of the following categories in a comma-separated list: audit-logs, event-logs, faults, fault-records, health, health-records, relations, stats, tasks.

When you want the rsp-subtree option to return child objects, the **rsp-prop-include** option specifies whether to return all properties in the response, only the naming properties, or the configurable properties of the managed objects.

![](/images/dcaui/dcaui_2_2_2.JPG)

![](/images/dcaui/dcaui_2_2_3.JPG)

Multiple filter conditions may be combined using logical operators to improve the granularity of the returned results:

![](/images/dcaui/dcaui_2_2_4.JPG)


The following figure shows a few examples of how multiple filters can be used in GET requests.

Class aaa:User where last name equals “Smith”:

```bash
  https://apic/api/class/aaaUser.json?query-target-filter=eq(aaaUser.lastName,"Smith") 
```

Class fv:BD (bridge domains) where arpFlood property is no (not flooding ARP packets):

```bash
  https://apic/api/class/fvBD.json?query-target-filter=eq(fvBD.arpFlood,"no")  
```

Class fv:Tenant objects where the current health score is less than 90:

```bash
  https://apic/api/class/fvTenant.json?rsp-subtree-include=health,required&rsp-subtree-filter=lt(healthInst.cur,"90") 
```

Class aaa:Domain objects where names are not "infra" or "common":

```bash
  https://apic/api/class/aaaDomain.json?query-target-filter= and(ne(aaaDomain.name,"infra"),ne(aaaDomain.name,"common"))
```

The first query searches for all locally defined users (class aaaUser) on the APIC for which the property lastName is equal to Smith.

The second query searches for all Bridge Domains (class fvBD) for which the property arpFlood is equal to no.

The third query searches for all Tenants (class fvTenant), retrieves their health properties, and filters the results only to tenants that have a health score less than 90.

The final query retrieves all AAA domains (class aaaDomain) that match both conditions of the filter (and)—the name of the domain is not infra or common (ne = not equal).

### Scoping Filters: query-target

The query-target query scoping filter is very useful because it allows you to control the depth of the returned results when querying the API. It has three possible values:

* **self:** (Default) This value considers only the managed object itself, without any of its dependent nodes.

* **children:** This value considers only the children of the managed object, not the managed object itself.

* **subtree:** This value considers the managed object itself and its subtrees.

![](/images/dcaui/dcaui_2_2_5.png)

Here is an example of its usage in a URL, querying for the specific fabric node 201 properties and only those properties (self):

```bash
  https://apic/api/node/mo/topology/pod-1/node-201.json?query-target=self
```

The query mechanisms are very sophisticated, so it is not always obvious how to use the scoping filter options. Examining the calls that are shown in the API Inspector is a good way to see how to use the scoping filters. The subscription mechanisms are also clearly shown in the API Inspector, so that you can get a sense of how subscriptions function.

For further examples and details about syntax and parameters, you can consult the Cisco APIC REST API Configuration Guide document at Cisco.com.

## 2.3  Cisco ACI REST API Clients

The REST API is the main interface into the MIT and is used by all the various clients you will encounter, such as the APIC web interface, the APIC CLI, and the SDKs.

### API Authentication

Before you can start performing any API operations on managed objects and classes, you must first log in with the name and password of a configured user and obtain a token to use with further requests. The various API client user interfaces usually perform this operation for you transparently while presenting necessary information in a user-friendly way (that is, a login page for the web interface, a login prompt on the APIC CLI).

Every time that you want to query the API directly (such as with Postman or Python), you will have to first pass the authentication step to obtain the APIC session cookie and periodically refresh its remaining validity.

![](/images/dcaui/dcaui_2_3_1.png)

The login operation is a POST to aaaLogin, as shown in the figure. The body of the POST is a document that contains the username and password. The response contains a token that represents the session. To maintain the session, you must send log in refresh messages if no other messages are sent for a period longer than the session timeout period, which is 600 seconds by default. The token changes each time the session is refreshed.

The main operations are as follows:

* **aaaLogin:** This operation is a POST message that contains a "aaa:User" object with the name and password attributes. The response contains a session token. If multiple AAA login domains are configured, you must prepend the user's name with “apic:domain\\”.

* **aaaRefresh:** This operation is a GET message, or a POST message with the "aaaLogin" message body. This operation resets the session timer. The response contains a new session token.

* **aaaLogout:** This operation is a POST message that logs out the user and closes the session. The message body contains an "aaa:User" object with the name attribute.

* **aaaListDomains:** This operation is a GET message that returns a list of valid AAA login domains. You can send this message without logging in.

If stronger security is needed, you can require that your session use a challenge token with the request option “?gui-token-request=yes”. The response contains a token string that you must include in every subsequent message to the API. This challenge token is not stored as a cookie.

The response will contain an attribute of the form "urlToken:" "token," where the token is a long string of characters. All subsequent messages to the API during this session must include the challenge token, like the following example:

```bash
  GET https://apic/api/class/aaaUser.json?challenge=fa47e44df54562c24fef6601dc...
```

The challenge token can also be expressed as the value of a header using APIC-challenge, as shown here:

```bash
  GET //api/class/aaaUser.json
  HTTP/1.1
  Host: apic
  …
  APIC-challenge: fa47e44df54562c24fef6601dcff72259299a077336aecfc5b012b036797ab0f
```

### Cisco APIC Web GUI

Now you will look at the REST API-related interactions and functionality, which are very helpful when interacting directly with the APIC REST API.

![](/images/dcaui/dcaui_2_3_2.png)

Everything that the Cisco APIC web GUI does is implemented via REST APIs that are exposed by the APIC controller. All objects and classes can be addressed via a URL, the operation POST, GET, or DELETE, and the payloads for both requests and responses can be either JSON or XML. Results can be narrowed down to specific conditions using query scoping or continuously streamed through subscriptions.

One of the best ways to understand how to use the REST API is to see which API calls the web GUI makes when you use it for different functions. The API Inspector can help you see these calls.

When you need to understand how to navigate the object model to get data and see the detail of the data structures, the Visore Object Model Browser is the tool of choice. It is especially useful for copying the query URIs from Visore for GET queries in your own client of choice (Postman, Python, curl, and so on).

Everything can be done via the REST API, but how to do certain things is not always obvious. In particular, it is important to use the API Inspector to see the body of specific POST requests. You can copy the JSON body from the API Inspector, and use that body in your own code.

### Cisco APIC API Inspector

The figure shows the API Inspector. It is available in the Help and Tools drop-down menu (see the gear-shaped icon shown on the left). The API Inspector appears in a separate browser window, which is illustrated on the right in the figure.

![](/images/dcaui/dcaui_2_3_3.png)

The API Inspector shows the REST APIs that the APIC GUI application uses as it interacts with the APIC server. These APIs are the same REST APIs that you would use. A very good way to find out which API you need for a given use case is to try the use case in the APIC GUI, and use the API Inspector to see which REST API is being called.

For example, in the following figure you can see one of the requests that the APIC GUI sends to the API when you open the Tenants view. First, it queries the API for the list of tenants. Then it retrieves their properties to display in the GUI: name, alias, description, bridge domains, VRF instances, EPGs, and health score.

![](/images/dcaui/dcaui_2_3_4.png)

### Browse the Object Model with Visore

Visore is a valuable tool for browsing and running queries against any objects within the Cisco ACI fabric. Visore allows you to query both logical and concrete objects, if they are actually instantiated on a particular node. Visore returns all properties of any object that is queried, including any statistics and faults.

To start using it, you can open Visore (Object Store Browser) from the Help and Tools gear-shaped icon, as illustrated on the left. The Object Store Browser appears in a separate browser tab, which is illustrated on the right in the figure. You have to authenticate again in the Visore window with the same credentials that you use for the Cisco APIC login.

You may also directly navigate to https://apic/visore.html.

![](/images/dcaui/dcaui_2_3_5.png)

The Visore interface allows you to build queries and to navigate up and down the MIT. You can search based on class names or DNs, and further add filters for property values and names. The initial view is that of the class fabricNode.

![](/images/dcaui/dcaui_2_3_6.png)

By filtering in Visore, you can restrict the view to a single object’s DN, or a specific set of objects (class) across the entire fabric. Each DN has five navigation buttons, which will take you to the following parts of the MIT:

* Parent of this managed object
* Children of this managed object
* Statistics (if supported)
* Faults for this managed object
* Health score for the managed object

![](/images/dcaui/dcaui_2_3_7.png)

Navigating up and down the tree is possible via the left and right green arrows. Statistics, faults, and health scores can be returned per object if applicable.

To find these objects, you can search the MIT by class name or by DN. Searching by class name will return all objects of that class type, for example, fvTenant = all Tenants and fvAEPg = all EPGs.

![](/images/dcaui/dcaui_2_3_8.png)

Visore also includes a link to Display URI of last query, which is extremely helpful if you are also using the REST API in native languages such as Python.

![](/images/dcaui/dcaui_2_3_9.png)

In comparison to the previous figure, this figure shows how to perform a query using an object’s DN. In this case, the query is for exactly one managed object, which is the common tenant.

### Cisco APIC CLI

Beginning with Cisco APIC Release 1.2, the APIC provides a Cisco Nexus Operating System (NX-OS) style CLI by default. You can also access the bash shell and run commands that interact directly with managed objects by typing bash at the initial CLI prompt.

To connect to the Cisco APIC CLI, simply open a Secure Shell (SSH) connection to the APIC and log in using your credentials.

The Cisco NX-OS style CLI is organized in a hierarchy of command modes with EXEC mode as the root, containing a tree of configuration submodes beginning with global configuration mode. The commands that are available to you depend on the mode you are in. To obtain a list of available commands in any mode, type a question mark (?) at the system prompt.

For example, to work with tenants, you can view the list of all tenants that are currently created on the Cisco APIC (**show tenant**), then create, change properties, and delete a tenant with the familiar Cisco syntax. These commands are only a subset of the full object model and provide a familiar interface for the most common operations from the CLI as opposed to a GUI.

**Important:** The CLI configuration comes with some restrictions and sometimes, such as for L3ExtOut, configuration created through the CLI cannot be modified through the Cisco APIC web GUI.

```bash
  apic1# show tenant
  Tenant           Tag              Description
  ---------------  ---------------  ----------------------------------------
  common
  infra
  mgmt
  Sales
  WebHosting                        Tenant for hosting websites.

  apic1# configure
  apic1(config)# tenant Support
  apic1(config-tenant)# description 'New Tenant created from APIC CLI'
  apic1(config-tenant)# end

  apic1# show tenant
  Tenant           Tag              Description
  ---------------  ---------------  ----------------------------------------
  common
  infra
  mgmt
  Sales
  Support                           New Tenant created from APIC CLI
  WebHosting                        Tenant for hosting websites.

  apic1# configure
  apic1(config)# no tenant Support
  apic1(config)# end

  apic1# show tenant
  Tenant           Tag              Description
  ---------------  ---------------  ----------------------------------------
  common
  infra
  mgmt
  Sales
  WebHosting                        Tenant for hosting websites.
```

# 3 Using Python to Interact with the Cisco ACI REST API

## 3.1  Introduction

The Cisco Application Centric Infrastructure (ACI) programmability model allows complete programmatic access to the ACI. With this access, customers can integrate network deployment into management and monitoring tools, and they can deploy new workloads programmatically. The Cisco ACI fabric is configured using an abstract policy model on the Cisco Application Policy Infrastructure Controller (APIC), which provides a rich and complete object model that is accessible through a programmatic REST API. The API accepts and returns HTTP or HTTPS messages that contain JSON or XML documents. You can use a programming language to generate messages and JSON or XML documents that contain the API methods or managed object descriptions. In addition to the standard REST interface, Cisco provides several open-source tools or frameworks such as the Cisco ACI Toolkit, Cobra (Python), ACIrb (Ruby), Puppet, and Ansible to automate and program the Cisco APIC.

## 3.2  Python for Cisco ACI Automation

A rich ecosystem of tools and libraries exists for interaction, management, and troubleshooting of Cisco ACI. A great starting point is the Cisco DevNet ACI Programmability documentation page (https://developer.cisco.com/docs/aci/), which contains guides, links to documentation for the various libraries, many tools, and other miscellaneous learning resources. You will now learn about using Python to interact with the API directly, through the Cisco ACI Toolkit, and (in a later topic) the Python Cobra SDK.

### Direct API Interaction with Python Requests
The Cisco APIC REST API is primarily an HTTP API. An HTTP client library can be used to send requests to the API and receive its responses. In Python, the most popular HTTP library is called “requests.”

Because you are familiar with the basics of working with Python requests, you will now look at the main differences you will see when working with the Cisco APIC REST API.

Before you can start sending other requests to the Cisco APIC REST API, you must first authenticate. This process is covered in Section 2. In the figure, you can see sample code that performs user and password authentication.

```python
  import requests
  import json
  url = "https://apic/api/aaaLogin.json"
  credentials = {
      "aaaUser": {"attributes": {"name": "admin", "pwd": "secret_password"}}
  }
  response = requests.post(url, data=json.dumps(credentials), verify=False)
```

The response object contains important information such as the status_code (this code should be 2xx for success, or 4xx or 5xx for errors) and the response body (if the API returns something to you, which is not the case every time).

For a successful authentication, you can see how these fields appear in the figure.

```bash
  >>> response.status_code
  200
  >>> response.text
  '{"totalCount":"1","imdata":[{"aaaLogin":{"attributes":{"token":"mAYBAAAAAAAAAAAAAAAAAFXFnTMKitQWZNNfvSLbgnZTVfu7PG9ymOAb7FQKb/oD/ZKehRw3ZmLpfrA3ZQi8hUjyUo2x2ps67EKVs1Xzo2BsPwNL3h/jUMzF5F8/pf+eKyQMjuAzqJZOJkmNHlYH+2meHYQhTXLE77j8J1EqUPgmFu/Kvc7MyNCQQSdLWwHQRekKUtibffncSzivYbbJng==","siteFingerprint":"59Y6ii6fuq6NadCv","refreshTimeoutSeconds":"600",... OUTPUT TRIMMED}'
  >>> response.cookies['APIC-cookie']
  'mAYBAAAAAAAAAAAAAAAAAFXFnTMKitQWZNNfvSLbgnZTVfu7PG9ymOAb7FQKb/oD/ZKehRw3ZmLpfrA3ZQi8hUjyUo2x2ps67EKVs1Xzo2BsPwNL3h/jUMzF5F8/pf+eKyQMjuAzqJZOJkmNHlYH+2meHYQhTXLE77j8J1EqUPgmFu/Kvc7MyNCQQSdLWwHQRekKUtibffncSzivYbbJng=='
```

If authentication is successful as noted in the figure, the Cisco APIC response also sets a cookie that is called “APIC-cookie” that you can send with further requests to the API instead of placing the token inside an HTTP header. This approach is especially convenient with tools like Postman, which manages and saves cookies for you per session (just like a browser).

Building on the authentication flow, this next example uses the freshly obtained authentication cookie to send a request to the Cisco APIC API for the list of all tenants on the fabric (a class-based query for all objects of type fvTenant).

```bash
  >>> url = "https://apic/api/class/fvTenant.json"
  >>> response = requests.get(url, cookies=apic_cookies, verify=False)
  >>> response.status_code
  200
  >>> response.text
  '{"totalCount":"4","imdata":[{"fvTenant":{"attributes":{"annotation":"","childAction":"","descr":"","dn":"uni/tn-mgmt","extMngdBy":"","lcOwn":"local","modTs":"2019-09-07T06:04:20.673+00:00","monPolDn":"uni/tn-common/monepg-default","name":"mgmt","nameAlias":"","ownerKey":"","ownerTag":"","status":"","uid":"0"}}},{"fvTenant":{"attributes":{"annotation":"","childAction":"","descr":"","dn":"uni/tn-common","extMngdBy":"","lcOwn":"local","modTs":"2019-09-07T06:04:14.336+00:00","monPolDn":"uni/tn-common/monepg-default","name":"common",... OUTPUT TRIMMED}'
```

### Cisco ACI Toolkit

The Cisco ACI Toolkit exposes a small subset of the Cisco ACI object model to introduce the Cisco ACI concepts and allows you to get the most common workflows operating as quickly as possible.

![](/images/dcaui/dcaui_3_2_1.png)

The Cisco ACI Toolkit documentation is available at: https://developer.cisco.com/site/aci/docs/apis/aci-toolkit/. A support forum may be found on Cisco.com Communities.

The main goals of the Cisco ACI Toolkit are as follows:

* Ease the learning curve that is associated with learning and using Cisco ACI and the Cisco APIC REST APIs
* Allow you get started doing something useful quickly
* Address most of the common use cases
* Provide examples and sample scripts on which you can build

The Cisco ACI Toolkit code is preinstalled on the Student Workstation for direct usage within Python scripts as a package, but you may find the latest versions of the code, examples, and applications at https://github.com/datacenter/acitoolkit.

There are three core components of the Cisco ACI Toolkit: Python bindings for the object model, applications, and sample scripts.

### Cisco ACI Toolkit Python Library (Classes)

A subset of the object model is exposed via the Cisco ACI Toolkit to facilitate building the most commonly used workflows.

These workflows can be found in the acitoolkit package (https://github.com/datacenter/acitoolkit/tree/master/acitoolkit).

The object model that the Cisco ACI Toolkit exposes is focused on three main areas: application topology, interfaces, and physical topology. These areas are reviewed a bit later in this section.

![](/images/dcaui/dcaui_3_2_2.png)


### Cisco ACI Toolkit Applications

The Cisco ACI Toolkit also comes with prebuilt applications that target common use cases. You will cover Applications in more detail later in this topic.

### Cisco ACI Toolkit Sample Scripts

In the Cisco ACI Toolkit repository, you may find over 50 prebuilt scripts that allow you to perform common operations. These scripts give you a starting point for creating configuration or running familiar show commands against Cisco APIC configuration for policy objects such as contexts (virtual routing and forwarding instances), bridge domains, tenants, EPGs, filter entries, and so on.

![](/images/dcaui/dcaui_3_2_3.png)

### Cisco ACI Toolkit Application Topology Object Model

The Cisco ACI Toolkit application topology model encompasses the concepts of the tenant, application profile, EPG, VRF context, subnet, and bridge domain.

![](/images/dcaui/dcaui_3_2_4.png)

These concepts are described as follows:

* **Tenant:** This class is the root class within the acitoolkit object model hierarchy. All the application topology configuration occurs within a tenant.
  
* **AppProfile:** This class is the application profile class. It contains the configuration for a given application.
  
* **EPG:** This class is the endpoint group class. It is the object for defining configuration that is applied when endpoints connect to the fabric.
  
* **Context:** This class represents a Layer 3 namespace (generally, a traditional VRF instance in Cisco terminology).
  
* **BridgeDomain:** This class is the class representing a Layer 2 forwarding domain (generally, a traditional VLAN). It is associated with a single context.
  
* **Subnet:** This class is the class representing a Layer 3 subnet. It is associated with a single bridge domain.
  
* **OutsideEPG:** This class represents an EPG that connects to the world outside the fabric. It is where routing protocols such as OSPF are enabled.
  
* **Contract:** This concept defines the application network services that EPGs provide and consume. EPGs may provide and consume many contracts.
  
* **Taboo:** This class defines the application network services that EPGs can never provide or consume.
  
* **FilterEntry:** This class is contained within a contract or a taboo. It defines the traffic profile that the contract or taboo applies.

### Cisco ACI Toolkit Interface Object Model

The Cisco ACI Toolkit interface model describes how interfaces link the logical application topology to the underlying physical network topology. The classes in the interface model are connected through the relationships that are shown in the figure.

![](/images/dcaui/dcaui_3_2_5.png)

From a terminology perspective, link aggregation is a logical data link layer interface that is composed of one or more physical interfaces. These interfaces are commonly called an EtherChannel or PortChannel.

Physical interfaces are, for example, the RJ-45 ports into which Ethernet cables are plugged. The endpoints attach to these interfaces.

A PortChannel is the logical aggregated Ethernet port that link aggregation forms, which is created by attaching one or more interface instances to a PortChannel instance; for example, five 40-GB ports on a switch could be combined to form a logical port with 200 GB of capacity. Technically, a virtual port channel, or virtual port channel (vPC), composes interfaces on two distinct switches. In the toolkit, vPCs are also represented by the PortChannel class.

The L2Interface class represents the logical Layer 2 network attachment on an Ethernet interface. This interface could be an instance of an Interface class or PortChannel class, because both are representations of the data link layer Ethernet interfaces. Multiple such logical Layer 2 network attachments can occur on the same Ethernet interface, but the encapsulations must differ by identifier or type. The types of encapsulation include VLAN, VXLAN, and Network Virtualization Using Generic Routing Encapsulation (NVGRE).

The L3Interface class represents the logical Layer 3 network attachment on a Layer 2 interface and it is where the IP address resides. The OSPFInterface class represents the logical router interface that routes from the Layer 3 interface instance. It contains the Open Shortest Path First (OSPF)-specific interface configuration.

### Cisco ACI Toolkit Physical Topology Object Model

The Cisco ACI Toolkit physical topology model is made up of three hierarchies, which define the various nodes, links, and their grouping as pods within the Cisco ACI fabric.

![](/images/dcaui/dcaui_3_2_6.png)

The Pod class represents the physical pod that contains all switches, links, and controllers that are connected in the Cisco ACI fabric. The pod does not include the endpoints or other devices that are attached to the Cisco ACI fabric.

The Node class represents the switches and the controllers, where the role determines what the node is. Switches are nodes with the role of leaf or spine. Controllers are nodes with the role of controller.

The Link class represents links between leaf and spine switches, and links from leaf switches to controllers. The Link class has methods for retrieving the switch, line card, and interface for each of the ends of the link.

Switches are composed of the elements that are shown on the left in the figure. The parts of a switch are as follows:

The Supervisor class represents the supervisor function in a switch. The supervisor is where the primary software of the switch runs. Not all switches have a physically distinct card on which the supervisor runs, but it is logically present.

The Linecard class represents a line card where all the physical interfaces or ports are attached. The slot_id attribute identifies a particular line card in a switch. Like the supervisor card, not all switches have distinct physical line cards and some may only have one such line card. The interfaces are located on the line cards.

The Powersupply class represents a power supply in a node. Monitoring the power supply is important, and it is common for a switch to have redundant power supplies.

The Fantray class represents a fan tray in a node. Controlling the node temperature is vital for its proper functioning, and nodes often have redundant fans.

For the controller node, the Systemcontroller class represents the motherboard of a Cisco APIC controller, where the version of software that is running in the controller is found. The Powersupply and Fantray classes have the same logical meanings that they do with a switch.

### Cisco ACI Toolkit Applications

The Cisco ACI Toolkit provides the applications that are shown in the figure.

![](/images/dcaui/dcaui_3_2_7.png)

The functionality of a few of these applications is described here:

* **ACI Endpoint Tracker:** The ACI Endpoint Tracker application tracks all the attachment, detachment, and movement of endpoints on the Cisco ACI fabric. This activity is stored in a database that provides a foundation for visualization and querying tools.

* **ACI Lint:** This application is a static analysis tool for Cisco ACI fabrics that supports configuration analysis, compliance, governance, and auditing.

* **Cableplan:** This application supports importing cable plans from XML files, importing the currently running cable plan from a Cisco APIC, exporting previously imported cable plans to a file, and comparing cable plans.

* **Snapback:** Snapback is a configuration snapshot and rollback tool for Cisco ACI fabrics. The tool supports versioned snapshots of the running Cisco ACI fabric configuration, immediate or scheduled, with differential analysis and rollback capabilities.

* **Visualization Examples:** This application can be used to display information that is collected using the Cisco ACI Toolkit. Many of the examples are meant to run with the ACI Endpoint Tracker application and interact with the MySQL database that the ACI Endpoint Tracker populates.

* **Event Feeds:** This application subscribes to Cisco APIC Managed Objects and records updates to the objects. Updates can be viewed in various Any Transport over MPLS (AToM) feeds provided over HTTP.

* **Multisite:** This application allows application policies to be applied across Cisco ACI fabrics. Policies are configured on an EPG basis. When endpoints appear in an EPG that is configured as an intersite EPG, the endpoint and EPG information is propagated to the remote sites per the intersite policy.

* **Connection Search:** This application allows you to search for connections between endpoints on the Cisco ACI fabric.

* **Fake APIC:** This application is designed to view managed objects (and their properties) based on JSON configuration files. It works as an offline tool when you may not have access to the Cisco APIC, but still want to see certain (or all) managed objects on the network.

* **ACI Reports:** This application is a collection of reporting tools for the Cisco APIC logical and physical model.

* **APIC Test Harness:** This application wraps the Fake APIC into an application server so that toolkit applications can run against the test harness. The test harness can be used to execute toolkit scripts and applications against a Cisco APIC snapshot. The test harness is especially useful for creating conditions that are very difficult to create in real systems such as communication failures, connection resets, slow responses, and response timeouts.


# 4 Using Ansible to Automate Cisco ACI

## 4.1 Introduction

Ansible is a powerful and flexible framework for automation in general. Although you may use its generic URI module to interact with the APIC REST API as a front end to Python Requests, many modules that are specific to Cisco Application Centric Infrastructure (ACI) exist in Ansible that facilitate manipulating the APIC policy. This section focuses on these modules, how to access their documentation, and some of the specific details that are particular to using them in practice. You will then implement multiple playbooks that manage APIC objects, apply infrastructure-as-code concepts to building configuration, and perform validation and reporting workflows that generate an infrastructure health report.

##  4.2 Ansible ACI Modules

Ansible ships with many modules that help you manage objects on the Cisco APIC. As of Ansible version 2.8, there are 67 Cisco ACI modules and 33 Cisco ACI Multi-Site Orchestrator (MSO) modules.

Documentation for these modules can be found under the **Network Modules** category, which is easily accessed by navigating to https://docs.ansible.com and following the links. The specific Cisco ACI modules are all prefixed with aci_* (for example, aci_epg or aci_tenant) and the modules for the Cisco ACI MSO are prefixed with mso_* (for example, mso_role or mso_template). In this environment, you have an ACI fabric, so understanding the ACI modules will be the focus.

![](/images/dcaui/dcaui_4_2_1.png)

When opening the Ansible Docs online, pay special attention to the version of Ansible for which the documentation is written. In the top-left part of the page, you can find the version to which the page applies and a drop-down list to select previous versions.

The URL will not show the version when browsing the latest Ansible version, in this case version 2.8. The content of these pages under the latest version will change over time as new major versions of Ansible are released (for example, version 2.9, 2.10, and so on).

https://docs.ansible.com/ansible/latest/modules/aci_tenant_module.html#aci-tenant-module

**Note:** Ansible version 2.10, for example, introduced some differences how modules, such as Cisco ACI and Cisco ACI MSO modules, are installed and documented.

![](/images/dcaui/dcaui_4_2_2.png)

Each individual module documentation page follows the standard structure and provides information about the following:

* The version in which the module was added to Ansible
* A summary of what the module does
* The Python libraries that are required to execute the module
* The parameters that you can provide to the module
* Examples
* Return values: The variables that the module returns after execution
* Status: Who maintains the module

The Ansible ACI modules are certified, which means that an Ansible partner maintains them; this information is documented at https://docs.ansible.com/ansible/latest/user_guide/modules_support.html#certified.

Finally, there is an official Ansible Network: ACI community that reviews, comments, documents, fixes issues, and works on the plan for the ACI and MSO modules that are hosted on GitHub: https://github.com/ansible/community/wiki/Network:-ACI


**Use a Cisco ACI Module**
The Ansible ACI modules are essentially an abstracted interface to the Cisco APIC REST API. Except the aci_rest module, which is discussed later, Cisco ACI modules are each used to manage a particular object on the Cisco ACI MIT. For example, the aci_epg module only addresses EPG-managed objects (class fvAEPg in Cisco ACI).

The Cisco ACI modules do not provide complete coverage of the Cisco ACI MIT. Similar to the ACI Toolkit, these modules cover the objects that are used in most common workflows, and therefore provide a subset of the available REST API functionality. Keep in mind that a particular module may not expose all the attributes and relationships that can be created via the API. If you find that a module needs additional functionality, you have two options: add the required functionality to the module yourself and contribute it to Ansible, or use a generic REST API module like aci_rest or uri. The latter method is the Ansible equivalent workflow of using Postman or Python requests with your own custom-built APIC API requests.

Standard Ansible modules execute on the host that they manage. In Cisco ACI modules, because they are acting like HTTP clients calling the APIC REST API, the Python code (modules are written in Python) has to run on the Ansible control host (or on another system that can connect to the APIC).

Usually, the case is that the playbooks will delegate task execution to the local host, which is the same machine that is executing the playbook. This delegation is done by adding the parameter delegate_to: localhost to each task that needs to execute locally.

As an alternative to task delegation, you may also define the ansible_connection: local variable, which instructs Ansible to let the module manage its own connection and execute locally on the control host. Instead of having to define delegation for each task, you may define this variable only once in the inventory or at the playbook level.

Note: The differences between connection-local and delegate-to-localhost are beyond the scope of this discussion, but you can be read more information on this subject at https://docs.ansible.com/ansible/latest/user_guide/playbooks_delegation.html#local-playbooks. Examples here use delegate_to: localhost for improved clarity and to reinforce local execution.

Because modules do not execute on the Cisco APIC, the built-in Ansible fact gathering must be disabled. You can disable this function using ansible.cfg (gathering = explicit) or by adding gather_facts: false in the playbook.

**Module Example: Create a Tenant**
To better understand how to use Ansible ACI modules, the following is an example using aci_tenant to create a tenant that is called Support.

The following parameters of the aci_tenant module are taken from the following Ansible Documentation page: https://docs.ansible.com/ansible/latest/modules/aci_tenant_module.html#parameters. Note that a few of these parameters are required. Documentation is invaluable when determining the parameters that you need to provide to a module and some of the default values that will be silently used.

![](/images/dcaui/dcaui_4_2_3.png)

Cisco ACI modules need to be told where and how to connect to the APIC including the hostname or IP and the authentication details, in this case, the username and password. In this example, the module is also told not to validate the APIC certificate, which is useful when you are using self-signed certificates (such as in an educational lab environment).

**Note:** It is good practice not to embed credentials in your playbooks. Instead, use Ansible host variables or prompt at playbook run time. If you have to save credentials in the inventory, you should do so in an encrypted format using tools like Ansible Vault.

The spacing that is added after the connection details exists only to make reading easier. You will notice when working with Cisco ACI modules that these details repeat for every ACI module that you are using.

Finally, the module needs to know the name of the tenant (Support) and the operation to execute; state: present tells the module to ensure that this tenant exists on the APIC.

```bash
  - name: CREATE A TENANT
    hosts: apic
    
    tasks:
      - name: Create Support Tenant
        aci_tenant:
          host: "apic"
          user: "admin"
          password: "cisco"
          validate_certs: false

          tenant: "Support"
          state: present
        delegate_to: localhost
```

This module is idempotent, so if the tenant exists on the APIC, the module will not create it again. You can verify this behavior by running playbooks multiple times and by increasing the verbosity level to see more of what the module is doing. For example, the following output shows the module output with increased verbosity (-v on the command line and output_level: info in the module parameters) both when the Support tenant has to be created and when it already exists on the APIC.

```bash
  # Tenant does not exist and module will create it (changed)

  TASK [Create Support Tenant] **************************************************************************************
  changed: [apic -> localhost] => {"changed": true, "current": [{"fvTenant": {"attributes": {"annotation": "", "descr": "", "dn": "uni/tn-Support", "name": "Support", "nameAlias": "", "ownerKey": "", "ownerTag": ""}}}], "previous": [], "proposed": {"fvTenant": {"attributes": {"name": "Support"}}}, "sent": {"fvTenant": {"attributes": {"name": "Support"}}}}

  # Tenant exists and module does nothing (ok)
```

```bash
  # Tenant exists and module does nothing (ok)

  TASK [Create Support Tenant] **************************************************************************************
  ok: [apic -> localhost] => {"changed": false, "current": [{"fvTenant": {"attributes": {"annotation": "", "descr": "", "dn": "uni/tn-Support", "name": "Support", "nameAlias": "", "ownerKey": "", "ownerTag": ""}}}], "previous": [{"fvTenant": {"attributes": {"annotation": "", "descr": "", "dn": "uni/tn-Support", "name": "Support", "nameAlias": "", "ownerKey": "", "ownerTag": ""}}}], "proposed": {"fvTenant": {"attributes": {"name": "Support"}}}, "sent": {}}
```

If you require even more detailed troubleshooting, you can set output_level: debug for the task. Then the module shows you all the details about the API call that it is making, such as the URL, method, response, and so on.

```bash
  TASK [Create Support Tenant] **************************************************************************************
  ok: [apic -> localhost] => {"changed": false, "current": [{"fvTenant": {"attributes": {"annotation": "", "descr": "", "dn": "uni/tn-Support", "name": "Support", "nameAlias": "", "ownerKey": "", "ownerTag": ""}}}], "filter_string": "?rsp-prop-include=config-only", "method": "GET", "previous": [{"fvTenant": {"attributes": {"annotation": "", "descr": "", "dn": "uni/tn-Support", "name": "Support", "nameAlias": "", "ownerKey": "", "ownerTag": ""}}}], "proposed": {"fvTenant": {"attributes": {"name": "Support"}}}, "response": "OK (168 bytes)", "sent": {}, "status": 200, "url": "https://apic:443/api/mo/uni/tn-Support.json"}
```

### Create Tenant Infrastructure

Putting a few of these Ansible ACI modules together makes automation powerful, and for many common tasks, as you can see in the following example, Ansible makes it very straightforward. The next example is a whole playbook that creates a tenant and a VRF instance, a bridge domain, and a subnet that belong to the tenant.

Also, note that instead of repeating hardcoded connection details for each module, you can define them in your inventory and then reference them in the playbooks. It is always a good idea to make the playbooks more portable and independent if possible.

The first task creates a tenant that is called Support; this task should be familiar from earlier. The second task creates a VRF instance, but a VRF instance is an object in the Cisco ACI that needs to belong to a tenant. Therefore, it has to have a parameter in which you specify that the Support_VRF object is a child of the Support tenant object.

A bridge domain object also belongs to a tenant, but it also has a relationship to a VRF instance, so the module requires both parameters. Finally, the bridge domain subnet belongs to a bridge domain under a specific tenant. Take some time to look at these tasks and understand the relationships between the objects; look them up in the Ansible documentation (reminder: https://docs.ansible.com, choose **Network Modules**).

```bash
  - name: CREATE TENANT INFRASTRUCTURE
    hosts: apic

    tasks:

      - name: Create Tenant i
        aci_tenant:
          host: "{{ ansible_host }}"
          user: "{{ ansible_user }}"
          password: "{{ ansible_password }}"
          validate_certs: "{{ validate_certs }}"

          tenant: "Support"
          description: "The Support Organization."
          state: present
        delegate_to: localhost
```

```bash
  - name: Create VRF
        aci_vrf:
          host: "{{ ansible_host }}"
          user: "{{ ansible_user }}"
          password: "{{ ansible_password }}"
          validate_certs: "{{ validate_certs }}"

          tenant: "Support"
          vrf: "Support_VRF"
          description: "VRF for Support Tenant. Managed by Ansible."
          state: present
        delegate_to: localhost
```

```bash
      - name: Create Bridge Domain
        aci_bd:
          host: "{{ ansible_host }}"
          user: "{{ ansible_user }}"
          password: "{{ ansible_password }}"
          validate_certs: "{{ validate_certs }}"

          tenant: "Support"
          vrf: "Support_VRF"
          bd: "Support_BD"
          description: "BD for Support Tenant Users. Managed by Ansible."
          state: present
        delegate_to: localhost
```

```bash
      - name: Create Bridge Domain Subnet
        aci_bd_subnet:
          host: "{{ ansible_host }}"
          user: "{{ ansible_user }}"
          password: "{{ ansible_password }}"
          validate_certs: "{{ validate_certs }}"

          tenant: "Support"
          bd: "Support_BD"
          gateway: "10.100.0.1"
          mask: "24"
          description: "Subnet for Support Tenant Users. Managed by Ansible."
          state: present
        delegate_to: localhost
```

Here is an example of the inventory file for this playbook.

```bash
  # The ACI Controllers
  [aci]
  apic ansible_host=192.168.0.15

  [aci:vars]
  ansible_user=admin
  ansible_password=cisco
  validate_certs=false
```

The output from executing this playbook, if none of the intended objects exist in the APIC policy, will look like the following. Note that all tasks report changed because they have created or modified configuration:

```bash
  PLAY [CREATE TENANT INFRASTRUCTURE] ****************************************************

  TASK [Create Tenant] *******************************************************************
  changed: [apic -> localhost]

  TASK [Create VRF] **********************************************************************
  changed: [apic -> localhost]

  TASK [Create Bridge Domain] ************************************************************
  changed: [apic -> localhost]

  TASK [Create Bridge Domain Subnet] *****************************************************
  changed: [apic -> localhost]

  PLAY RECAP *****************************************************************************
  apic                       : ok=4    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### aci_rest Module

Sometimes the specialized modules do not provide the full API coverage of the object model that you need or, in the case of less common workflows, may not exist at all. The aci_rest module helps in this situation; it allows you to craft and send any API REST calls to the APIC.

As you might expect, this module requires connection details (APIC host and authentication credentials). In addition to these details, this module acts more like Postman or Python requests: you must tell the module which path to access in the API (the URI), which HTTP method to use (APIC supports POST, GET, DELETE), and optionally provide a payload.

The module accepts payloads in both JSON and XML formats, but also supports writing JSON payloads as YAML data. Keep in mind that if you provide an XML payload, the path also needs to end in .xml.

The following payloads are all equivalent in functionality, but different in presentation.

```bash
  - aci_rest:
      method: post
      path: /api/mo/uni.xml
      content: |
        <fvTenant name="Support" descr="The Support Organization."/>

  - aci_rest:
      method: post
      path: /api/mo/uni.json
      content:
        {
          "fvTenant": {
            "attributes": {
              "name": "Support",
              "descr": "The Support Organization."
            }
          }
        }
```

```bash
  - aci_rest:
      method: post
      path: /api/mo/uni.json
      content:
        fvTenant:
          attributes:
            name: Support
            descr: The Support Organization.
```

For most operations, this module is idempotent. If you set the status of the object in the payload of the request explicitly, keep in mind that using “status: created” is not idempotent. If you try to create an object that already exists, the APIC API will return an error. If you need idempotency, use “status: modified.”

Certain paths in the APIC MIT are not idempotent by design; an example is user passwords when creating users locally on the APIC. Trying to set the same password multiple times will cause an error because the API fails the password historical reuse check.

## 4.3 Manage Configuration using Ansible

Ansible provides many modules (aci_*) dedicated to working with the Cisco Application Centric Infrastructure (ACI) object model. With small exceptions (for example, aci_rest), modules are designed to manage the configuration of one Cisco ACI-managed object at a time.

You will start by setting up the necessary Ansible files—the inventory, configuration overrides, and connection variables—which will be used by all playbooks when performing their tasks. Then the aci_tenant module is introduced, allowing you to query, then create, and finally delete tenants from Ansible. In case the specific modules are not available or they don't support the full range of object attributes, the aci_rest module allows you to send full Application Programming Interface (API) calls to the Cisco Application Policy Infrastructure Controller (APIC) from within the playbook.

## 4.4 Set Up a New Tenant the NetDevOps Way

Ansible’s large number of Cisco ACI modules aci_* provide an accessible interface to the most common workflows. You will use various modules to build a complete tenant configuration from scratch – that includes VRFs, Bridge Domains, EPGs, Filters, and Contracts.

The playbooks you are writing in this lab will have a minimum of hardcoded values, one of the goals being to write modular and reusable code. To achieve this goal, it is important to understand the design of the tenant, how it is represented as a data model, and finally how the data model is used by Ansible to deploy the configuration to the lab APIC.

## 4.5 Create an Infrastructure Health Report

Configuration management is only one side of the coin and leveraging automation tools to retrieve, manipulate, and report on operational data provides a lot of potentially useful information, especially when things go wrong.


# 5 Introducing Cisco NX-OS Programmability

##  5.1 Introduction

For many years, the state of network device management was stagnant. The process of configuring a network device meant that you needed to connect directly to the device using its SSH console or web interface and apply changes one device at a time. This approach was very tedious, inefficient, and error-prone.

Fortunately, recent innovations have introduced much more robust options for interacting with network devices. Many vendors have added rich APIs to their network operating systems, which enable the exchange of structured data among automated systems using protocols like Network Configuration Protocol (NETCONF) and RESTCONF. Also, SDKs tailored specifically to network automation needs have greatly reduced the level of development effort that is needed to manage these APIs.

Here, you will learn about the Cisco Nexus Series Switches and explore some of their available features, which enable programmability.

##  5.2 Cisco Nexus Platform

The Cisco Nexus Series was first introduced in 2008. The current roster includes several models, each developed for a particular set of use cases. Some models are fixed one- or two-unit devices, while others are large modular chassis. Despite the physical differences among Cisco Nexus lines, they all share the same Cisco Nexus Operating System (NX-OS).

### Introduction to Cisco Nexus Platforms

The Cisco Nexus Series Switches accommodate various use cases:

**Cisco Nexus 3000 Series:** These switches are low-latency, highly programmable, and high-density. These compact switches are excellent for general-purpose deployments, high-performance computing, high-frequency trading, massively scalable data centers, and cloud networks. They provide Layer 2 switching and Layer 3 routing capabilities.

**Cisco Nexus 5000 Series:** These switches are designed to deliver high-density, top-of-rack Layer 2 and Layer 3, with 10 and 40 Gigabit Ethernet interfaces in compact one-, two-, and four-rack-unit form factors. 

**Cisco Nexus 7000 Series:** These modular switches deliver a comprehensive Cisco NX-OS feature set and open-source programmable tools for software-defined networking (SDN) deployments. They offer high-density 10, 40, and 100 Gigabit Ethernet with application awareness and performance analytics.

**Cisco Nexus 9000 Series:** These switches deliver high performance and density up to 400 Gigabit Ethernet, and also low latency and exceptional power efficiency in a range of form factors. The switches are highly programmable and offer industry-leading SDN for data center automation.

##  5.3 Cisco NX-OS Programmability

The programmatic capabilities of Cisco NX-OS can be used by supporting IT departments to create automation scripts and applications to configure or manage the device locally or via centralized management servers.

### Model-Driven Programmability

Traditionally, a network device was built on hardware with tightly coupled software that was intended to be managed and administered through the CLI. These systems worked well in a world of static network configurations, static workloads, and predictable, slower change rates for application scaling. However, as data center networks have become virtualized and more focused on cloud and agile IT models, the constraints that this approach imposes make it difficult to scale efficiently.

![](/images/dcaui/dcaui_5_3_1.png)

Model-driven programmability was introduced to address these challenges. In model-driven architectures, software maintains a complete, explicit representation of the administrative and operational state of the system (the model) and performs actions only as side-effects of mutations of the model entities. Maintaining the state as a series of data models that are abstracted from the underlying hardware of the device allows for much more flexible manipulation of data and greatly reduces the time that is needed to develop automation tools.

### NX-API CLI

Cisco NX-OS provides two forms of APIs to enable the remote programming of Cisco Nexus devices: NX-API CLI and NX-API REST.

The NX-API CLI is a web interface through which commands traditionally entered via the CLI, can be encoded using XML or JSON, and transmitted via HTTP or a secure transport (HTTPS) to the device. The commands are executed on-box and responses are returned in XML or JSON format. The NX-API CLI can be characterized as an "encapsulated CLI" supporting Cisco NX-OS commands and those commands available in bash. In contrast, the NX-API REST interface provides a means through which device configurations can be manipulated via RESTful API calls. The NX-API CLI and NX-API REST are serviced by a Nginx web server that runs on the Cisco Nexus device.

![](/images/dcaui/dcaui_5_3_2.png)

The NX-API CLI uses HTTPS to secure and encrypt data. The NX-API CLI provides a session-based cookie that is named nxapi_auth when users first authenticate. The session cookie is used to avoid reauthentication during communication. If the session cookie is not included with subsequent requests, a new session cookie is required, and is achieved through a full authentication process. Avoiding unnecessary use of the authentication process helps to reduce the workload on the device. 

NX-API CLI is a great starting point for network engineers to familiarize themselves with network programmability via the NX-API, because it employs familiar CLI commands. NX-API REST provides the next level of programmability with a RESTful interface to a data model, enabling model-driven programmability. 

Source: https://developer.cisco.com/docs/nx-os/#!cisco-nx-api-cli-summary

### Cisco NX-API Sandbox

![](/images/dcaui/dcaui_5_3_3.png)

Cisco NX-OS includes a sandbox environment where developers can experiment with various requests and formats. Entering a CLI command in the box at the top will generate a request and response pairing in the chosen format. This approach provides a very convenient mechanism for crafting and testing API requests.

### On-Box Python Interpreter

One very powerful feature that the Cisco Nexus Series offers is the ability to execute arbitrary Python code directly on-box. Cisco NX-OS includes a Python 2.7 interpreter and also the pip Python package management utility.

![](/images/dcaui/dcaui_5_3_4.png)

The interpreter can be called directly from the Cisco NX-OS command line, and can interact with the underlying device using the "cisco" and "cli" libraries:

```bash
  switch# python
  Python 2.7.5 (default, Oct  8 2013, 23:59:43)
  [GCC 4.6.3] on linux2
  Type "help", "copyright", "credits" or "license" for more information.                   
  >>> from cli import *
  >>> import json
  >>> cli('configure terminal ; interface loopback 5 ; no shut')
  ''
  >>> intflist=json.loads(clid('show interface brief'))
  >>> i=0
  >>> while i < len(intflist['TABLE_interface']['ROW_interface']):
  ...   intf=intflist['TABLE_interface']['ROW_interface'][i]
  ...   i=i+1
  ...   if intf['state'] == 'up':
  ...     print intf['interface']
  ...
  mgmt0
  Ethernet2/7
  Ethernet4/7
  loopback0
  loopback5
  >>>
```

This feature enables the developer to create scripts that perform tasks including the following:

* Run a script to verify configuration on switch bootup.
* Back up a configuration.
* Perform proactive congestion management by monitoring and responding to buffer utilization characteristics.
* Integrate with the POAP for Embedded Event Manager (EEM) modules.
* Perform a job at a certain time interval (such as Port Auto Description).
* Programmatically access the switch CLI to perform various tasks.

Source: https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus7000/sw/programmability/guide/cisco_nexus7000_programmability_guide_8x/b-cisco-nexus7000-programmability-guide-8x_chapter_0100.html#concept_17DAEF691C7B4A069BCDC43B241962A0>

### NETCONF and RESTCONF Capabilities

Cisco NX-OS supports the Yet Another Next Generation (YANG) data modeling language, which is an open-source standard that is defined in RFC 6020. YANG is used to describe configuration and operational data, remote procedure calls (RPC)s, and notifications for network devices. Two protocols are available for manipulating these data models directly: NETCONF and RESTCONF.

![](/images/dcaui/dcaui_5_3_5.png)

NETCONF uses a client/server model with RPC functionality. First, the NETCONF client establishes an SSH Protocol connection with the NETCONF server. The NETCONF server sends a hello message encoded with XML and declares the NETCONF capabilities that it is capable of (for example, some NETCONF base capabilities), the YANG data models the device knows, and some other proprietary models, and so on. The NETCONF client replies with its capabilities. Once the capabilities match, the client can send a series of RPC requests encoded in XML format to the server. The server likewise responds with an RPC reply encoded in XML. The contents of the request and the response are fully described in XML schemas, allowing the two parties to recognize the syntax constraints imposed on the exchange.

The Cisco NX-OS RESTCONF agent is a REST-like interface that runs in addition to HTTP and HTTPS to access YANG data, using the data store defined in NETCONF. RESTCONF is similar in operation to NETCONF, but it uses HTTP verbs like GET and POST in accordance with REST principles. RESTCONF supports XML and JSON encoding formats. It is important to note that RESTCONF does not support the candidate data store; it uses the running configuration data store only. Hence, RESTCONF does not support locking, candidate configuration, and commit features. Multiphase transactions are not supported with RESTCONF.

Source: https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/93x/progammability/guide/b-cisco-nexus-9000-series-nx-os-programmability-guide-93x/b-cisco-nexus-9000-series-nx-os-programmability-guide-93x_chapter_0100110.html

### Configuration Management Systems

Several configuration management systems are available to simplify the management of large fleets of infrastructure devices and to enforce standard configurations across all devices. Four of the most popular configuration management systems are Ansible, Puppet, Chef, and Salt.

|            | Ansible   | Chef        | Puppet      | Salt        |
|------------|-----------|-------------|-------------|-------------|
| Type       | Agentless | Agent-based | Agent-based | Agent-based |
| Language   | Python    | Ruby        | Ruby        | Python      |
| Complexity | Low       | High        | Medium      | High        |
| Released   | 2012      | 2009        | 2011        | 2011        |

Puppet, Chef, and Salt are agent-based, which means they require the installation of a software client on the device that is managed. This agent contacts a control server to receive instructions and applies the necessary changes locally. Ansible, on the other hand, is agentless: Ansible manages devices remotely without the need for a local agent.

Because configuration management agents cannot be installed directly on Cisco NX-OS, Cisco NX-OS provides an Open Agent Container (OAC). This container is a Linux Container (LXC) that provides the environment and resources necessary to support agent software.

**Note:** The OAC feature was deprecated in Cisco NX-OS Release 8.4(1).

Puppet, however, can also be agentless when used against network devices

Source: https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus7000/sw/programmability/guide/cisco_nexus7000_programmability_guide_8x/b-cisco-nexus7000-programmability-guide-8x_chapter_0110.html

### EEM and Tcl with Cisco NX-OS

EEM is a powerful feature that allows a Cisco NX-OS device to perform some actions autonomously in response to certain events. For example, you might configure EEM to restart a service when a particular syslog message is generated. Or, maybe it would be helpful to run and record a series of show commands when a failure is detected.

* EEM performs certain actions based on triggering events.
* Can also be set to execute on a schedule.

![](/images/dcaui/dcaui_5_3_6.png)

EEM comprises two main components: events and actions. An event can take many forms, such as a log message, an increase in interface counters, a change in chassis temperature, and so on. Events can also be scheduled to occur at certain times or intervals. Triggers are the events that are performed in response to events. These actions can include sending an email or Simple Network Management Protocol (SNMP) trap, executing CLI commands, restoring a previous version of the configuration, executing a script, and other options.

EEM triggers can execute Python or Tool Command Language (Tcl) scripts. The two languages can be used to interact with the device and to execute CLI commands, offering plenty of flexibility to carry out very detailed operations in response to an interesting event.

### Cisco NX-OS Bash and Guest Shell

In addition to the Cisco NX-OS CLI and Bash access on the underlying Linux environment, the Cisco Nexus 9000 Series devices support access to a decoupled execution space running within an LXC called the Guest Shell.

![](/images/dcaui/dcaui_5_3_7.png)


Decoupling the execution space from the native host system allows customization of the Linux environment to suit the needs of the applications without impacting the host system or applications running in other Linux containers. The Guest Shell supports the following capabilities:

* Access to the network over Linux network interfaces 
* Access to Cisco Nexus 9000 bootflash 
* Access to Cisco Nexus 9000 volatile tmpfs 
* Access to Cisco Nexus 9000 CLI 
* Access to Cisco NX-API REST 
* Ability to install and run Python scripts 
* Ability to install and run 32-bit and 64-bit Linux applications

Here is some example output of the Bash Shell:

```bash
  sbx-n9kv-ao# run bash
  bash-4.3$ ifconfig
  Eth1-1    Link encap:Ethernet  HWaddr 00:50:56:bb:ab:07  
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
            RX packets:0 errors:0 dropped:0 overruns:0 frame:0
            TX packets:14 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:100 
            RX bytes:0 (0.0 B)  TX bytes:4382 (4.2 KiB)
  Eth1-2    Link encap:Ethernet  HWaddr 00:50:56:bb:ab:08  
            UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
            RX packets:0 errors:0 dropped:0 overruns:0 frame:0
            TX packets:14 errors:0 dropped:0 overruns:0 carrier:0
            collisions:0 txqueuelen:100 
            RX bytes:0 (0.0 B)  TX bytes:4382 (4.2 KiB)
  <. . . Truncated for brevity . . .>
  bash-4.3$ pwd
  /bootflash/home/admin
  bash-4.3$ 
```

# 6 Describing Day-Zero Provisioning with Cisco NX-OS

## 6.1 Introduction

Building a new data center or introducing many new devices to the existing infrastructure can be very challenging. An engineer needs to connect to each device, set the proper configuration, and install the latest version of its operating system. Taking these steps into account, deploying a single device can take half an hour. Obviously, this approach does not scale.

Fortunately, automatic provisioning technologies—often referred to as Zero Touch Provisioning (ZTP)—can alleviate much of the need for human interaction in the deployment process. ZTP enables the automatic provisioning of new devices, from initial boot to being production-ready. The technologies must be so robust that they can listen for new devices that have joined the network, identify their vendor and model information, and provide necessary information like management IP address, relevant configuration, and operating system file location for the device to download and install with no human interaction.

## 6.2 Day-Zero Operations

Day-zero operations include the automation of device software upgrades and configuration provisioning, and deployment of new network devices in a functional state with minimal involvement of human operators.

### Day 0 Provisioning of Cisco NX-OS

Cisco Nexus Operating System (NX-OS) has foundational elements to automate and configure the life-cycle management of a network device. These elements are essential to initial bootstrapping and provisioning of the Cisco NX-OS device. Continuous life-cycle management can be achieved by using configuration management agents, open-sourced or proprietary tools, and programming. The network build and operation life cycle can be divided into four main stages.

* Day 0: Initial device and network startup
* Day 1: Incremental configuration, including provisioning of new endpoints and workloads
* Day 2: Monitoring and visibility
* Day N: Upgrading and patching  

![](/images/dcaui/dcaui_6_2_1.png)

Day 0 starts by bringing up the network device and providing it with an initial configuration, like a management IP, basic system configuration, and routing information. During this step, the devices are usually upgraded to the latest and stable software version that is approved by the organization. The following list summarizes what can be achieved in the Day 0 phase.

Sample minimal switch configuration (Day 0)

* Switch name
* Admin username and password
* Out-of-band management interface and routing
* Operating system version upgrade

Extended switch configuration (Day 0)

* In-band management
* AAA
* Enable Cisco NX-OS features
* Global switching parameters
* Common routing protocol parameters
* vPC domain
* VXLAN VXLAN Tunnel Endpoint (VTEP) parameters
* Network interfaces

Next comes Day 1 provisioning, which addresses incremental and ongoing configuration changes. At Day 2, visibility and monitoring solutions are put into place. The remainder of the device's life (referred to as Day N) focuses on recurring operational maintenance.

Source: https://developer.cisco.com/docs/nx-os/#!device-power-on-automation

Two approaches for ZTP are available on the Cisco Nexus platform: POAP and PXE.

![](/images/dcaui/dcaui_6_2_2.png)

POAP was designed to provide advanced Day 0 provisioning capabilities using an extensible framework. It includes the ability to execute Python scripts as part of its workflow, which offers an unparalleled level of flexibility. Today, POAP can download and install additional management agents and apply specific configurations, based on information such as location in a network topology.

A similar approach is achieved using PXE, which uses a process that is well known in compute environments. PXE has extended its presence into the network because infrastructure devices are increasingly managed more like servers. Cisco NX-OS uses Enhanced Preboot Execution Environment (iPXE), which is an open source network firmware that is based on gPXE/Etherboot. With PXE, you can use existing skill sets and infrastructures that were developed for compute environments to simplify initial device startup.

Source: https://developer.cisco.com/docs/nx-os/#!day-0-zero-provisioning

### POAP and iPXE Architecture Components

![](/images/dcaui/dcaui_6_2_3.png)

The POAP and PXE startup processes depend on multiple services that provide different functions. It is possible to host all these services on the same server, if desired:

DHCP: DHCP provides necessary information to the device that is being set up.

A script server for providing the initial configuration script download: TFTP- and HTTP-based script download mechanisms are supported with Cisco Open NX-OS.

A configuration and software server host a repository of software images, configuration, and additional components: Various transport protocols such as Secure Copy Protocol (SCP), FTP, SFTP, or HTTP could be supported here.

Source: https://developer.cisco.com/docs/nx-os/#!poappxe-components-and-architecture

## 6.3 Describe iPXE

iPXE is an open source preboot execution tool. It is derived from PXE boot and supports modern file transport protocols. iPXE supports downloading secured Cisco NX-OS Software via HTTP over either IPv4 or IPv6. iPXE uses DHCP to determine its initial IP address and to locate its DNS and TFTP servers.

By default, a Cisco Nexus 9000 Series Switch will boot Linux via a Cisco NX-OS image that is located in bootflash. The Cisco Nexus 9000 switch boots from the standard Linux bootloader, which is named GRUB. GRUB is responsible for system control until the Linux kernel loads. The boot process can be changed using the Cisco NX-OS **boot order** command or by manual boot interruption. To manually interrupt the boot process, press **ctrl-b** within three seconds of power-on. Boot interruption will display the menu in the following example.

Please choose a bootloader shell:

1.  GRUB shell
1.  PXE shell

Enter your choice:

![](/images/dcaui/dcaui_6_3_1.png)

The command **boot order pxe bootflash** configures GRUB to automatically boot to iPXE. (If iPXE fails, the switch will fall back to booting from bootflash.)

Source: https://learning.oreilly.com/library/view/programming-and-automating/9780134436777/ch04.html

### iPXE Process

The PXE process starts by assigning a temporary IP address to the switch via the DHCP protocol. Additional DHCP scope options are also provided to facilitate the configuration script download:

* **Option 66:** This next-server option provides the TFTP server name, which acts as a script server. 
* **Option 67:** Provides the configuration script or boot filename

![](/images/dcaui/dcaui_6_3_2.png)

Cisco NX-OS uses NETBOOT to execute the configuration script and, respectively, fulfill the task of downloading the software image and configuration replay onto the network device. The device retrieves its configuration file via a Boot Image Negotiation Layer (BINL) request. The new software image and configuration will be used on the next reboot of the network device.

Source: https://developer.cisco.com/docs/nx-os/#!pxe-process

## 6.4 Power on Auto Provisioning

POAP is another option for ZTP, and operates similarly to iPXE.

### POAP Process

The POAP process starts by assigning a temporary IP address to the switch via the DHCP protocol. Additional DHCP scope options are also provided to facilitate the configuration script download.

* **Option 66 or Option 150:** These options reference the script server. IEEE Option 66 allows a single IP address or server name. Cisco Option 150 allows provisioning of a list of IP addresses for accessing the TFTP server.

* **Option 67:** This option references the configuration script or boot filename.

![](/images/dcaui/dcaui_6_4_1.png)

The Cisco Open NX-OS switch, acting as a DHCP client, will use this information to contact the TFTP server to obtain the configuration script file. The configuration script (for example, poap.py) will be executed. The logic of the configuration script will download the software image, switch configuration, agent information, and any other additional requirements from the network. POAP provides multiple mechanisms to flexibly identify switches using their serial number, system MAC address, or their location in the network, as determined by their directly connected neighbors. The downloaded image and configuration are scheduled to be applied after a reboot.

Source: https://developer.cisco.com/docs/nx-os/#!poap-process

# 7 Implementing On-Box Programmability and Automation with Cisco NX-OS

##  7.1 Introduction

Cisco Nexus Operating System (NX-OS) Software is a data center-class operating system that is built with modularity, resiliency, and serviceability. Cisco NX-OS helps ensure continuous availability and sets the standard for mission-critical data center environments. The self-healing and highly modular design of Cisco NX-OS makes zero-impact operations a reality and enables exceptional operational flexibility.

Focused on the requirements of the data center, Cisco NX-OS provides a robust and comprehensive feature set that fulfills the switching and storage networking needs of present and future data centers. Cisco Open NX-OS is a technical strategy that unlocks the potential of the Cisco Nexus 9000 Series platform with open interfaces, integrated automation, and application extensibility.

##  7.2 On-Box Programmability on Cisco NX-OS

### Work with Cisco NX-OS Bash

The Cisco Nexus 9000 Series platform can be extended and managed via the Bourne Again Shell (Bash shell). Bash is a powerful command-line processor and is the default shell on most Linux and Mac systems. It is also available for the Windows operating system. Bash supports scripting in the form of a series of Linux commands to automate a task. In Cisco NX-OS, Bash allows the network administrator to manage the switch similar to Linux systems, including operations, troubleshooting, and scripting.

![](/images/dcaui/dcaui_7_2_1.png)

The Bash shell is found on modern Mac or Linux platforms via the “terminal.” On Cisco NX-OS, the Bash shell must be enabled before it can be accessed:

```bash
  switch(config)# feature bash-shell
```

The shell is executed from enable mode:

```bash
  switch# run bash
  bash-4.2$
```

###  Bash Scripting

Bash scripting is a simple yet powerful tool to automate Linux environments. A Bash script is interpreted line-by-line by the Bash shell. Bash files must have executed permissions and should have a .sh extension. Bash scripts run a list of standard commands, but often require chaining multiple commands to parse and manipulate output.

### Bash Variables

Similar to Python, Bash supports variables, conditions, and flow control. To create a Bash variable, assign a value to a variable name. (Note that spaces in are not allowed in variable names.) The following output declares the variable “v” and assigns it the value “hello world.”

```bash
  v="hello world"
  echo $v
  hello world
```

The output uses the Linux command ***echo*** to print the value of v, which is referenced using the dollar sign symbol ($) symbol. By default, Bash has several built-in environment variables. These variables are automatically set when the shell starts and are very useful in writing Bash scripts. Except $PATH, built-in variables are not commonly changed. The following are some common built-in Bash variables:

* **$PATH:** This variable is the working path.
* **$PWD:** This variable is the current directory.
* **$HOME:** This variable is the user’s home directory.
* **$RANDOM:** This variable generates a random number.

Bash supports passing parameters into a script. Passed parameters are listed after the script name and are represented in the script as positional variables. The value of positional variables is referenced with **$parameter number**; for example, **$1** refers to the first passed variable. The following example shows Bash command-line arguments are variables:

```bash
  bash-4.2$ touch simplebash.sh
  bash-4.2$ chmod +x simplebash.sh
  bash-4.2$ echo '#!/bin/bash' > simplebash.sh
  bash-4.2$ echo echo The first input is \$1 and the second is \$2 >> simplebash.sh
  bash-4.2$ ./simplebash.sh Hello Cisco
```

The first input is Hello and the second is Cisco.

The previous example first creates a file that is called simplebash.sh, and then uses chmod to set execute permissions. Lines 3 and 4 use **“>”** and **“>>”** echo commands as text into the file, where **“>”** replaces everything in the file with the text and **“>>”** appends the file. The backslashes in line 4 escape the $ character to insert a $1 into the file instead of the value of the variable 1. Finally, the command is run using **“./”**. Dot-slash specifies that the script is to be run from the current directory.

Cisco Nexus devices feature the **vsh** command to run the Cisco NX-OS CLI commands from the Bash shell. Configuration changes are only allowed through the **vsh** command. By default, Bash shell access is available to users in the net-admin or DevOps roles. Text-based output can be searched or manipulated with standard Linux tools. **vsh** has no state, meaning the environment context does not persist from one command to the next. The following output will fail, for example:

```bash
  vsh –c configure terminal
  vsh –c interface e1/1
  vsh –c ip address 1.1.1.1 255.255.255.0
```

This output fails because all three commands are executed from EXEC mode, despite the first command entering global configuration mode. The following output demonstrates the correct usage of the **vsh** command. Individual commands are chained together using semicolons to retain context during execution:

```bash
  bash-4.2$ vsh -c "configure t; interface ethernet1/1; no switchport; ip address 1.1.1.1 255.255.255.0"
```

The **vsh** command can also be used to gather output from Cisco NX-OS **show** commands:

```bash
  bash-4.2$ vsh -c "show ip interface brief"
  IP Interface Status for VRF "default"(1)
  Interface            IP Address      Interface Status
  Lo0                  10.1.1.1        protocol-up/link-up/admin-up
  Eth1/25              192.168.1.1     protocol-up/link-up/admin-up
  Eth1/26              192.168.1.9     protocol-up/link-up/admin-up
```

The output of the **vsh** command can be parsed with standard Linux tools, such as grep, sed, and awk:

```bash
  bash-4.2$ vsh -c 'show interface'| grep "Ethernet1..* is up"
  Ethernet1/1 is up
  Ethernet1/26 is up
```

### Tcpdump

The **tcpdump** command is a popular Linux tool to capture and display IP packet data. By default, **tcpdump** will capture the first 96 bytes of a packet, but this limit is configurable. **Tcpdump** requires root access to capture traffic. The **-i** parameter specifies the interface on which to capture traffic:

```bash
  bash-4.2$ sudo Tcpdump -i Eth1-26
  Tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
  listening on Eth1-26, link-type EN10MB (Ethernet), capture size 65535 bytes
  18:16:22.904037 STP 802.1w, Rapid STP, Flags [Learn, Forward], bridge-id
  8001.f8:c2:88:90:cf:17.8035, length 43
  18:16:24.807971 IP 192.168.1.9 > 224.0.0.13: PIMv2, Hello, length 30
```

The **tcpdump** console output is convenient for quick troubleshooting; however, you usually need to save the output for off-box analysis. **tcpdump** output files are readable by Wireshark and other network analysis tools. The **–w** flag indicates the name of the file to be saved:

```bash
  bash-4.2$ sudo Tcpdump -i Eth1-26 -w eth126.cap
```

Press **Ctrl-C** to stop the tcpdump tool and return to the prompt. The file will be saved to the home directory of the user, in this case, admin. Use SCP or SFTP to transfer the file from the Cisco Nexus device to a server or your local workstation for analysis.

### Ethtool

**Ethtool** is another well-known network analysis tool that is available on Cisco NX-OS. It can be used to display statistics about the switch's Ethernet interfaces. The following example captures statistics from interface Ethernet1-26 and uses grep to display counters with nonzero values:

```bash
  bash-4.2$ ethtool -S Eth1-26 | grep .*[^0]$
  NIC statistics:
      admin_status: 1
      oper_status: 1
      reset_counter: 636
      rx_bit_rate1: 336
      tx_bit_rate1: 128
      rx_bit_rate2: 248
      tx_bit_rate2: 128
      rx_multicast_pkts: 186505
      rx_broadcast_pkts: 472
      rx_input_pkts: 186977
      rx_bytes_input_pkts: 14172461
      tx_multicast_pkts: 74928
      tx_broadcast_pkts: 1271
      tx_output_pkts: 76199
      tx_bytes_output_pkts: 7261951
```

### Linux Containers

Cisco NX-OS supports Linux containers (LXC) to provide a decoupled and protected space to execute commands, customize the Linux environment, and install third-party applications. Containers virtualize a Linux application environment and thus protect resources from third-party processes. Container virtualization is more efficient than standard hypervisors (VMware vSphere ESXi (ESXi), HyperV, kernel virtual machines (KVM)) because the host and guest share the kernel and do not duplicate shared functions. Linux namespaces are employed to uniquely identify and isolate containers from each other and the host. The following is a list of Linux namespaces and their descriptions:

* **User:** User IDs
* **PID:** Process ID
* **Network:** Network ID (address)
* **UTS:** Hostname ID
* **Mount:** File System ID
* **IPC:** Interprocess communication ID

* Independent application running on a Cisco switch
* Enables third-party applications to run on Cisco switches
* Enables users to run any application
* Run applications without compromising security
* Custom features

![](/images/dcaui/dcaui_7_2_2.png)

A container in Cisco NX-OS is considered a virtual service. The **virtual-service** commands are used to install, upgrade, and destroy containers:

```bash
  switch# virtual-service ?
    connect    Request a virtual service shell
    install    Add a virtual service to install database
    move       Move a virtual service log or core files
    reset      Virtualization reset commands
    uninstall  Remove a virtual service from the install database
    upgrade    Upgrade a virtual service package to a different version
```

The **list** command displays all virtual services that are running on the switch:

```bash
  switch# show virtual-service list

  Virtual Service List:

  Name                    Status             Package Name
  -----------------------------------------------------------------------
  guestshell+             Activated          guestshell.ova
```

The **virtual-service** command is used to connect to the console of the container:

```bash
  switch# virtual-service connect name guestshell+ console
```

### Guest SHell

Cisco NX-OS ships with a Linux container that is called the guest shell. Guest shell instances are recommended when using third-party agents; for example, Puppet and Cisco Nexus Data Broker. The Cisco NX-OS guest shell is a CentOS 7 Linux image that is preloaded with popular tools including the following:

* yum
* Python
* pip
* net-tools
* tcpdump
* ifconfig
* ethtool

The guest shell is an open Linux environment that is decoupled from Cisco NX-OS. 64-bit CentOS 7.

![](/images/dcaui/dcaui_7_2_3.png)

By default, an LXC is limited to 1 percent of available CPU, 200 MB of memory, and 200 MB of storage, but these limits can be increased to 3.8 GB of RAM and 2 GB of storage. The CPU is limited to 1 percent only during times of contention; if the CPU is not taxed, the container CPU usage is not limited.

A guest shell has access to all network interfaces, to bootflash and volatile storage, and to the CLI. Guest shells can run prepackaged OVAs (for example, Cisco Nexus Data Broker or Puppet agent), Python interpreters, or generic Linux environments. The Cisco NX-OS guest shell supports 32- and 64-bit applications.

Cisco NX-OS automatically creates guest shells with the command **run guestshell**:

```bash
  leaf1# run guestshell
  [guestshell@guestshell ~]$
```

Optionally, an application to be launched can be passed as a parameter:

```bash
  leaf1# run guestshell python /bootflash/helloWorld.py
```

Guest shells support running Cisco NX-OS commands with the dohost command. The CLI syntax must be contained in double quotation marks so that the full command is passed as a single argument:

```bash
  [guestshell@guestshell ~]$ dohost "show run"
```

Guest shell commands can be piped to Linux tools like grep to filter or manipulate command output:

```bash
  [guestshell@guestshell ~]$ dohost "show run" | grep interface
```

### Network Access in a Guest Shell

The guest shell has access to all front panel ports and all VRF instances in Cisco NX-OS; however, network settings may not be modified:

```bash
  [guestshell@guestshell ~]$ ifconfig | grep -A 7 Vlan100
  Vlan100: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 192.168.100.5  netmask 255.255.255.0  broadcast 192.168.100.255
          ether f8:c2:88:90:b6:4f  txqueuelen 100  (Ethernet)
          RX packets 0  bytes 0 (0.0 B)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 2  bytes 84 (84.0 B)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

By default, a guest shell is in the default VRF instance, but can be changed using the command **chvrf**:

```bash
  [guestshell@guestshell ~]$ chvrf management
  [guestshell@guestshell ~]$ ifconfig
  eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 10.1.1.183  netmask 255.255.255.128  broadcast 10.1.1.255
          ether f8:c2:88:90:b6:48  txqueuelen 1000  (Ethernet)
          RX packets 14043817  bytes 2458426247 (2.2 GiB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 499018  bytes 64185936 (61.2 MiB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

**chvrf** can accept a single command to be executed once within the specified VRF instance, similar to Cisco NX-OS:

```bash
  [guestshell@guestshell ~]$ chvrf default ping 192.168.100.5
  PING 192.168.100.5 (192.168.100.5) 56(84) bytes of data.
  64 bytes from 192.168.100.5: icmp_seq=1 ttl=59 time=0.031 ms
```

DNS settings in Cisco NX-OS are not used and must be manually configured in the guest shell. As with most Linux systems, DNS is configured in /etc/resolv.conf:

```bash
  [guestshell@guestshell ~]$ cat /etc/resolv.conf
  nameserver 192.168.0.50
  domain cisco.com
```

### Installing Applications in a Guest Shell

The Cisco NX-OS guest shell supports running third-party applications. Applications can be installed manually or using the Yellowdog Updater, Modified (YUM) package manager (preferred). The default repository is the Community Enterprise Operating System (CentOS) base that is configured in /etc/yum.repos.d/CentOS-Base.repo.

By default, Linux containers in Cisco NX-OS are limited to running Cisco digital signed packages, but this policy can be modified to run unsigned OVA packages. You can change the package signing security by using the following:

```bash
  leaf1(config-virt-serv-global)# signing level ?
    cisco     Allow only Cisco signed packages
    none      Most restrictive, don't allow package installation
    unsigned  Least restrictive, allow unsigned and all signing methods

  leaf1(config-virt-serv-global)# signing level unsigned
```

By default, the main partition in the guest shell is very small and will most likely need to be resized. To increase disk space on the guest shell to 2 GB, use the following:

```bash
  switch# guestshell resize rootfs 2000
  Guest shells support 32- or 64-bit applications; however, by default, 32-bit support is not enabled. To support 32-bit applications, glibc.i686 must be installed via yum:
  sudo chvrf management yum -y install glibc.i686
  <-- snipped -->
  Installed:
    glibc.i686 0:2.17-106.el7_2.1

  Dependency Installed:
    nss-softokn-freebl.i686 0:3.16.2.3-13.el7_1

  Dependency Updated:
    glibc.x86_64 0:2.17-106.el7_2.1
  glibc-common.x86_64 0:2.17-106.el7_2.1
```

### Python API

The Cisco Nexus 9000 Series platform includes a built-in Python interpreter to execute both interactive and noninteractive scripts. On-box Python is a powerful network automation tool that can be used to automate configurations, EEM actions, or other functions. The following output shows how to access the Cisco Nexus interactive interpreter.

```bash
  switch# python
  Python 2.7.5 (default, Oct  8 2013, 23:59:43)
  [GCC 4.6.3] on linux2
  Type "help", "copyright", "credits" or "license" for more information.
  >>>
```

![](/images/dcaui/dcaui_7_2_4.png)

Cisco NX-OS ships with standard Python libraries and some libraries that are specific to Cisco. Cisco libraries enable interaction with Cisco NX-OS and include “cli” for cli-like interaction and “cisco” for direct object interaction. The following example shows how to import the cli library:

```bash
  >>> from cli import *
  >>> dir(cli)
  ['__builtins__', '__doc__', '__file__', '__name__', '__package__', 'cli',
  'cli_syntax_error', 'clid', 'clip', 'cmd_exec_error', 'data_type_error', 'json',
  'shlex', 'structured_output_not_supported_error', 'subprocess', 'unexpected_
  error', 'xmltodict']
  >>>
```

The following is a brief overview of the cisco library:

```bash
  >>> import cisco
  >>> help(cisco)
  NAME
      cisco
  FILE
      /isan/python/scripts/cisco/__init__.py
  PACKAGE CONTENTS
      acl
      bgp
      buffer_depth_monitor
      check_port_discards
      cisco_secret
      feature
      history
      interface
      ipaddress
      key
      line_parser
      mac_address_table
      nxcli
      ospf
      routemap
      routes
      section_parser
      ssh
      system
      tacacs
      transfer
      vlan
      vrf
```

### Use the Cisco NX-OS CLI Python Library

To execute a CLI command through the Python interpreter, enter the CLI command as an argument string of one of the following APIs:

* **cli():** This API returns the raw output of CLI commands, including control and special characters. Example:

```bash
  string=cli("show version")
```

* **clid():** For CLI commands that support XML, this API returns JSON output. Example:

```bash
  json_string=clid("show version")
```

* **clip():** This API prints the output to the CLI command directly to stdout and returns nothing. Example:

```bash
  clip("show version")
```

When two or more CLI commands are run individually, the state is not persistent from one command to subsequent commands. In the following example, the second command fails because the state from the first command does not persist for the second command:

```bash
  >>> cli("conf t")
  >>> cli("interface eth4/1")
```

When multiple commands are chained together, the state is persistent throughout the execution. In the following example, all commands succeed because the state persists for the second and third commands:

```bash
  >>> cli("conf t ; interface eth4/1 ; shut")
```

### Use the Cisco NX-OS Cisco Python Library

Cisco NX-OS Release 7.0 and later for the Cisco Nexus 9000 Series platform provide a Cisco Python package that enables access to many core network device modules, such as interfaces, VLANs, VRF instances, access control lists (ACL)s, and routes. You can display the details of the Cisco Python package using the **help()** Python function. To obtain additional information about the classes and methods in a module, you can run the **help** command for a specific module. For example, **help(cisco.vrf)** displays the properties of the cisco.vrf module, as shown in the following example:

```bash
  >>> help(cisco.vrf)
      class VRF(__builtin__.object)
      |  Use this object to create/delete a VRF on the switch, add/remove
  interfaces
      |  to a VRF or simply just to check if a VRF exists.
      |
      |  Methods defined here:
      |
      |  __init__(self, vrf)
      |      Initializes a VRF object with the specified VRF name or ID and returns
      |      the new object.
      |
      |      Arguments:
      |          vrf: VRF name (string) or the VRF ID (int). If the VRF is
  specified
      |              as an integer only VRF values corresponding to an existing VRF
      |              will be accepted because it is not possible to create a VRF
      |              using an ID.
      |
      |      Example:
      |          a. v = VRF('management')
      |          b. v = VRF(2)
      |
      |      Returns: VRF object on success
      |
      |  add_interface(self, if_name, **args)
      |      Sets the specified interface's VRF membership to this VRF.
      |
      |      Arguments:
      |          if_name: A string specifying the interface name
      |
      |      Optional Arguments:
      |              interface's VRF membership.
      |
      |      Returns: True on success
      |
      |      Example:
      |          v = VRF('floor1')
      |          v.create()
      |          v.add_interface('Ethernet 1/1')
      |
      |  create(self, **args)
      |      Creates the VRF
      |
      |      Arguments: None
      |
      |      Optional Arguments:
      |          no: A boolean, set to true to delete the VRF
      |
      |      Example:
      |          v = VRF('floor1')
      |          v.create()
      |
      |      Returns: True on success
  :
```

The following example uses the Cisco API to set the interface IP address.

```bash
  >>> x = cisco.Interface("Ethernet1/2")
  >>> x.set_description("hello")
  True
  >>> x.set_ipaddress("2.1.1.1 255.255.255.0")
  True
  >>>
```

### Noninteractive Python

Python scripts can be saved as files and executed in Cisco NX-OS. Scripts must be saved in the /bootflash directory. The following output creates some simple Python and demonstrates how to run a Python script in Cisco NX-OS:

```bash
  switch# run bash
  cd /bootflash
  touch some_python.py          #create a file
  echo 'print "Hello World" > some_python.py'    #Python code to print hello world
  exit         #exit bash
  switch# python some_python.py
  Hello World
  switch#
```

The EEM can execute Python scripts or the Cisco NX-OS scheduler can schedule a Python script to run at a certain time or interval. Scheduling must be enabled manually:

```bash
  switch# (config) feature schedule
```

Scheduler configuration:

```bash
  scheduler job name schedule_some_python
  python bootflash:/some_python.py
  end-job
```

Scheduler schedule name: schedule_some_python

```bash
  scheduler schedule name my_schedule
    job name schedule_some_python
    time start 2015:11:27:19:09 repeat 0:0:10
```

### Cisco or CLI Package?

The CLI and Cisco modules are very similar in their ability to affect the configuration of Cisco NX-OS; however, the Cisco module is usually easier to use. The CLI module is very useful when the Cisco module does not cover a particular object or the configuration already exists.

### On-Box Python: Use Cases and Examples

The Cisco command **reload in** is a popular failsafe when working with configuration. This command will automatically reload a Cisco device after a configurable amount of time. It is very useful when working with remote boxes, because if a configuration change is made that inadvertently disables access to the device, the device will reload to the startup configuration and restore access. Cisco NX-OS on the Cisco Nexus 9000 Series does not support the **reload in** command, but a simple Python script can replicate its functionality.

1.  Input the number of minutes, or optionally if the configuration should be saved or canceled by user.
1.  Cancel the script if the input is not correct.
1.  Determine the current time.
1.  Add users input in minutes to current time
1.  Enable the scheduler feature.
1.  Option—Save Configuration
1.  Use the CLI to schedule a reload.
1.  Option—Cancel reload

```bash
  # Written by Ryan Tischer @ Cisco - @ryantischers
  # Designed to run on Nexus 9000 switch
  # script replicates IOS "reload in" command to schedule reloads
  # leverages scheduler feature in NXOS

  #Usage Documentation -

  #copy/save script in bootflash/scripts folder on switch
  #from NXOS CLI type "source reloadin.py [options]
  #options...
  #       - number of minutes until reload
  #       - "save" to save configuration before reload.  Must be used as second
  option behind minutes
  #       - "cancel" cancel reload
```

### Usage Examples

```python
  #Examples
  # source reloadin.py 20 - schedules a reload in 20 minutes
  # source reloadin.py 20 save - schedules a reload in 20 minutes and saves the config
  # source reloadin.py cancel - cancels current reloadin command.

  #imports – datatime for current time, cli for access to the cli and sys for input
  # variables

  import datetime
  from cli import cli
  import sys
  #get the number of inputed arguments

  numArg = len(sys.argv)

  #check input for cancelation

  if sys.argv[1] == "cancel":

          schedCLIjob = 'conf t ; no scheduler job name reloadinCommand5657373'
          schedCLItime = 'conf t ; no scheduler schedule name
          reloadinCommand5657373'
          print "Canceling reload"

  #check number of args and if first one is a int
  elif numArg <= 3:
          try:    #python error handling

                  requestTime = int(sys.argv[1])
          except:
                  print "Enter a integer for time"
                  sys.exit() #bail out if input is wrong  #exit if input is
                  incorrect

          now = datetime.datetime.now()  #get the current time
          actionTime = now + datetime.timedelta(minutes = requestTime)  #set
          the reload time
          reloadTime = str(actionTime) # reload time in a string
          reloadTime=reloadTime[11:-10]

  #Build CLI command with unique schedule name            
          schedCLIjob = 'conf t ; scheduler job name reloadinCommand5657373 ;
          reload ; exit'
          schedCLItime = 'conf t ; scheduler schedule name
          reloadinCommand5657373 ; time start ' + reloadTime + ' repeat 48:00 ;
          end '

  #save configuration
          if numArg == 3 and sys.argv[2] == "save".lower():
                  cli('copy running-config startup-config')
                  print "Saving config before reload"

  #print script output
          print "current time on the switch is " + str(now)
          print "reload scheduled at " + reloadTime

  #run the CLI
  cli('conf t ; feature scheduler')

  #schedule the job
  try:
          cli(schedCLIjob)
  except:
          print "operation failed..did you cancel a job that was not there?"
          sys.exit()

  cli(schedCLItime)
  print "Operation success"
```

### EEM Neighbor Discovery

This example uses a Python script from the EEM syslog event. The EEM configuration is straightforward:

```bash
  event manager applet CDP_Helper
    event syslog pattern "IF_UP"
    action 1 cli source cdp_neighbor.py
```

The EEM syslog event will wait for a syslog message with the pattern IF_UP and when observed, executes the Python script cdp_neighbor.py. This script uses the cli() and clid() functions to get Cisco Discovery Protocol data from the switch and apply it as an interface description.

```python
  #import libraries
  from cli import *
  import json

  #get cdp data as json
  cdp_data = clid('show cdp neigh')

  #format the json
  cdp_data_json=json.loads(cdp_data)

  #The cdp output was nice enough to give how many neighbors are connected
  num_neighbors=cdp_data_json["neigh_count"]
  #num neighbors is used for the loop, however we need to account for 0
  num_neighbors = int(num_neighbors)-1

  counter=0 #init a counter variable

  #compare counter to num neighbors
  while counter < num_neighbors:

    #d[number] gets relevant data from cdp_data_json    
    d1=cdp_data_json["TABLE_cdp_neighbor_brief_info"]["ROW_cdp_neighbor_brief_
    info"][counter]["platform_id"]
    d2=cdp_data_json["TABLE_cdp_neighbor_brief_info"]["ROW_cdp_neighbor_brief_
    info"][counter]["device_id"]
    d3=cdp_data_json["TABLE_cdp_neighbor_brief_info"]["ROW_cdp_neighbor_brief_
    info"][counter]["intf_id"]

  #create the NX-OS cli command
  cliCommand = "conf t ;  interface " + d3 + " ; description Connected device is a "
  + d1 + " named " + d2

  #run the cli command
              cli(cliCommand )

      #add to the counter
              counter = counter + 1
```

The following shows the cdp_neighbor.py output:

```bash
  interface Ethernet1/2
    description Connected device is a cisco WS-C2960S-48TS-L named oob_Switch
```

The following shows the raw data from json.dumps:

```bash
  print (json.dumps(cdp_data_json, indent=4))
  {
      "neigh_count": "3",
      "TABLE_cdp_neighbor_brief_info": {
          "ROW_cdp_neighbor_brief_info": [
              {
                  "platform_id": "cisco WS-C2960S-48TS-L",
                  "intf_id": "mgmt0",
                  "capability": [
                      "switch",
                      "IGMP_cnd_filtering"
                  ],

                  "ttl": "166",
                  "ifindex": "83886080",
                  "port_id": "GigabitEthernet1/0/27",
                  "device_id": "Switch"
              },
              {
                  "platform_id": "N9K-C9504",
                  "intf_id": "Ethernet1/49",
                  "capability": [
                      "router",
                      "switch",
                      "Supports-STP-Dispute"
                  ],
                  "ttl": "158",
                  "ifindex": "436232192",
                  "port_id": "Ethernet1/1",
                  "device_id": "Spine2(FOX1830GW5Y)"
              },
              {
                  "platform_id": "N9K-C9332PQ",
                  "intf_id": "Ethernet1/53",
                  "capability": [
                      "router",
                      "switch",
                      "Supports-STP-Dispute"
                  ],
                  "ttl": "158",
                  "ifindex": "436234240",
                  "port_id": "Ethernet1/26",
                  "device_id": "spine1(SAL18391KGF)"
              }
          ]
      }
  }
  Independent application running on a Cisco switch
  Enables 3rd party applications to run on Cisco switches
  Enables users to run any application
  Run applications without compromising security
  Custom features
```

### Embedded Event Manager

EEM monitors a network device for predefined system events (for example, syslog messages, hardware issues, or CLI input) and performs a user-defined action when the event occurs. EEM is a system automation tool that has been embedded in Cisco devices for some time; however, Cisco NX-can execute a Python script as an action. EEM and on-box Python scripting offer powerful and easy-to-use tools that enable advanced network automation.

![](/images/dcaui/dcaui_7_2_5.png)

The following list summarizes the available EEM Cisco NX-OS system events: cli, counter, fanabsent, fanbad, fib, syslog, temperature, gold, memory, module, module-failure, neighbor-discovery, sysmgr, test, Online-Insertion-Removal, policy-default, Power over budget, snmp, storm-control, tag, and track. 

EEM supports the following actions:

* Execute a Python Script
* Execute any CLI Command
* Update a Counter
* Log an exception
* Force shutdown of a module
* Reload the device
* Generate syslog message
* Generate Call Home Event
* Generate SNMP notification
* Shutdown specified modules

### Embedded Event Manager

An EEM policy includes an event and its associated actions, and is configured from the CLI.

This example uses EEM to save a copy of the running configuration on bootflash, and writes a message to syslog. “Event-default” is required as the last action to allow the CLI command. Without event-default, the CLI command will be blocked.

```bash
  event manager applet test1
    event cli match "copy running-config startup-config"
    action 1 cli copy running-config bootflash:/current_config.txt
    action 2 syslog msg Configuration saved and copied to bootflash
    action 3 event-default
```

![](/images/dcaui/dcaui_7_2_6.png)

To tie a Python script to EEM, use the following syntax:

```bash
  action 1 cli python bootflash:somepython.py
```

### EEM Variables

EEM supports creating and using environment variables. Variables are convenient when working with dynamic data and can be set using Python or Bash scripts. EEM variables are set though the Cisco NX-OS command line:

```bash
  event manager environment some_server "192.168.1.1"
```

EEM variable set via vsh in a Bash script:

```bash
  run bash eem_date=$(date) ; vsh -c "configure t ; event manager environment
  todays_date '$eem_date' "
```

To verify an EEM policy, use the following command:

```bash
  show event manager events action-log
```

# 8 Implementing Off-Box Programmability and Automation with Cisco NX-OS

##  8.1 Introduction

Off-box programmability and automation are tools that run outside of the network devices being configured. This type of programmability allows the network to be managed holistically or as part of an automation or orchestration solution. Off-box programmability provides a pathway for the network to be used in cloud operational models.

Off-box Cisco Nexus solutions include the NX-API, which provides full configuration and monitoring of the switch. Also, Cisco Nexus Operating System (NX-OS) supports next-generation automation tools, including Puppet, Chef, and Ansible. NX-API serves as the interface that enables Cisco Nexus solutions to participate in continuous integration/continuous delivery (CI/CD) software development methodologies, including DevOps.

##  8.2 NX-API Enhancement

NX-API is an enhancement to the Cisco Nexus 9000 Series CLI system that supports XML output. It is a RESTful API that provides full access to the switch over HTTP and HTTPS. NX-API is supported on all Cisco Nexus 9000 and Cisco Nexus 3000 platforms, with limited support on traditional Cisco Nexus (limited to CLI methods). It supports both a CLI method, in which CLI commands are passed over the API, or an object model, in which data models are directly manipulated. Any configurations, including show commands, are supported in the CLI-based NX-API. However, only specific objects are supported.

### NX-API REST Object-Oriented Model

In addition to CLI commands, NX-API supports an object-oriented data model that is called NX-API REST. Object data is native to the switch and therefore does not require a CLI parser or translator. Data models tend to be easier to use, because they bypass the constraints of CLI-based programmability by requesting the state of an object.

![](/images/dcaui/dcaui_8_2_1.png)

NX-API object configuration requires less string manipulation or Cisco NX-OS sequencing. A Cisco NX-OS object is a configuration of a specific feature, for example, BGP or an interface. Not all types of objects or configurations are yet available under the object model.

### Management Information Tree

NX-API objects use an information-model-based architecture in which the model describes all the information that a management process can control. Object instances are referred to as managed objects. A unique DN can be used to identify every managed object in the system. This approach allows the object to be referred to globally. An object’s DN is the full path of the object that is specific to the device. The DN of ethernet1/27 on a Cisco Nexus 9000 Series platform, for instance, will be sys/intf/phys-[eth1/27]d.

![](/images/dcaui/dcaui_8_2_2.png)

Objects in the MIT are part of a class, which represents the parent level structure for an object. Classes are generally used for queries on higher-level data; for example, an entire switch chassis. Managed objects and classes are accessed by their DN, using standard HTTP verbs including GET, POST, PUT, and DELETE. The URL format that is used can be represented as follows:

```bash
  <switch ip >:/api/[mo|class]/[dn|class][:method].[xml|json]?{options}
```

The various building blocks of the preceding URL are as follows:

* **mo | class:** This part indicates a managed object or class-level query.

* **class:** This part is the managed-object class (as specified in the information model) of the queried objects; the class name is represented.

* **dn:** This part is the DN (unique hierarchical name of the object in the MIT tree) of the queried object.

* **method:** This part is the optional indication of the method that is being invoked on the object; it applies only to HTTP POST requests.

* **xml | json:** This part is the encoding format.

* **options:** This part represents the query options, filters, and arguments. Query options include self or children to define which data to query. For example, "?query-target=self".

An example URL to work with BGP would be http://192.168.0.1/api/mo/sys/bgp.json.

### NX-API CLI Method

NX-API offers a choice of data formatting, including XML, JSON, and JSON Remote Procedure Call (JSON-RPC). In general, JSON-RPC is easier to use because it calls a procedure (or method) on the switch and supports passing parameters for the procedure directly in the payload. The following table illustrates how JSON and JSON-RPC send **BGP configuration** command to Cisco NX-OS. As described, JSON-RPC calls the CLI method with a parameter for the command, whereas the JSON-formatted message must specify all details about the command and output.

![](/images/dcaui/dcaui_8_2_3.png)

The following example shows how to configure and launch the NX-API CLI:

```bash
  switch# conf t
      switch(config)# feature nxapi
```

The following example shows a request and its response in XML format:

Request:

```xml
  <?xml version="1.0" encoding="ISO-8859-1"?>
  <ins_api>
    <version>0.1</version>
    <type>cli_show</type>
    <chunk>0</chunk>
    <sid>session1</sid>
    <input>show switchname</input>
    <output_format>xml</output_format>
  </ins_api>
```

Response:

```xml
  <?xml version="1.0"?>
  <ins_api>
    <type>cli_show</type>
    <version>0.1</version>
    <sid>eoc</sid>
    <outputs>
      <output>
        <body>
          <hostname>switch</hostname>
        </body>
        <input>show switchname</input>
        <msg>Success</msg>
        <code>200</code>
      </output>
    </outputs>
  </ins_api>
```

The following example shows a request and its response in JSON format.

Request:

```json
  {
      "ins_api": {
          "version": "0.1",
          "type": "cli_show",
          "chunk": "0",
          "sid": "session1",
          "input": "show switchname",
          "output_format": "json"
      }
  }
```

Response:

```json
  {
      "ins_api": {
          "type": "cli_show",
          "version": "0.1",
          "sid": "eoc",
          "outputs": {
              "output": {
                  "body": {
                      "hostname": "switch"
                  },
                  "input": "show switchname",
                  "msg": "Success",
                  "code": "200"
              }
          }
      }
  } 
```

### NX-API Sandbox

NX-API includes a sandbox for building and testing NX-API calls. The sandbox is a web front end that is accessed from a switch’s base URL. The NX-API sandbox accepts any Cisco NX-OS command and converts it into formatted data to send to the switch. The formatting is specified as JSON-RPC, JSON, or XML. The POST button will send formatted data to the switch and return the formatted results.

![](/images/dcaui/dcaui_8_2_4.png)

The NX-API sandbox is very useful for understanding the exact data formatting that is used for both sending and receiving data. The NX-API sandbox also includes a feature that automatically transforms the CLI input into a simple Python program using the requests library to generate an API request. Use the **Python** button to convert the CLI command to Python. The following example demonstrates NX-API–generated Python code for the command **show interface ethernet1/1.**

```python
  import requests
  import json

  """
  Modify these please
  """
  url='http://YOURIP/ins'
  switchuser='USERID'
  switchpassword='PASSWORD'

  myheaders={'content-type':'application/json-rpc'}
  payload=[
    {
      "jsonrpc": "2.0",
      "method": "cli",
      "params": {
        "cmd": "show interface ethernet1/1",
        "version": 1
      },
      "id": 1
    }
  ]
  response=requests.post(url,data=json.dumps(payload),headers=myheaders,auth=
  (switchuser,switchpassword)).json()
```

## 8.4  Model-Driven Programmability on Cisco NX-OS

The model-driven programmability of the Cisco NX-OS device allows you to automate the configuration and control of the device. Data modeling provides a programmatic and standards-based method of applying configuration changes to the network device, replacing the process of manual configuration. Data models are written in a standard, industry-defined language. Although configuration using a CLI may be more human-friendly, automating the configuration using data models results in better scalability.

Cisco NX-OS supports the YANG data modeling language. YANG is a data modeling language that is used to describe configuration and operational data, remote procedure calls, and notifications for network devices.

### Programmable Interface Infrastructure

When a request is received (whether via NETCONF, RESTConf, or gRPC), it is converted into an abstract message object. That message object is distributed to the underlying model infrastructure based on the namespace in the request. Using the namespace, the appropriate model is selected and the request is passed to it for processing. The model infrastructure executes the request (read or write) on the device datastore. The results are returned to the agent of origin for response transmission back to the requesting client.

![](/images/dcaui/dcaui_8_4_1.png)

### Cisco NX-OS Programmable Interface Agents

Agents provide an interface between the device and the management clients. They specify the transport, protocol, and encoding of the communications with the device. Cisco NX-OS programmable interfaces support three agents: NETCONF, RESTConf, and gRPC. Each of these agents provides different interfaces for configuration management of the device via YANG models.

```bash
  Agent     Transport     Protocol                        Encoding
  NETCONF   SSH                                           XML
  RESTConf  HTTP          draft-ietf-netconf-restconf-10  XML or JSON
  gRPC      HTTP          gRPC Protocol Spec              Google Protobuf
```

### YANG Data Models

The model infrastructure takes requests that are received from the agent, determines the namespace that is associated with the YANG model in the request, and selects the model component that matches the namespace to process the request. When the selected model component completes the request processing, the processing results are sent to the requesting agent for transmission back to the client. The model infrastructure is also responsible for processing protocol initiation requests involving authentication, handshaking, and so on, as specified by the agent protocol.

![](/images/dcaui/dcaui_8_4_2.png)

### Device YANG Model

The device configuration is described in a YANG model that is called a device model. The device model is manifested in the model infrastructure as another model component with the device namespace.

### Common YANG Models

A common model is another kind of model component that contains, within its elements, YANG paths to the equivalent device model elements. These equivalent device model elements are used to read and write device model data in the device YANG context.

## 8.6  Ansible for Cisco NX-OS

Ansible is an open-source IT automation engine that automates cloud provisioning, configuration management, application deployment, intraservice orchestration, and other IT needs. It can be used very effectively to manage Cisco NX-OS devices.

### Ansible Modules for Cisco NX-OS

Ansible uses small programs that are called modules to make API calls to devices, and apply configurations that are defined in playbooks. All the Cisco NX-OS modules are included in Ansible Core as of Ansible 2.2, so no additional effort is required to begin automating your Cisco Nexus devices. By default, Ansible represents the machines it manages by using a simple INI file that puts all your managed machines in groups of your own choosing.

**Note:** Ansible version 2.10 introduces project-local collections paths, where the modules can be stored.

Because Ansible has an agentless architecture, once the SSH or NX-API feature is enabled, and a username and password are configured, the Cisco Nexus device can be managed through Ansible. (The authentication credentials that are provided in the playbook must have the requisite role privilege to allow device configuration changes.)

**Note:** Ansible can also use SSH instead of NX-API.

![](/images/dcaui/dcaui_8_6_1.png)

The host file is where the devices that Ansible manages are listed. A single device can be in a single group or included in multiple groups. In the following hosts file, there is a single group that is called “leaf,” which has two devices: n9k1 and n9k2. For additional security, this host file can be encrypted using Ansible Vault, which is not used here.

```bash
  $ cat /etc/ansible/hosts
  [all:vars]
  ansible_connection=network_cli
  ansible_user=cisco
  ansible_password=cisco
  [leaf]
  n9k1
  N9k2
```

### Connectivity

First, you should establish that there is basic connectivity and that you can log in to the devices. You will use the built-in ping module to complete this test. This module is not an ICMP ping, but rather a module to test IP connectivity and authentication.

```bash
  $ ansible leaf -m ping
  n9k1 | SUCCESS => {
      "changed": false,
      "ping": "pong"
  }
  n9k2 | SUCCESS => {
      "changed": false,
      "ping": "pong"
  }
```

### Documentation

Thorough documentation for all Cisco NX-OS modules can be found on the Ansible website or locally using the **ansible-doc** command.

### Example Playbook

In this initial playbook, you will provision several VLANs across both leaf devices. You will use the Ansible module that is called **nxos_vlan** to automate this task.

As you can see, the following playbook is defined in YAML. The hosts field denotes the group of hosts that are in scope for this playbook, and the tasks are the modules to be run.

```yaml
  ---
  - name: Vlan Provisioning
    hosts: leaf
    gather_facts: no
    tasks:
      - name: CREATE VLANS AND ASSIGN A NAME, USING VLAN_ID
        nxos_vlan: 
          vlan_id: "{{ item.vlan_id }}"
          name: "{{ item.name }} "
        loop:
          - vlan_id: 2
            name: Native
          - vlan_id: 15
            name: Web
          - vlan_id: 20
            name: App
          - vlan_id: 30
            name: DB
```

This playbook is saved as provision_vlans.yml.

### Running a Playbook

By default, Ansible will use the host file that is located in /etc/ansible/hosts, but a different hosts file can be specified by using the -i flag. In the following example, the standard hosts file is used when executing the playbook.

```bash
  $ ansible-playbook provision_vlans.yml
  PLAY [Vlan Provisioning] *******************************************************
  TASK [CREATE VLANS AND ASSIGN A NAME, USING VLAN_ID] ***************************
  changed: [n9k2] => (item={u'name': u'Native', u'vlan_id': 2})
  changed: [n9k2] => (item={u'name': u'Web', u'vlan_id': 15})
  changed: [n9k1] => (item={u'name': u'Native', u'vlan_id': 2})
  changed: [n9k2] => (item={u'name': u'App', u'vlan_id': 20})
  changed: [n9k1] => (item={u'name': u'Web', u'vlan_id': 15})
  changed: [n9k2] => (item={u'name': u'DB', u'vlan_id': 30})
  changed: [n9k1] => (item={u'name': u'App', u'vlan_id': 20})
  changed: [n9k1] => (item={u'name': u'DB', u'vlan_id': 30})
  PLAY RECAP *********************************************************************
  leaf-1                     : ok=1    changed=1    unreachable=0    failed=0
  leaf-2                     : ok=1    changed=1    unreachable=0    failed=0
```

This Ansible playbook is idempotent, which means that it will only make a change to the device if a change is necessary. If the configuration is already present, then no changes will be made. In the previous playbook, all the listed VLANs were absent and were created on both devices. You know this fact because both devices indicate changed=1 in the play recap.

### Module Types

There are two core module types that you can use when automating Cisco Nexus switches. You can use resource modules or generic common modules.

![](/images/dcaui/dcaui_8_6_2.png)

Resource modules perform an action on a given resource. Resources map back to a given feature. For example, there are dozens of resource modules for Cisco Nexus switches. They exist to manage VLANs, interfaces, routing, BGP, OSPF, HSRP, and much more. Resources eliminate the need to manage devices with commands, because the modules themselves know which commands are required to get the device into its desired state.

Generic common modules are the lowest common denominator for managing devices. There are three generic modules of which you should be aware:

* **nxos_config:** With the “core config” module, it is possible to send arbitrary configuration CLI commands to Cisco Nexus switches. You can statically define commands or generate them dynamically with Jinja templates.

* **nxos_command:** With the “core command” module, you can send arbitrary exec-level commands, that is, **show** commands, **ping**, and so on. You can then register (or save) the response data to generate reports or conditionally execute more tasks in a given Ansible playbook.

* **nxos_facts:** The facts module gathers key “facts” from Cisco Nexus switches including, but not limited to, hostname, Link Level Discovery Protocol (LLDP) neighbors, model, serial number, interfaces and interface state, configuration file, and much more. The facts module issues several **show** commands, parses text automatically to return JSON that can be viewed, and used within Ansible playbooks.

# 9 Automating Cisco UCS Using Developer Tools

##  9.1 Introduction

Cisco Unified Computing System (UCS) helps change the way IT organizations do business. It combines industry-standard, x86-architecture servers with networking and storage access in a single unified system. Cisco UCS brings increased productivity, reduced total cost of ownership, and scalability into your data center. Cisco UCS automation tools make it easy to automate compute operations in the data center. Cisco UCS is built on an XML API, which provides programmatic capabilities and allows the development of tools and SDKs. Cisco UCS PowerTool for Microsoft PowerShell, Cisco UCS Manager Python SDK, and Ansible modules.

##  9.2 Cisco UCS Overview

Cisco Unified Computing System (UCS) is a not a server, but rather a system. Cisco UCS has a unique architecture that integrates compute, data network access, and storage network access in a common set of components under a single-pane-of-glass management interface.

Cisco UCS fuses access layer networking and servers. This high-performance, next-generation server system provides a data center with a high degree of workload agility and scalability. The hardware and software components support Cisco Unified Fabric, which runs multiple types of data center traffic over a single converged network adapter.

### Architectural Simplification

The simplified architecture of Cisco UCS reduces the number of required devices and centralizes switching resources. By eliminating switching inside a chassis, network access-layer fragmentation is significantly reduced. Cisco UCS implements Cisco Unified Fabric in blades and groups of racks, and supports Ethernet and Fiber Channel protocols over 10 Gigabit Cisco Data Center Ethernet and FCoE links. This radical simplification reduces the number of switches, cables, adapters, and management points by up to two-thirds. All devices in a Cisco UCS domain are under a single management domain, which remains highly available by using redundant components.

### High Availability

The management and data plane of Cisco UCS is designed for high availability and redundant access layer fabric interconnects. In addition, Cisco UCS supports existing high-availability and disaster recovery solutions for the data center, such as data replication and application-level clustering technologies.

### Scalability

A single Cisco UCS domain supports multiple chassis and their servers along with rack-mounted individual servers, all of which are administered through one Cisco UCS Manager. Servers are easily added by placing additional blade servers within a chassis or connecting a rack-mounted server to the fabric interconnects.

### Flexibility

A Cisco UCS domain allows you to quickly align computing resources in the data center with rapidly changing business requirements. This built-in flexibility is determined by whether you choose to fully implement the stateless computing feature. Pools of servers and other system resources can be applied as necessary to respond to workload fluctuations, support new applications, scale existing software and business services, and accommodate both scheduled and unscheduled downtime. Server identity can be abstracted into a mobile service profile that can be moved from server to server with minimal downtime and no need for additional network configuration. With this level of flexibility, you can quickly and easily scale server capacity without having to change the server identity or reconfigure the server, LAN, or SAN. During a maintenance window, you can quickly do the following:

Deploy new servers to meet unexpected workload demand and rebalance resources and traffic.

Shut down an application, such as a database management system, on one server and then boot it up on another server with increased I/O capacity and memory resources.

### Optimized for Server Virtualization

Cisco UCS has been optimized to implement Cisco Virtual Machine Fabric Extender (VM-FEX) technology. This technology provides improved support for server virtualization, including better policy-based configuration and security, compliance with a company's operational model, and accommodation for VMware's vMotion.

### Cisco UCS Fabric-Centric Design

Cisco UCS has single-wire management, networking, and storage all in one. A Cisco UCS system contains fabric interconnects that contain all the networking, both Ethernet and storage, and management the chassis and rack servers are connected to the fabric interconnects. There are two fabric interconnects per Cisco UCS domain to assure high availability. If a fabric interconnect fails, traffic, storage, and management are switched to the other fabric interconnect so that the server can remain powered up and stable.

![](/images/dcaui/dcaui_9_2_1.png)

The compute power can come from blade servers that are located inside a chassis, or form factor servers. Cisco UCS is easy to scale. Adding additional blades, chassis, or rack-mount servers will not interrupt operations and can be done quickly. If there is a failure of a physical server, the service profile can be moved to another server with limited downtime.

Cisco UCS Bare Metal Abstraction
The service profile is the central concept of Cisco UCS. Each service profile ensures that the associated server hardware has the configuration that is required to support the applications it will host. The service profile maintains configuration information about the server hardware, interfaces, fabric connectivity, and server and network identity. This information is stored in a format that you can manage through Cisco UCS Manager. All service profiles are centrally managed and stored in a database on the fabric interconnect. Every powered-on server must be associated with a service profile.

At any given time, each server can be associated with only one service profile. Similarly, each service profile can be associated with only one server at a time. After you associate a service profile with a server, the server is ready to have an operating system and applications installed, and you can use the service profile to review the configuration of the server. If the server associated with a service profile fails, the service profile does not automatically fail over to another server.

![](/images/dcaui/dcaui_9_2_2.png)

When a service profile is disassociated from a server, the identity and connectivity information for the server is reset to factory defaults. All the identity information and connectivity information are stored with the service profile.

A service profile can move from server to server, which is an advantage in hardware upgrades or equipment failure. If a server fails, it is easy to disassociate the service profile on the server, replace a server, and associate the service profile with the newly replaced server. All the settings in the service profile will apply to the server.

There are two types of service profiles:

* Service profiles that override server identity
* Service profiles that inherit server identity

A service profile that overrides server identity provides the maximum amount of flexibility and control. This profile allows you to override the identity values that are on the server at the time of association and use the resource pools and policies that are set up in Cisco UCS Manager. You can disassociate this service profile from one server and then associate it with another server. This profile allows you to take advantage of and manage system resources through resource pools and policies, such as the following:

* Virtualized identity information, including pools of MAC addresses, WWN addresses, and UUIDs
* Ethernet and Fiber Channel adapter profile policies
* Firmware package policies
* Operating system boot order policies

Service Profiles That Inherit Server Identity
This hardware-based service profile is the simplest to use and create. This profile uses the default values in the server and mimics the management of a rack-mounted server. It is tied to a specific server and cannot be moved or migrated to another server. You do not need to create pools or configuration policies to use this service profile. This service profile inherits and applies the identity and configuration information that is present at the time of association, such as the following:

* MAC addresses for the NICs
* For a converged network adapter or a virtual interface card, the WWN addresses for the two HBAs
* BIOS settings
* Server UUID

### Service Profile Templates

With a service profile template, you can quickly create several service profiles with the same basic parameters, such as the number of vNICs and vHBAs, and with identity information drawn from the same pools.

![](/images/dcaui/dcaui_9_2_3.png)

Cisco UCS has two types of service profile templates:

* **Initial template:** Service profiles that are created from an initial template inherit all the properties of the template. However, after you create the profile, it is no longer connected to the template. If you need to make changes to one or more profiles that were created from this template, you must change each profile individually.

* **Updating template:** Service profiles that are created from an updating template inherit all the properties of the template and remain connected to the template. Any changes to the template automatically update the service profiles that were created from the template.

### Cisco UCS Manager

Cisco UCS Manager is embedded software that resides on the fabric interconnects and provides complete configuration and management capabilities for all the components in the Cisco UCS system. This configuration information is replicated between the two fabric interconnects and provides a highly available solution for this critical function. The most common way to access Cisco UCS Manager for simple tasks is to use the HTML5-based GUI.

![](/images/dcaui/dcaui_9_2_4.png)

For command-line or programmatic operations on the system, a CLI and an XML API are available. The Cisco UCS Manager GUI provides RBAC to allow multiple user-level rights. Cisco UCS Manager provides unified, embedded management of all software and hardware components. Every instance of Cisco UCS Manager and all the components that it manages form a domain. For organizations that deploy multiple Cisco UCS domains, Cisco UCS Director and Cisco Intersight provide a centralized user interface that allows you to manage multiple, globally distributed Cisco UCS domains with thousands of servers.

### Cisco Integrated Management Controller

![](/images/dcaui/dcaui_9_2_5.png)

The Cisco Integrated Management Controller (IMC) is a baseboard management controller that provides embedded server management for Cisco UCS C-Series Rack Servers and Cisco S-Series Storage Servers. Cisco IMC enables system management in the data center and across distributed branch-office locations. It supports multiple management interfaces, including a web user interface, a CLI, and an XML API that is consistent with the API that is used by Cisco UCS Manager. Cisco IMC provides the following information and capabilities:

* Platform hardware inventory and health status
* Server management, including vKVM launch
* Northbound API (REST and XML API) for programmatic access
* Firmware updates

##  9.3 Cisco UCS Manager XML API

Cisco UCS is built on an HTTP-based XML API. The Cisco UCS Manager XML API is a programmatic interface to Cisco UCS. The API accepts XML documents through HTTP or HTTPS. Developers can use any programming language to generate XML documents that contain the proper structure.

Configuration and state information for Cisco UCS is stored in a hierarchical tree structure that is known as the MIT, which is completely accessible through the XML API. The XML API allows third-party development for tie-ins to Cisco UCS.

![](/images/dcaui/dcaui_9_3_1.png)


### Cisco UCS Management Information Model

All the physical and logical components that comprise Cisco UCS are represented in a hierarchical MIM, which is also referred to as the MIT. Each node in the tree represents a managed object or group of objects, and contains its administrative state and its operational state. The hierarchical structure starts at the top (sys) and contains parent and child nodes.

![](/images/dcaui/dcaui_9_3_2.png)

Each node in this tree is a managed object and each object in Cisco UCS has a unique DN that describes the object and its place in the tree. Managed objects are abstractions of the Cisco UCS resources, such as fabric interconnects, chassis, blades, and rack-mounted servers. Most of the policies in the system are configuration policies that describe the configurations of different Cisco UCS components. Policies determine how the system behaves under specific circumstances. Cisco UCS automatically creates certain managed objects rather than users, for example, power supply objects and fan objects. By invoking the API, you are reading and writing objects to the MIM.

### References to Managed Objects

The DME centrally stores and manages the information model, which is a user-level process that runs on the fabric interconnects. When a user initiates an administrative change to a Cisco UCS component (for example, applying a service profile to a server), the DME first applies that change to the information model, and then applies the change to the actual managed endpoint. This approach is called a model-driven framework. The following is a branch diagram that starts at sys from the top root of the Cisco UCS MIT. The diagram shows a Cisco UCS hierarchy.

![](/images/dcaui/dcaui_9_3_3.png)

The contents of the managed objects are referenced during the operation of Cisco UCS. Some of the managed objects are referenced implicitly (PreLoginBanner during login) or as part of the deployment of another managed object. The Service Profile managed object may refer to a template or a vNIC may refer to several VLAN managed objects. A singleton managed object type is found only once in the entire MIT and is typically referred to implicitly.

More than one managed object type(s) may be instantiated one or more times in the MIT. Often, when one managed object refers to another, the reference is made by name. Depending on the type of the referenced managed object, the resolution may be hierarchical. For instance, a service profile template is defined in an org. An org may contain suborgs, and a suborg may have a service profile template that is defined with the same name. Now, when a service profile instance refers to a service profile template (by name), the name is looked up hierarchically from the org of the service profile instance up to the root org. The first match is used. If no match is found, the name “default” is looked up in the same way and the first such match is used.

The Cisco UCS Manager XML API supports operations on a single object or an object hierarchy. An API call can initiate changes to attributes of one or more objects such as chassis, blades, adapters, policies, and other configurable components. The API operates in forgiving mode. Missing attributes are replaced with applicable default values that are maintained in the internal DME. The DME ignores incorrect attributes.

When multiple managed objects are being configured, the API operation stops if any of the managed objects (a virtual NIC, for example) cannot be configured. In that case, the MIT is rolled back to the prior state that preceded the API operation and an error is returned. Updates to managed objects and properties conform to the existing object model to ensure backward compatibility. If existing properties are changed during a product upgrade, they are managed during the database load after the upgrade. New properties are assigned default values.

Operation of the API is transactional and terminates on a single data model. Cisco UCS is responsible for all endpoint communication, such as state updates. Users cannot communicate directly with endpoints, which means that developers do not have to administer isolated, individual component configurations. The API model includes the following programmatic entities:

* **Classes:** Classes define the properties and states of objects in the management information tree.

* **Methods:** Methods are actions that the API performs on one or more objects.

* **Types:** Methods are object properties that map values to the object state (for example, equipment presence).

A typical request comes into the DME and is placed in the transactor queue in FIFO order. The transactor gets the request from the queue, interprets the request, and performs an authorization check. After the request is confirmed, the transactor updates the MIT. This complete operation is done in a single transaction. Full event subscription is enabled. After subscribing, any event notification is sent along with its type of state change.

### Distinguished and Relative Names

You can identify a specific object by its DN or by its relative name RN.

### Distinguished Name

The DN enables you to unambiguously identify a target object. The DN has the following format, which consists of a series of relative names:

![](/images/dcaui/dcaui_9_3_4.png)


```bash
  dn = {rn}/{rn}/{rn}/{rn}... 
```

In the following example, the DN provides a fully qualified path for adaptor-1 from the top of the object tree to the object. The DN specifies the exact managed object on which the API call is operating.

```bash
  < dn =”sys/chassis-5/blade-2/adaptor-1” />
```

### Relative Name

The RN identifies an object within the context of its parent object. The DN is composed of a sequence of RNs, for example, the following:

```bash
  <dn = "sys/chassis-5/blade-2/adaptor-1/host-eth-2"/> is composed of the following relative names:
  Top System MO: rn="sys" equipment Chassis MO: rn="chassis-<id>" compute Blade MO: rn ="blade-<slotId>" adaptor Unit MO: rn="adaptor-<id>" adaptor HostEthIf MO: rn="host-eth-<id>"
```

### Authentication Methods

Authentication methods authenticate and maintain the session.

* **aaaLogin:** Initial method for logging in.
* **aaaRefresh:** Refreshes the current authentication cookie.
* **aaaLogout:** Exits the current session and deactivates the corresponding authentication cookie.

Use the aaaLogin method to get a valid cookie. Use aaaRefresh to maintain the session and keep the cookie active. Use the aaaLogout method to terminate the session (also invalidates the cookie). A maximum of 256 sessions to Cisco UCS can be open at any one time. Operations are performed using the HTTP POST method (Cisco UCS supports both HTTP and HTTPS requests) over TCP. HTTP and HTTPS can be configured to use different port numbers, but TCP/443 (or TCP/80 for nonsecure connections) is used by default. The HTTP envelope contains the XML configuration.

### Login Example

To log in, the XML API client establishes a TCP connection to the Cisco UCS Manager HTTP (or HTTPS) server and posts an XML document containing the aaaLogin method.

In the following example, you are using a TCP connection to port 80 of Cisco UCS Manager with IP address 192.168.16.30.

```bash
  IP: 192.168.16.130
  PORT: 80
  <aaaLogin
      inName="admin"
      inPassword="password" /> 
```

Typical successful login response:

```bash
  <aaaLogin
    response="yes"
    outCookie="1217377205/85f7ff49-e4ec-42fc-9437-da77a1a2c4bf"
        outRefreshPeriod="600"
        outPriv="aaa,ext-lan-policy,ext-lan-qos,ext-san-policy,operations,
                pod-policy,pod-qos,read-only"
    outDomains="mgmt02-dummy"
    outChannel="noencssl"
    outEvtChannel="noencssl">
  </aaaLogin>
```

**Note:** Do not include XML version or DOCTYPE lines in the XML API document. The inName and inPassword attributes are parameters.

Each XML API document represents an operation to be performed. When the request is received as an XML API document, Cisco UCS reads the request and performs the actions as provided in the method. Cisco UCS responds with a message in XML document format and indicates success or failure of the request.

### Query Methods

The following query methods are available.

* **configResolveDn:** Retrieves objects by DN.
* **configResolveDns:** Retrieves objects by a set of DNs.
* **configResolveClass:** Retrieves objects of a given class.
* **configResolveClasses:** Retrieves objects of multiple classes.
* **configFindDnsByClassId:** Retrieves the DNs of a specified class.
* **configResolveChildren:** Retrieves the child objects of an object.
* **configResolveParent:** Retrieves the parent object of an object.
* **configScope:** Performs class queries on a DN in the MIT.

```bash
  <configResolveDn … inHierarchical="false"></> 
  <configResolveDn … inHierarchical="true"></>
```

Most query methods have the argument **inHierarchical** (Boolean true or yes; or false or no). If true, the **inHierarchical** argument returns all child objects.

```bash
  <configResolveDn … inHierarchical="false"></> <configResolveDn … inHierarchical="true"></> 
```

Because the amount of data that is returned from Cisco UCS can be quite large, the inHierarchical argument should be used with care. For example, if the query method is used on a class or DN that refers to a managed object that is located high on the MIT and inHierarchical is set to true, the response can contain almost the entire Cisco UCS configuration. The amount of resources that are required for Cisco UCS to process the request can be high, causing Cisco UCS to take an extended amount of time to respond. To avoid delays, the query method should be performed on a smaller scale involving fewer managed objects.

If a query method does not respond or is taking a long time to respond, increase the timeout period on the client application or adjust the query method to involve fewer managed objects.

The XML query also supports filters. These include simple filters such as Boolean; property filters such as equality, nonequal, greater than, less than, and wildcard; and composite filters such as AND, OR, XOR, and BETWEEN.

The example shows a query by DN, sys/chassis-1/blade-1. An authentication cookie is used that was received after successfully logging in. Finally, the … inHierarchical="false" filter is used.

### Configuration Methods

There are several methods to make configuration changes to managed objects. These changes can be applied to the whole tree, a subtree, or an individual object. The following are examples of configuration methods:

* **configConfMo:** Affects a single managed object (for example, a DN).
* **configConfMos:** Affects multiple subtrees (for example, several DNs).
* **configConfMoGroup:** Makes the same configuration changes to multiple subtree structures (DNs) or managed objects.

### XML Configuration Example

The following is an XML configuration example.

```bash
  <configConfMo                                                  <--- Config Method
      cookie=”1217377205/85f7ff49-e4ec-42fc-9437-da77a1a2c4bf”   <--- Cookie
      dn="sys/rack-unit-1" inHierarchical="false">               <--- DN
      <inConfig>                                                <--- Change Config
          <computeRackUnit                                      <--- MO
              adminPower="cycle-immediate"                      <--- Changes
              usrLbl="Cisco C210 Server" 
              dn="sys/rack-unit-1">
          </computeRackUnit>
        </inConfig>
  </configConfMo>
```

The Cisco UCS XML API has a very specific way of crafting an XML document and includes the following:

1.  This example includes the configConfMo configuration method, which allows changes on a DN.
1.  The cookie that was used to log in initially
1.  The DN on which changes will be made.
1.  The change config indicates what is actually being changed.
1.  The name of the managed object
1.  The changes to the DN that will be made

### Query Example with Response

The figure shows a query example with its response.

![](/images/dcaui/dcaui_9_3_5.png)

This query example shows the following:

1.  The query method
1.  The initial login cookie
1.  The DN of the queried object

As you can see from the successful response, much data was collected. If the inHierarchical argument is set to true, the higher the query is in the hierarchy, the more time is required, and the more data is retrieved.

### Failed Requests

The response to a failed request includes XML attributes for errorCode and errorDescr. The following is an example of a response to a failed request:

```bash
  <configConfMo
      dn="fabric/server“
      cookie="<real_cookie>“
      response="yes“
      errorCode="103“
      invocationResult="unidentified-fail“
      errorDescr="can't create; object already exists.">
  </configConfMo>
```

Notice that there is a failure message. This true failure has an error code, but not all failures are so clear.

### Empty Results

A query request for an existing object is not treated as a failure by the DME. If the object does not exist, Cisco UCS returns a success message, but the XML document contains an empty data field **(<outConfig> </outConfig>)** to indicate that the requested object was not found. The following example shows the response to an attempt to resolve the DN on a nonexistent rack-mounted server:

```bash
  <configResolveDn
      dn="sys/chassis-1/blade-4711"
      cookie="<real_cookie>"
      response="yes">
      <outConfig>
      </outConfig>
  </configResolveDn>
```

##  9.4 Cisco IMC XML API

The Cisco IMC XML API is a programmatic interface to the Cisco IMC software for a Cisco UCS C-Series Rack Server. The API accepts XML documents through HTTP or HTTPS. Developers can use any programming language to generate XML documents that contain the API methods. Configuration and state information for Cisco IMC is stored in a hierarchical tree structure that is known as the MIT, which is completely accessible through the XML API.

The Cisco IMC XML API implements a subset of the methods and MIM that are available in the Cisco UCS Manager XML API. The behavior of these APIs is similar in syntax and semantics, and you can use the same client development tools and techniques for both.

The scope of the Cisco IMC XML API is limited to a single Cisco UCS C-Series Rack Server, in contrast to the Cisco UCS Manager XML API, which controls an entire Cisco UCS domain consisting of switches, FEX modules, servers, and other devices. Using the Cisco IMC XML API, the user has programmatic access to Cisco IMC to configure, administer, and monitor the server. The API provides most of the functions that are accessible through the Cisco IMC CLI and GUI interfaces.

### Cisco IMC Query Methods

Query methods obtain information on the current configuration state of an object. The following are the supported query methods:

* **configResolveDn:** Retrieves objects by DN.
* **configResolveClass:** Retrieves objects of a given class.
* **configResolveChildren:** Retrieves the child objects of an object.
* **configResolveParent:** Retrieves the parent object of an object.

Most query methods have the argument inHierarchical (Boolean true or yes; or false or no). If true, the inHierarchical argument returns all child objects.

### Configuration Methods

There are several methods to make configuration changes to managed objects. These changes can be applied to the whole tree, a subtree, or an individual object. The following are examples of configuration methods:

* **configConfMo:** Affects a single managed object (for example, a DN).
* **configConfMos:** Affects multiple subtrees (for example, several DNs).

### Cisco IMC Example

The following example retrieves information on a local hard drive on a Cisco UCS C-Series server.

Notice the DN, which defines the exact disk that is being queried.

```bash
  <configResolveDn cookie="1313146313/b38e04a0-aa4c-1a4c-8008-cdac3888" 
  inHierarchical="false" dn="sys/rack-unit-1/board/disk-4"/> 
  Response:
  <configResolveDn cookie="1313146313/b38e04a0-aa4c-1a4c-8008-cdac38388" 
  response="yes" 
  dn="sys/rack-unit-1/board/disk-4"> 
      <outConfig> 
    <storageLocalDiskSlotEp id="4" operability="operable" presence="equipped" 
    dn="sys/rack-unit-1/board/disk-4"/> 
      </outConfig> 
  </configResolveDn> 
```

If the inHierarchical argument is set to true, the higher in the hierarchy that you search, the more information you will get. This example is interested in disk-4.

The output indicates that a hard drive is equipped in the slot and is operable.

##  9.7 Python SDK

### Cisco UCS Python SDK

To make Cisco UCS management easier, Cisco has provided a Python SDK library. The Python SDK library abstracts some of the low-level details and provides simple APIs to manage Cisco UCS. It is easier to automate configuration and monitoring using the abstractions that are provided by the SDK.

![](/images/dcaui/dcaui_9_3_6.png)

Python SDK functionalities:

* Manages setup of connections with login details.
* Provides APIs to get managed objects based on class ID or DN.
* Provides APIs to add, set, remove, and compare managed objects.
* Provides the capability to watch managed object events.

### Installation of Python SDK

####  Hosted on GitHub

### Cisco UCS Manager SDK

* Source: https://github.com/CiscoUcs/ucsmsdk
* Samples: https://github.com/CiscoUcs/ucsmsdk_samples
* Documents: https://CiscoUcs.github.io/ucsmsdk_docs

### Cisco UCS IMC SDK

* Source: https://github.com/CiscoUcs/imcsdk
* Documents: https://ciscoucs.github.io/imcsdk_docs

or

Installing the last released version of the SDK from pip.

Python 2.7.X or 3.5.X, or higher
  * Cisco UCS Python SDKs can coexist!

Installation of the Python SDK can be done by downloading from the Cisco UCS GitHub site or from pip. There is an SDK for both Cisco IMC and Cisco UCS. Source, samples, and documentation are all on GitHub. There are thousands of examples and code that have been uploaded to GitHub. Pip is by far the easiest and best way of downloading the SDK.

### Base APIs

The SDK provides APIs to enable CRUD operations.

* **C**reate an object: **add_mo**
* **R**ead an object: **query_dn,query_classid,query_dns,query_classids**
* **U**pdate an object: **set_mo**
* **D**elete an object: **remove_mo**

The Cisco UCS SDK allows you to perform simple create, read, update, and delete (CRUD) operations on objects. The CRUD operations are the four basic functions of persistent storage.

All these methods are invoked using a UCSHandle, which is referred to as simply a “handle” in the following examples.

### Connect Using the Python SDK

####  Connect

```python
  from ucsmsdk.ucshandle import UcsHandle 
  handle = UcsHandle("192.168.1.1", "admin", "password") 
  handle.login()
```

* **handle.query_classid:** Query a specific class ID.
* **handle.query_classids:** Query a group of class ID.
* **handle.query_children:** Query DN children or specific class ID children of a DN.
* **handle.query_dn:** Query a specific DN.
* **handle.query_dns:** Query a group of DNs.

As you can see in the following example, UCSHandle is imported and used to log in with a Cisco UCS domain, username, and password.

Then the handle is used to perform operations. You can query for class ID, a list of class IDs, children of a DN, and specific DNs. Querying children of a DN based on hierarchy can return very lengthy results and require a long time.

### Query Objects

The following examples show various query formats:

  * Query objects based on DN:

```bash
  object = handle.query_dn("org-root/ls-ServiceProfile_Linux01")
```

* Query multiple objects based on multiple DNs:

  The returned object is a dictionary in **{dn:object}** format

```bash
  object_dict = handle.query_dns("org-root/ls-ServiceProfileLinux01", "org-root/ls-SericeProfileLinux02")
```

* Query objects based on the class ID:

  The following returns all objects of type **orgOrg**

```bash
  object_array = handle.query_classid("orgOrg")
```

* Query objects based on multiple class IDs:

  The following returns all objects of type **orgOrg** and **fabricVlan**.

The output is a dictionary of format **{classId: [objects]}**

```bash
  object_dict = handle.query_classids("orgOrg", "fabricVlan")
```

### Query Example

The following example is a query for class IDs.

```bash
  >>>  compute_blades = handle.query.classid(“ComputeBlade”)
  >>>  for computer_blade in compute_blades;
    print () compue_blade.dn
  
  sys/chassis-3/blade-3
  sys/chassis-3/blade-1
  sys/chassis-3/blade-7
  sys/chassis-4/blade-1
  sys/chassis-4/blade-2
  sys/chassis-5/blade-1
  sys/chassis-5/blade-2
  sys/chassis-5/blade-3
  sys/chassis-5/blade-4
  sys/chassis-5/blade-5
  sys/chassis-6/blade-1
  >>> 
```

After this query, you can further break down the needed information by getting the objects of each individual DN in the class ID.

You can get more data than you need with querying, but you can use filters to reduce the amount of data you receive. The following are filter types.

**filter_type:**

* **re:** Default, regular expression match
* **eq:** Equal, exact match
* **ne:** Not equal
* **ge:** Greater than or equal to
* **gt:** Greater than
* **le:** Less than or equal to
* **lt:** Less than

The following is an example of an exact match:

```bash
  filter_str = '(name, "ls-ServiceProfileLinux02", type="eq")'
  sp = handle.query_classid(class_id="LsServer", filter_str=filter_str)
```

The following is an example of a Not Equal query:

```bash
  filter_exp='(model,"UCSB-B200-M4", type="ne")'
  compute_blades = handle.query_classid("ComputeBlade",filter_str=filter_exp)
  print len(compute_blades) , filter_ex
```

### Create, Modify, and Delete Objects

As noted earlier, everything is done with a handle. Creating, modifying, and deleting are done with the following handles:

**Creating:** add_mo
**Modifying:** set_mo
**Deleting:** remove_mo

API operations are batched in FIFO order by default until a commit() is invoked.

* Create

```python
  from ucsmsdk.mometa.ls.LsServer import LsServer
  sp = LsServer(parent_mo_or_dn="org-root", name="sp_ESX03")
  handle.add_mo(sp)
  handle.commit()
```

* Delete

```python
  sp = handle.query_dn("org-root/ls-sp_ESXi03 ")
  handle.remove_mo(sp)
  handle.commit()
```

* Modify Objects Example

```bash
  sp = handle.query_dn("org-root/ls-ESXi03")		
  sp.descr = "demo_descr"
  handle.set_mo(sp)
```

### Retrieve Meta Information

The get_meta_info call is useful for getting information about a managed object.

```python
  from ucsmsdk.ucscoreutils import get_meta_info

  class_meta = get_meta_info("FabricVlan")
  print (class_meta)
```

Metadata is built into the SDK. When retrieving metadata, you can see expressions, lengths, and acceptable values. You can see everything in the class and all objects that are associated with it. This command is extremely powerful because you can see where it belongs in the hierarchy.

The following sample output starts with a tree view that shows where FabricVlan fits, its parents and children, followed by managed object information. It then shows information about the managed object properties, which are defined as follows:

* **xml_attribute:** This property is the name of the property as expected by the server.
* **field_type:** This property is the type of field.
* **min_version:** This property is the Cisco UCS server release in which the property was first introduced.
* **access:** This property determines if a property is internal, user-readable, or user-writable.

The following are property restrictions:

* **min_length:** This restriction is the minimum length for a string property type.
* **max_length:** This restriction is the maximum length for a string property type.
* **pattern:** This restriction identifies the allowed patterns and regular expressions.
* **value_set:** This restriction is the set of allowed values for this property.
* **range_val:** This restriction is the range for int and uint values.

```bash
  sample output: (omitted)
  [FabricEthEstc]
  [FabricEthEstcCloud]
  [FabricEthLan]
  [FabricLanCloud]
    |-FabricVlan
      |-FabricEthMonFiltEp
      |-FabricEthMonSrcEp
      |-FabricEthVlanPc
      |  |-FaultInst
      |-FabricEthVlanPortEp
      |  |-FaultInst
      |-FabricPoolableVlan
      |-FabricSwSubGroup
      |  |-FabricEthVlanPortEp
      |  |  |-FaultInst
      |  |-FabricFcoeVsanPortEp
      |     |-FaultInst
      |-FaultInst

  ClassId                         FabricVlan
  -------                         ----------
  xml_attribute                   :fabricVlan
  rn                              :net-[name]
  min_version                     :1.0(1e)
  access                          :InputOutput
  access_privilege                :['admin', 'ext-lan-config', 'ext-lan-policy']
  parents                         :[u'fabricEthEstc', u'fabricEthEstcCloud', u'fabricEthLan', u'fabricLanCloud']
  children                        :[u'fabricEthMonFiltEp', u'fabricEthMonSrcEp', u'fabricEthVlanPc', u'fabricEthVlanPortEp', u'fabricPoolableVlan', u'fabricSwSubGroup', u'faultInst']

  Property                        assoc_primary_vlan_state
  --------                        ------------------------
  xml_attribute                   :assocPrimaryVlanState
  field_type                      :string
  min_version                     :3.2(3g)
  access                          :READ_ONLY
  min_length                      :None
  max_length                      :None
  pattern                         :None
  value_set                       :['does-not-exists', 'is-empty', 'is-in-error-state', 'is-not-primary-type', 'ok']
  range_val                       :[]

  Property                        assoc_primary_vlan_switch_id
  --------                        ----------------------------
  xml_attribute                   :assocPrimaryVlanSwitchId
  field_type                      :string
  min_version                     :3.2(3g)
  access                          :READ_ONLY
  min_length                      :None
  max_length                      :None
  pattern                         :None
  value_set                       :['A', 'B', 'NONE']
  range_val                       :[]
```

### Finding Class IDs

In the Cisco UCS Manager web interface, it is easy to quickly find class IDs. Right-click an object and choose **Copy XML** to see all information about that object.

![](/images/dcaui/dcaui_9_7_1.png)

The first object will be the class ID.

You can see the details of class IDs by using Python’s **help** command or you can use the class ID in queries or configuration.

### Code Generation Using the Cisco UCS Manager Web Interface

The Cisco UCS Python SDK has a command to translate XML tasks into Python SDK commands. This command allows someone with limited scripting ability to record their tasks in the GUI as XML and then translate them into Python. Automation is so important that Cisco UCS will do it for you.

1.  Click in the browser window and press **ctrl-alt-q** (Windows) or **control-option/alt-q** (MAC) to enable a **Record XML** hyperlink.
1.  Click **Record XML** to start recording.
1.  Perform a task that you want to script.
1.  Click **Stop XML Recording** and enter a filename for the XML.
1.  Run the **convert_to_ucs_python** command.

```python
  from ucsmsdk.utils.converttopython 
  import convert_to_ucs_python convert_to_ucs_python(xml=True, literal_path="C:\\Temp\\vlan-ops_xmlReq.log")

  convert_to_ucs_python()
```

##  9.10  Cisco UCS Manager Ansible Modules

### Benefits of Ansible and Cisco UCS Manager Integration

You can now use Ansible, as your usual tool for configuration management, deployment, and orchestration of Cisco UCS standalone rack servers and Cisco Nexus. Ansible can help you set up infrastructure quickly and easily.

The integration of Cisco UCS Manager and Ansible by Red Hat provides a software-defined approach to the management of the entire hardware and software stack. This solution delivers some significant benefits. You can orchestrate all the steps that are needed to configure Cisco UCS. As a result, you achieve faster build times, because all application stacks can be provisioned automatically in minutes.

You can also automate Cisco UCS Manager policy, resource pool, and resource profile configuration and ongoing management including the ability to detect and remediate unintended changes. Ansible can achieve a desired state across Cisco UCS domains, guarantee uniformity, and prevent manual mistakes, which makes managing several Cisco UCS domains easier and more consistent.

### Control the Full Range of Cisco Infrastructure

![](/images/dcaui/dcaui_9_10_1.png)

Cisco UCS Manager allows you to create reuseable profiles that consist of policies that are established by subject matter experts. The service profiles are a software definition of a server and its LAN and SAN network components. They allow you to define infrastructure configuration and settings across servers, storage, and networking. The service profiles and policies that are created in Cisco UCS Manager allow you to create logical infrastructure from available resource pools.

### How It Works

Ansible allows you to simply describe what you want the infrastructure to look like or the desired state of the infrastructure, and Ansible will determine how to accomplish that task for you.

![](/images/dcaui/dcaui_9_10_2.png)

Ansible performs automation and orchestration of IT environments via playbooks. A playbook uses the YAML syntax to define a series of “plays” for the automation across a set of hosts, which is known as the inventory. Each play consists of multiple tasks that can target one, many, or all the hosts in the inventory. Each task is a call to an Ansible module. Cisco and Red Hat have worked together to develop an Ansible module for Cisco UCS Manager. This module works with the Cisco UCS API.

### Ansible for Standalone Server

You can also use Ansible to support all your standalone Cisco UCS servers. This ability allows you to use one tool to enable greater automation and simplify daily tasks in your environment. The module for the standalone Cisco UCS servers is built on the Cisco IMC Python SDK, which provides programmatic interfaces to all server components.

### Installation Prerequisites

![](/images/dcaui/dcaui_9_10_3.png)

Ansible must be installed. Use pip to install Ansible:

The Cisco UCS Manager Ansible modules depend on the Cisco UCS Manager Python SDK and are required.

### Installation of the Cisco UCS Manager Ansible Module

All the Cisco UCS Manager Ansible modules are in the Cisco UCS GitHub repository. The easiest way to install the Cisco UCS Manager Ansible module is by cloning the GitHub repository.

![](/images/dcaui/dcaui_9_10_4.png)

Git clone https://github.com/ciscoucs/ucsm-ansible

Git reflects active development by Cisco Engineering and includes official documentation and samples for Ansible. Ansible uses the Python SDK “as is.”

After cloning this repository to a local directory, Ansible will find the repository’s library directory as a module path for any playbooks that reside in the ucsm-ansible directory.

### Cisco UCS Ansible Time Zone Example

The following example illustrates the use of the Ansible time zone library.

* **Inventory**

```bash
  [UCS]
  ucs1 ucs_hostname=172.16.143.150 ucs_username=admin ucs_password=password ucs_state=present
```

* **Playbook**

```yaml
  - hosts: "UCS"
    connection: "local"
    gather_facts: "no"
    tasks:
      - name: Configure Time Zone
        ucs_timezone:
          hostname: "{{ ucs_hostname }}"
          username: "{{ ucs_username }}"
          password: "{{ ucs_password }}"
          state: "{{ ucs_state }}"
          admin_state: enabled
          timezone: America/New_York
          description: 'Time Zone for New York'
```

As you can see, the inventory file that is named Cisco UCS defines a Cisco UCS domain by IP address with a username and password. That Cisco UCS domain is defined as UCS1. You can define as many Cisco UCS domains as you want in the inventory file. You can then run the playbook against the list of domains in the inventory file.

In this example, the ucs_timezone library is used. This playbook is used to set the time zone on Cisco UCS to the U.S. Eastern Time Zone.

The playbook file will use the inventory file to fill in the hostname, username, password, and state. Using a product like Ansible Tower can keep this setting in a desired state.

### Run the Cisco UCS Ansible Time Zone Example

The following example illustrates what happens when you run the playbook that is shown in the previous figure:

```bash
  (ansible) student@student-vm:~/ucs_labs/ansible_labs$ ansible-playbook -i inventory ucs_TimeZone_example.yml
  PLAY [UCS]***** ************************************************************
  TASK [Configure Time Zone]***************************************************
  changed: [ucs_manager]
  PLAY RECAP ****************************************************************
  ucs_manager: ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
  (ansible) student@student-vm:~/ucs_labs/ansible_labs$
```

### Run the Playbook

This figure shows a recap of the playbook. The playbook has successfully changed the time zone on the Cisco UCS instance in the inventory file.

### Detailed Ansible Automation for Entire Cisco UCS Domains

Cisco UCS and Ansible can be used to deploy entire Cisco UCS domains and create service profiles, configure storage and the network, and deploy operating systems. The following figure is an example outline for deploying an entire Cisco UCS domain.

![](/images/dcaui/dcaui_9_10_5.png)

First, the playbook configures the fabric interconnects and virtual port channels and set ups all the networking configurations. Then it would configure the chassis, servers, and apply policies. All the server-based configurations would then occur including creating service profiles, associating them with servers, and automatically installing the operating systems by PXE boot.

# 10 Describing Cisco DCNM

##  10.1  Introduction

Cisco Data Center Network Manager (DCNM) is a management system for the Cisco Unified Fabric. It enables you to provision, monitor, and troubleshoot the data center network infrastructure. It provides visibility and control of the unified data center. Cisco DCNM provides a comprehensive feature set that meets the routing, switching, and storage administration needs of data centers. Cisco DCNM streamlines the provisioning for the Cisco Unified Fabric, monitors the SAN and LAN components, and provides a high level of visibility and control through a single web-based management console for Cisco Nexus, Cisco Multilayer Director Switch (MDS), and Cisco Unified Computing System (UCS) products.

##  10.2 Cisco DCNM

Today, data center deployments for LAN, LAN fabrics such as VXLAN, and SAN can get complicated and difficult to manage through CLI or on a box-by-box basis. Cisco Data Center Network Manager (DCNM) is the manager that understands complex concepts, amplifies your operations efforts, gives visibility and situational awareness to large deployments, and supports scenarios for broadcast media solutions with Cisco IP Fabric for Media (IPFM).

![](/images/dcaui/dcaui_10_2_1.png)

### Network Monitoring

Cisco DCNM for SAN (DCNM-SAN) provides extensive SAN discovery, topology mapping, and information viewing capabilities. It collects information on the fabric topology through SNMP queries to the switches connected to it. Cisco DCNM-SAN recreates a fabric topology, presents it in a customizable map, and provides inventory and configuration information in multiple viewing options such as a Fabric View, Device View, Summary View, and Operation View.

### Health and Events Monitoring

Cisco DCNM-SAN works with the Cisco MDS 9000 Series Multilayer Switches to show the health and status of the fabric and switches. Information about the fabric and its components is gathered from multiple sources, including Online System Health Management, Call Home, system messages, and SNMP notifications. This information is made available from multiple menus on Cisco DCNM-SAN or Cisco Device Manager.

### SAN Discovery and Topology Mapping

Once Cisco DCNM-SAN is invoked, a SAN discovery process begins. Using information polled from a seed Cisco MDS 9000 Series Multilayer Switch, including name server registrations, FC-GS, FSPF, and SCSI-3, Cisco DCNM-SAN discovers all devices and interconnects on one or more fabrics. All available switches, HBAs, and storage devices are discovered. The Cisco MDS 9000 Series Multilayer Switches use FMDI to retrieve the HBA model, serial number and firmware version, and host operating-system type and version discovery without host agents. Cisco DCNM-SAN gathers this information through SNMP queries to each switch. The device information that is discovered includes device names, software revision levels, vendor, ISLs, port channels, and VSANs.

Cisco DCNM-SAN is built on a topology representation of the fabric. Cisco DCNM-SAN provides an accurate view of multiple fabrics in a single window by displaying topology maps based on the device discovery information. You can modify the topology map icon layout with an easy-to-use, drag-and-drop interface. The topology map visualizes device interconnections, highlights configuration information such as zones, VSANs, and the ISLs that exceed utilization thresholds. The topology map also provides a visual context for launching CLI sessions, configuring port channels, and opening device managers.

![](/images/dcaui/dcaui_10_2_2.png)

### Performance Monitoring

Device Manager provides an easy tool for monitoring ports on the Cisco MDS 9000 family of switches. This tool gathers statistics at a configurable interval and displays the results in tables or charts. Real-time performance statistics are useful for dynamic troubleshooting and fault isolation within the fabric. Real-time statistics gather data on parts of the fabric in user-configurable intervals and display these results in Cisco DCNM-SAN and Cisco Device Manager. For a chosen port, you can monitor several statistics including traffic in and out, errors, Class 2 traffic, and FICON data.

![](/images/dcaui/dcaui_10_2_3.png)

##  10.3  Cisco DCNM APIs

Cisco DCNM is a management system for Cisco Programmable Fabric. In addition to provisioning, monitoring, and troubleshooting the data center network infrastructure, Cisco DCNM provides a comprehensive feature set that meets the routing, switching, and storage administration needs of the data center. It streamlines the provisioning for Cisco Programmable Fabric and monitors the SAN and LAN components.

Cisco Fabric Automation REST APIs for third-party applications enable you to programmatically control Cisco Fabric Automation. The REST API supports POAP, autoconfiguration, and cable plan features. All REST API operations can also be performed using the Cisco DCNM GUI because Cisco DCNM uses these REST APIs to render the GUI.

An external application can use the authentication REST APIs to authenticate itself to Cisco DCNM to control the Cisco Fabric Automation cluster. After calling login to get the token, all subsequent REST API requests must set the Cisco DCNM Token field with the token in the HTTPS header.

![](/images/dcaui/dcaui_10_3_1.png)

### SMI-S and Web Services Programming

Cisco DCNM provides an industry-standard API using the Storage Management Initiative Specification (SMI-S). The SMI-S facilitates managing SANs in a multivendor environment.

Cisco DCNM-SAN Web Services provide APIs that expose Cisco DCNM-SAN core software functionalities as remote procedure calls to third-party vendors. Software developers can use the APIs to design computer applications that interact with the Cisco DCNM-SAN Server over the network.

With Cisco DCNM-SAN, you can monitor Cisco MDS 9000 switch events, performance, and inventory, and you can perform administrative tasks. Applications can access Cisco DCNM-SAN Web Services through many protocols and data formats such as HTTP, HTTPS, XML, Simple Object Access Protocol (SOAP), and Web Services Description Language (WSDL).

Cisco DCNM-SAN Web Services provide cross-platform operations. Cisco DCNM-SAN Web Services can interact with .NET applications, C++ applications, and applications that are written in other programming languages and Web Services must adhere to accepted conventions to make services interoperable with other applications. For this reason, Cisco DCNM-SAN Web Services must follow the Jakarta XML Web Services (JAX-WS) specification. Cisco DCNM-SAN Web Services relies on JBossWS as a service endpoint engine and as an entry point into the JAX-WS programming model. The framework also allows Cisco DCNM-SAN Web Services to become an important part of the Cisco DCNM-SAN Server run-time environment.

### Web Services Specifications

Cisco DCNM-SAN Web Services specifications combine to provide interoperable protocols for security, communication, and syntax for representing data.

### XML Data Format

XML is the data format that defines the structure of the message. XML Web Services architecture allows programs that are written in various languages on several platforms to communicate with each other in a standards-based way. XML Web Services expose useful functionality to web users through a standard web protocol (SOAP).

### SOAP Protocol

SOAP is the communications protocol for Cisco DCNM-SAN Web Services. SOAP is a specification that defines the XML format for messages. The advantage of SOAP is that it has been implemented on many hardware and software platforms.

### HTTP and HTTPS Transport Layers

HTTP and HTTPS are the transport layers of the service. HTTP and HTTPS allow data to traverse the network easily and they are widely accepted. They are also considered to be platform neutral. Every Cisco DCNM-SAN Web Services operation is through HTTP or HTTPS.

### WDSL Definition

A WSDL definition is an XML document with a root definition element from the http://schemas.xmlsoap.org/wsdl/ namespace. Cisco DCNM-SAN Web Services uses the WSDL document to publish which operations of Cisco DCNM-SAN are available. The definitions element can contain several other elements including types, message, portType, binding, and service, which come from the namespace.

![](/images/dcaui/dcaui_10_3_2.png)

### Summary of Cisco DCNM APIs

| Cisco DCNM API              | API Coverage                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Classic LAN Fabric REST API | Cisco DCNM Connect Cisco DCNM Session Management Cisco DCNM Storage Image Management                                                                                                                                                                                                                                                                                                                                                      |
| LAN Fabric REST API         | Control: Fabrics Control: Interface Service Control: Inventory Control: Links Control: Policies Control: Switches Cisco DCNM Session Management Image Management Resource Manager Operations Top-Down LAN Network Operations Top-Down LAN VRF Operations                                                                                                                                                                                  |
| Switch Roles                | The roles API enables you to view the roles of switches. You can input one or more serial numbers of switches with the GET request to view the role of all switches or certain switches.                                                                                                                                                                                                                                                  |
| Media Controller REST API   | Cisco DCNM Professional Media Network (PMN) Discovered Hosts Cisco DCNM PMN Events Management Cisco DCNM PMN Flow Alias Management Cisco DCNM PMN Flow Status Cisco DCNM PMN Flow Policy Management Cisco DCNM PMN Global Settings Cisco DCNM PMN Host Alias Management Cisco DCNM PMN Host Policy Management Cisco DCNM PMN Static Receiver Management Cisco DCNM PMN Topology Management Cisco DCNM Session Management Image Management |
| SAN Management REST API     | Cisco DCNM Connect Cisco DCNM Session Management Cisco DCNM Storage Image Management                                                                                                                                                                                                                                                                                                                                                      |

# 11  Describing Cisco Intersight

##  11.1  Introduction

Cisco Intersight is the Cisco systems management platform that delivers intuitive computing through cloud-powered intelligence. This platform offers a more intelligent level of management that enables IT organizations to analyze, simplify, and automate their environments in ways that were not possible with prior generations of tools. This capability empowers organizations to achieve significant savings in TCO and to deliver applications faster, so they can support new business initiatives. The advantages of the model-based management of the Cisco Unified Computing System (UCS) platform plus Cisco Intersight are extended to Cisco UCS servers and Cisco HyperFlex and Cisco HyperFlex Edge Systems. Cisco HyperFlex Edge is optimized for remote sites, branch offices, and edge environments.

##  11.2  Cisco Intersight

Cisco Intersight delivers intuitive computing through cloud-powered intelligence. This platform offers a more intelligent level of management that enables IT organizations to analyze, simplify, and automate their environments in ways that were not possible with prior generations of tools.

Main Features of Cisco Intersight

![](/images/dcaui/dcaui_11_2_1.png)

### SaaS Systems Management Platform

Cisco Intersight management helps translate your intent—what you want to accomplish—into infrastructure configuration, ongoing management, and proactive optimization. Cisco UCS and Cisco HyperFlex Systems are fully programmable, so you can manage everything in software from the cloud. The automation is declarative and the policies specify the end state, so there is no configuration drift. Because Cisco Intersight can reach all the locations where your infrastructure resides, it is ready for your enterprise data center and distributed computing environments at the edge in remote office, branch office, and colocation facilities.

### Flexible Deployment Options

The Cisco Intersight virtual appliance provides the same SaaS benefits while enabling customers to decide what data is sent back to Cisco to conform to organizational requirements.

### AI Operations in Action

The Cisco Intersight recommendation engine provides actionable intelligence for IT operations management. Cisco expert systems and best practices are the bases for these insights.

### Connected, Proactive Support

Enhanced capabilities and tight integration with Cisco Technical Assistance Center (TAC) enables more efficient support. Cisco Intersight automates sending files to speed troubleshooting.

### Cisco Intersight Details

![](/images/dcaui/dcaui_11_2_2.png)

### Unified Management

Cisco Intersight simplifies Cisco UCS and Cisco HyperFlex management with a single management platform. It increases scale across data centers and remote locations without extra complexity. Cisco Unified Computing System Manager (UCS Manager), Cisco Integrated Management Controller (IMC) Software, Cisco HyperFlex Connect, and Cisco Unified Computing System Director (UCS Director) tunneling allow access to element managers that do not have local network access

### Configuration, Provisioning, and Server Profiles

Cisco Intersight treats Cisco UCS servers and storage as infrastructure resources that can be allocated and reallocated among application workloads for more dynamic and efficient use of server capacity. Create multiple server profiles with just a few clicks or through the available API, automating the provisioning process.

### Inventory Information and Status

Cisco Intersight displays and reports inventory information for Cisco UCS and Cisco HyperFlex Systems. Use global search to rapidly identify systems based on names, identifiers, and other information. Monitor Cisco UCS and Cisco HyperFlex server alerts and health status across data centers and remote locations.

### Enhanced Support Experience

Automate the generation and forwarding of technical support files to Cisco TAC to accelerate the troubleshooting process and get automated alerts about failure notifications.

### Open API

Cisco Intersight has a RESTful API that supports the OpenAPI Specification (OAS) to provide full programmability and deep integration systems. The Python and PowerShell SDKs enable integrations with Ansible, Chef, Puppet, and other DevOps and IT Operations Management (ITOM) tools.

### Seamless Integration and Upgrade

Upgrades are available for Cisco UCS and Cisco HyperFlex Systems, and Cisco UCS Director software running supported firmware and software versions. Upgrades to Cisco Intersight are delivered automatically without requiring the resources of traditional management tool upgrades.

### Management as a Service

![](/images/dcaui/dcaui_11_2_3.png)

Cisco Intersight provides model-based deployment for Cisco UCS and Cisco HyperFlex platforms, building on the deployment methodologies of Cisco UCS. Cisco HyperFlex platforms can be sent to their destination location, have basic Internet connectivity established locally, and be set up remotely through the cloud-based Cisco HyperFlex installer. A Cisco UCS C-Series Server can be set up quickly and easily by replicating an existing server profile for rapid configuration. The model-based deployment works for a single system in a remote location or hundreds of systems in a data center and enables rapid, standardized configuration and deployment.

### Telemetry Data Collection

To support the complex environments that modern applications create and the dramatically increasing number of endpoints, enterprises require analytics that are tightly integrated with their operations management tools. To enable these analytics capabilities, every Cisco UCS server, Cisco HyperFlex System, or Cisco UCS Director software is configured to connect and transmit to Cisco Intersight certain telemetry information (including server serial numbers and IP addresses, the types of software installed on an endpoint, and feature use data). This telemetry information is used to power the Cisco Intersight recommendation engine. The Cisco Intersight recommendation engine uses the telemetry information to proactively review customer metadata to identify potential issues in customer environments to prevent problems and improve system uptime in the future.

### Customizable Dashboard

Cisco Intersight provides a dashboard that spans Cisco UCS and Cisco HyperFlex Systems. The dashboard is user-customizable, allowing users to focus on the information and tasks that are relevant to them.

### User Interface Launch

Cisco Intersight allows you to monitor Cisco UCS and Cisco HyperFlex Systems from a single management tool. However, if you need to look more deeply into a particular platform, Cisco Intersight provides cross-launch capabilities for virtual KVM (keyboard, video, mouse) sessions or tunneling capabilities for Cisco UCS Manager, Cisco UCS Director, Cisco IMC software, and Cisco HyperFlex Connect to allow secure access to the manager whether you are inside or outside the corporate network.

### Simplified Support

The telemetry data and incidents that are collected from the Cisco Intersight installed base are transmitted using secure communication mechanisms, and this information is available for use by Cisco TAC to provide insights and more proactive support. When combined with Cisco Smart Call Home services, Cisco TAC cases can be automatically opened for certain faults, Cisco TAC agents can initiate log collection, and relevant actions can be initiated with minimal customer input. Even if Cisco Smart Call Home is not used, this capability still speeds case resolution, limits the impact on IT personnel, and ultimately increases system uptime.

### Platform Compliance

Cisco Intersight evaluates your hardware and firmware compatibility to help ensure that your system is compliant with the Cisco UCS Hardware Compatibility List. This process identifies unsupported configurations and alerts you to potential problems that may arise from unknowingly running unsupported configurations.

### Flexible Deployment Options

Choose the ease of SaaS delivery of the Cisco Intersight Management platform on Intersight.com. There is no need to provision resources; simply connect your devices to the management portal, and claim them in the management portal.

##  11.3  Cisco Intersight APIs

The Cisco Intersight API is a programmatic interface that uses the REST architecture to provide access to the Cisco Intersight Management Information Model. API requests may be a read-only query with no side effects or produce modifications of the resources. The response may confirm that some alteration has been made to the resource and it may provide hypertext links to related resources or collections of resources. The Cisco Intersight API accepts and returns messages that are encapsulated through JSON documents and uses HTTP over TLS as the transport protocol.

Cisco Intersight provides the benefits of cloud-based management that customers have come to appreciate with SaaS products. For example, the Cisco Intersight API is updated when new features are deployed to the cloud, providing programmatic access to new IT infrastructure capabilities.

The Cisco Intersight API is based on the OpenAPI standard, which defines a programming language-agnostic interface description for describing, producing, consuming, and visualizing RESTful web services. The OAS for Cisco Intersight allows humans and computers to discover and understand the capabilities of the service without requiring access to source code, further documentation, or inspection of network traffic.

Cisco Intersight provides downloadable SDK packages for popular programming languages. SDKs for dozens of other programming languages can be generated with the help of the open-source OpenAPI tools, including Swagger code generators. Other documents cover available programming language SDKs.

### Supported HTTP Methods

Requests to the Cisco Intersight RESTful API are made to the URI of a resource and elicit a response in JSON format. Cisco Intersight maps standard HTTP methods to RESTful operations. The Cisco Intersight API supports the following HTTP methods:

* GET
* POST
* PATCH
* DELETE

The POST and DELETE methods are idempotent, meaning that there is no extra effect if they are called more than once with the same input parameters. The GET method is nullipotent, meaning that it can be called zero or more times without making changes (or that it is a read-only operation). The PATCH method may or may not be idempotent, depending on the value of the Content-Type HTTP header. When the Content-Type header is set to application/json, the PATCH method is idempotent. When the Content-Type header is set to application/json-patch+json, the PATCH method may not be idempotent.

As an exception to the previously stated rules, the Cisco Intersight search suggester API uses the POST method for complex read-only queries, in which case the POST method is nullipotent.

### Management Information Model

The term “resource” is used in a general sense for a Cisco Intersight addressable document that is specified in the Cisco Intersight Management Information Model and identified by a URI. In the Cisco Intersight API, each addressable REST resource is also called a managed object.

Examples of Cisco Intersight managed objects include the following:

* Cisco UCS servers
* Server components such as DIMMs, CPUs, GPUs, storage controllers, and Cisco IMC
* Cisco UCS Fabric Interconnects
* Firmware inventory
* Cisco HyperFlex nodes and Cisco HyperFlex clusters
* VLANs and VSANs
* Server, network, and storage policies
* Alarms, recommendations, and statistics
* Users, roles, and privileges
* Structured and free-text search results
* Collections of other resources

### Policy Model Overview

The Cisco Intersight API defines an intent-based, declarative policy model. The policies specify the desired resulting state. The end user writes what they want, rather than what to do (in contrast to an imperative policy model).

* **Intent is** invariant: The intent does not change when operational conditions change (such as a server going up or down).

* **Intent is** portable: The intent is abstracted from changes in the infrastructure or certain device particularities.

* **Intent is** composable: The policies are extensible and allow disparate services that are developed independently to express their requirements.

### API Error Processing

When the Cisco Intersight web service cannot process a request, the HTTP response is returned with an HTTP status code, as specified in RFC 7231.

In addition to the HTTP error status code, the HTTP response body contains a JSON document that provides more details about the error. The JSON document contains a human-readable message that is translated based on the value of the accept-language HTTP header.

### API Client Authentication

When a client sends an API request, the Cisco Intersight web service must identify and authenticate the client. The Cisco Intersight web service supports two authentication methods, API keys and session cookies. Other authentication methods may be supported in the future.

##  API Keys

An API key is composed of a keyId and keySecret. The API client uses the API key to cryptographically sign each HTTP request that is sent to the Cisco Intersight web service. The signature parameter is a Base64 encoded digital signature of the message HTTP headers and message content.

### OpenAPI

![](/images/dcaui/dcaui_11_3_1.png)

Cisco Intersight includes an API that supports the OAS (formerly known as the Swagger specification), a powerful definition format to describe RESTful APIs. Support for the OAS provides users with access to an interoperable REST API with tools that automate the generation of the Cisco Intersight API documentation (intersight.com/apidocs), API schemas, and SDKs. The Cisco Intersight API includes fully functional Python and PowerShell SDKs.

The OAS is an API description format for REST APIs. An OpenAPI file allows you to describe your entire API, including the following:

* Available endpoints (/users) and operations on each endpoint (GET /users, POST /users)
* Operation parameters input and output for each operation
* Authentication methods
* Contact information, license, terms of use, and other information

API specifications can be written in YAML or JSON. The format is easy-to-learn and readable to humans and machines.

