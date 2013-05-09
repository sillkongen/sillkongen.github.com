---
layout: post
title: "Replace bootblock in Solaris"
description: ""
category: Solaris
tags: [Solaris]
---
{% include JB/setup %}

###Sun hardware status
You have turned on the unit and the system reports the bootblock not being a executable file. 
	Probing system devices
	Probing memory
	Probing I/O buses

	Sun Fire V240, No Keyboard
	Copyright 2006 Sun Microsystems, Inc.  All rights reserved.
	OpenBoot 4.22.19, 8192 MB memory installed, Serial #76523464.
	Ethernet address 0:14:4f:8f:a7:c8, Host ID: 848fa7c8.
	Initializing     1MB of memory at addr		127feec000                                                                   
 	Initializing     1MB of memory at addr		127fe90000                                                                     
	Initializing    15MB of memory at addr		127f000000                                                                     
 	Initializing  2032MB of memory at addr 		1200000000                                                                     
 	Initializing  2048MB of memory at addr 		1000000000                                                                     
 	Initializing  2048MB of memory at addr		200000000                                                                    
 	Initializing  2048MB of memory at addr                 0                                                                     

	Rebooting with command: bootBoot device: /pci@1e,600000/LSILogic,scsi@3/disk@2,0:a  File and args: The file just loaded does not appear to be executable.
	{1} ok  
###Boot from a CDROM
Get a physical media with Solaris 9 or Solaris 10 (prior to 10/08 release*). To boot from a CDROM from the openboot firmware and bring up the machine in a single user mode by typing

	{1} ok  boot cdrom –s 
###Replace the bootblock
	installboot /usr/platform/`uname -i`/lib/fs/ufs/bootblk /dev/rdsk/c0t0d0s0
