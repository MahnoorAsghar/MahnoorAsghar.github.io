---
layout: page
title: "Everyone Struggles"
subtitle: ""
date: 2021-12-30 12:00:00 +0500
categories: ["Outreachy"]
---

<h3>First impression of Ironic</h3>
I was introduced to Ironic as an OpenStack component used to provision baremetal machines. The OpenStack Compute service offered users an API allowing them to provision virtual machines as per their requirement, and also provision baremetal machines via the same API, thanks to Ironic. 

In the first few days of my reading about Ironic, and attending a few community meetings (in listening/reading mode), I came across a myriad of terms that I did not know the meaning of. After spending some time reading and asking around, I started to get a feel of some of the terms that were used around baremetal provisioning. It took me some days to piece things together, and there is still a long way to go, but I have brushed up on some basics, and begun to understand some new terms.

<h3>Digging deeper</h3>
Ironic consumes requests from the users via the <a href='https://restfulapi.net/'>RESTful</a> <b>ironic-api</b>, and sends them to the <b>ironic-conductor</b>, which is the main component responsible for managing the baremetal nodes. It is responsible for turning the node on/off, adding/editing/deleting the nodes, and cleaning, provisioning, and deploying the node. 
The components of the conductor that perform the actual hardware management are called ‘<b>drivers</b>’ or ‘<b>hardware types</b>’. Drivers are composed of ‘<b>interfaces</b>’ to perform certain functions: like power management, hardware inspection, etc. For example, the iDRAC driver supports: 
* BIOS Interface: BIOS management
* Inspect Interface: Hardware inspection
* Management Interface: Boot device and firmware management
* Power Interface: Power management
* RAID Interface: RAID controller and disk management 
* Vendor Interface: BIOS management (using WSMAN protocol) and eject virtual media (Redfish protocol)

In order to support a wide range of hardware, Ironic supports some generic drivers and some vendor-specific drivers. The generic drivers supported by most hardware are <b>IPMI</b> and <b>Redfish</b>, and vendors can implement their own drivers as well to add more functionality. For example, Dell has the iDRAC driver, HP has the iLO driver, and Huawei has iBMC. The hardware and driver must at least have support for the following features:
* Getting and setting the power state of the machine
* Getting and setting the current boot device
* Being able to boot the machine from an image provided by Ironic 

Ironic can boot a machine using <b>PXE/iPXE</b> mechanisms, or using virtual media. PXE booting means to bootstrap a machine from the network instead of from disk. Instead of a regular bootloader like grub, a <b>NBP</b> (Network Bootstrap Program) is used to load the OS kernel/ramdisk from the network into memory. It is also possible to boot from an ISO image in memory.

Ironic is also capable of inspecting hardware that is unknown, i.e. finding the available resources on the node. This is done by booting a <b>ramdisk</b> running the <b>Ironic Python Agent (IPA)</b> onto the node.  
The hardware inspection can be performed <b>in-band</b> (i.e. by booting a ramdisk into the node) or <b>out-of-band</b> (i.e. by accessing the management controller/BMC of the node). In-band inspection forms a service called the <b>ironic-inspector</b> service. The exact hardware details that can be discovered depend on the hardware type. The basic information generally gathered contains: 
* CPU information (number of CPUs, CPU architecture, flags, model)
* Memory information (installed, available)
* BMC addresses (IPv4, IPv6)
* Disk information (name, model, size, rotational, disk path)
* Boot mode (UEFI/BIOS) and PXE interface
* Hostname 

More information is generally available as inspection output; for e.g. data captured from the network ports, internal hardware layout/architecture (e.g. core-to-memory mapping for setting up SRIOV or DPDK), etc. The data gathered during inspection is plugin-driven and can be extended.

The IPA can be used to rescue a node if the node’s disk has failed, or if the OS is otherwise inaccessible. It can also be used to inject files onto the node.

Ironic is able to clean a node’s existing configurations and apply new ones (e.g. RAID and BIOS configurations), set up the filesystem and disk partitions on the node, and deploy an OS on the node. It allows us to manage the complete lifecycle of a baremetal node. Depending on the work being done, the node is on one stage on the Ironic state machine, more about which can be read here.

The <a href='https://docs.openstack.org/ironic/latest/'>documentation</a> is my biggest source of information to learn more about Ironic. I have also found that the Ironic community is very friendly and helpful in answering questions. When I was setting up the environment to work on <a href='https://review.opendev.org/c/openstack/python-ironicclient/+/816098'>my code contribution for the Outreachy application</a>, I was faced with an issue. I was supposed to alter the help text for the baremetal sub-command on the python-ironicclient. I installed the client and cloned the repository, but the changes I made in the repository code were not being reflected when I invoked the CLI command. After spending some time and only getting more bewildered, I ventured to the openstack-ironic channel on the IRC and described my problem. There were some contributors active in the channel, and they pointed out that I might need to install my python-ironicclient repository as a package on my local machine to be able to see the changes to the CLI help text. I looked up how to do that, and with some help I was able to see the changes! After many days of silently listening in on the channel, I gathered the courage to ask a question, and it was very pleasant to get a friendly and helpful response :)

