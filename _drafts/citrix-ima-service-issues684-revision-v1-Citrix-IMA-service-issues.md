---
id: 1174
title: Citrix IMA service issues
date: 2016-04-25T11:12:00-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/684-revision-v1/
permalink: /2016/04/25/684-revision-v1/
---
If Citrix is giving you grief because IMA won&#8217;t start after unjoining and rejoining a farm, do the following:  
IMA Service Fails to Start and MFCOM Service Hangs in a Starting State  
Document ID: CTX127922 / Created On: 20-Jan-2011 / Updated On: 20-Oct-2011  
Average Rating: (5 ratings)  
View products this document applies to

Symptoms  
IMA Service fails to start and MFCOM Service hangs in a Starting state.  
Event ID: 7024  
The Independent Management Architecture service terminated with service-specific error: 2147483649 (0x80000001).  
&#8211; Or –  
The IMA service terminated with service-specific error: 2147483647  
Cause  
When looking into the services manager, the MFCOM Service is in status “starting”. MFCOM and IMA Service fail to start because of a corrupt radeoffline DB.  
Note: This issue also occurs after an incomplete or corrupted install of a Citrix Hotfix. Make sure you terminate the MFCOM32.exe as instructed below and re-install the hotfix properly. This can also ensure that the MFCOM Service will start successfully.  
Resolution  
**Stop the mfcom.exe service using Task Manager.  
Execute the following commands:  
** 

<pre class="lang:batch decode:true ">Dsmaint recreatelhc
Dsmaint recreaterade
Start the IMA Service and MFCOM Service.</pre>

**�** 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->