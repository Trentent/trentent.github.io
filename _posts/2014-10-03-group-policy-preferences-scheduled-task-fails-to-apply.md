---
id: 589
title: 'Group Policy Preferences &#8211; Scheduled Task fails to apply'
date: 2014-10-03T15:43:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/10/03/group-policy-preferences-scheduled-task-fails-to-apply/
permalink: /2014/10/03/group-policy-preferences-scheduled-task-fails-to-apply/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/10/group-policy-preferences-scheduled-task.html
blogger_internal:
  - /feeds/7977773/posts/default/3337392143032930675
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - Active Directory
  - Group Policy
---
<div>
  We had a couple issues with scheduled tasks not applying when submitted as a GPP (Group Policy Preference). � We turned on tracing via local gpedit.msc (Administrative Templates > System > Group Policy > Logging and tracing). � From here we turned on the Scheduled Task logging and events were then stored in the eventvwr.msc (we also turned on tracing which stored a computer.log file here: C:\ProgramData\Group Policy\Trace)
</div>

<div style="font-family: Helvetica; font-size: 12px;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-VeidyIP6wFE/VC8TrZa2F5I/AAAAAAAAAjo/eMGBJ3YGFr0/s1600/image_thumb.png"><img src="http://1.bp.blogspot.com/-VeidyIP6wFE/VC8TrZa2F5I/AAAAAAAAAjo/eMGBJ3YGFr0/s1600/image_thumb.png" width="320" height="191" border="0" /></a>
</div>

<div style="font-family: Helvetica; font-size: 12px;">
</div>

<div>
  The first error we got was:
</div>

<div style="font-family: Helvetica; font-size: 12px;">
  <pre class="lang:default decode:true ">2014-10-03 10:42:19.372 [pid=0x59c,tid=0x1294] No item to delete.
2014-10-03 10:42:19.372 [pid=0x59c,tid=0x1294] pWorkItemV2-&gt;Create [ hr = 0x80070534 "No mapping between account names and security IDs was done." ]
2014-10-03 10:42:19.372 [pid=0x59c,tid=0x1294] replaceTask [ hr = 0x80070534 "No mapping between account names and security IDs was done." ]
2014-10-03 10:42:19.372 [pid=0x59c,tid=0x1294] Properties handled. [ hr = 0x80070534 "No mapping between account names and security IDs was done." ]
2014-10-03 10:42:19.388 [pid=0x59c,tid=0x1294] EVENT : The computer 'AHS-Add-GlobalPrinters' preference item in the 'CTX XenApp 65 Test {E6775312-AAC0-45C3-8A1C-5F5EA46701A7}' Group Policy object did not apply because it failed with error code '0x80070534 No mapping between account names and security IDs was done.'%100790275
2014-10-03 10:42:19.388 [pid=0x59c,tid=0x1294] Completed class - AHS-Add-GlobalPrinters. [ hr = 0x80070534 "No mapping between account names and security IDs was done." ]
2014-10-03 10:42:19.388 [pid=0x59c,tid=0x1294] Error suppressed. [ hr = 0x80070534 "No mapping between account names and security IDs was done." ]</pre>
</div>

<div style="font-family: Helvetica; font-size: 12px;">
</div>

<div>
  So it can&#8217;t map between user ID&#8217;s. � It&#8217;d be nice if it told us which mapping failed, but it gives us a pretty good hint. Looking at the XML file the GPP creates (stored here: � &#8220;C:\ProgramData\Microsoft\Group Policy\History\\Machine\Preferences\ScheduledTasks\ScheduledTasks.xml&#8221; )
</div>

<div>
  We saw the following:
</div>

<div style="font-family: Helvetica; font-size: 12px;">
  <pre class="lang:default decode:true ">&lt;?xml version="1.0" encoding="UTF-8" ?&gt;&lt;ScheduledTasks clsid="{CC63F200-7309-4ba0-B154-A71CD118DBCC}"&gt;  
 &lt;TaskV2 clsid="{D8896631-B747-47a7-84A6-C155337F3BC8}" name="AHS-Add-GlobalPrinters" image="1" changed="2014-10-03 17:15:15" uid="{EDDCD78F-46AC-414A-92B2-65D37D12E3F9}" bypassErrors="0" userContext="0" removePolicy="1" desc="Create Network Printer Queues on Cancer Control XenApp Servers" policyApplied="1"&gt; 
 &lt;Properties action="R" name="AHS-Add-GlobalPrinters" runAs="NT AUTHORITY\SYSTEM" logonType="S4U"&gt; 
 &lt;Task version="1.2"&gt; 
 &lt;RegistrationInfo&gt; 
 &lt;Author&gt;HEALTHY\ssalehianadm&lt;/Author&gt; 
 &lt;Description&gt;Create Network Printer Queues on Cancer Control XenApp Servers&lt;/Description&gt;&lt;/RegistrationInfo&gt; 
 &lt;Principals&gt; 
 &lt;Principal id="Author"&gt; 
 &lt;RunLevel&gt;HighestAvailable&lt;/RunLevel&gt; 
 &lt;LogonType&gt;S4U&lt;/LogonType&gt; 
 &lt;UserId&gt;BUILTIN\SYSTEM&lt;/UserId&gt;&lt;/Principal&gt;&lt;/Principals&gt; 
 &lt;Settings&gt; 
 &lt;IdleSettings&gt; 
 &lt;Duration&gt;PT10M&lt;/Duration&gt; 
 &lt;WaitTimeout&gt;PT1H&lt;/WaitTimeout&gt; 
 &lt;StopOnIdleEnd&gt;true&lt;/StopOnIdleEnd&gt; 
 &lt;RestartOnIdle&gt;false&lt;/RestartOnIdle&gt;&lt;/IdleSettings&gt; 
 &lt;MultipleInstancesPolicy&gt;IgnoreNew&lt;/MultipleInstancesPolicy&gt; 
 &lt;DisallowStartIfOnBatteries&gt;true&lt;/DisallowStartIfOnBatteries&gt; 
 &lt;StopIfGoingOnBatteries&gt;true&lt;/StopIfGoingOnBatteries&gt; 
 &lt;AllowHardTerminate&gt;false&lt;/AllowHardTerminate&gt; 
 &lt;AllowStartOnDemand&gt;true&lt;/AllowStartOnDemand&gt; 
 &lt;Enabled&gt;true&lt;/Enabled&gt; 
 &lt;Hidden&gt;false&lt;/Hidden&gt; 
 &lt;ExecutionTimeLimit&gt;PT0S&lt;/ExecutionTimeLimit&gt; 
 &lt;Priority&gt;7&lt;/Priority&gt;&lt;/Settings&gt; 
 &lt;Triggers&gt;&lt;/Triggers&gt; 
 &lt;Actions&gt; 
 &lt;Exec&gt; 
 &lt;Command&gt;C:\Windows\System32\cmd.exe&lt;/Command&gt; 
 &lt;Arguments&gt;/c C:\PublishedApplications\AHS-Add-GlobalPrinters.cmd WSLBLPRINT01&lt;/Arguments&gt;&lt;/Exec&gt;&lt;/Actions&gt;&lt;/Task&gt;&lt;/Properties&gt; 
 &lt;Filters&gt;</pre>
</div>

<div>
  <div>
    Everything validates. � Googling for BUILTIN\SYSTEM brought up that several people were getting the same error when using BUILTIN\SYSTEM. � Which makes some sense as &#8220;BUILTIN\SYSTEM&#8221; isn&#8217;t a real account. � We renamed it to NT AUTHORITY\SYSTEM. � This time we got a new error message:
  </div>
  
  <div style="font-family: Helvetica; font-size: 12px;">
  </div>
  
  <div style="font-family: Helvetica; font-size: 12px;">
  </div>
  
  <div>
    <div style="font-family: Helvetica; font-size: 12px;">
      <pre class="lang:default decode:true ">The computer 'AHS-Add-GlobalPrinters' preference item in the 'CTX XenApp 65 Prod {CB954F1D-7AE5-4706-9BCC-995A0D83CED5}' Group Policy object did not apply because it failed with error code '0x80041316 The task XML contains an unexpected node.' See trace file for more details.</pre>
    </div>
  </div>
  
  <div>
  </div>
  
  <div>
    This doesn&#8217;t tell us a whole lot of information. � What is the unexpected node? Looking again at the XML file it looked like so:
  </div>
  
  <div>
  </div>
  
  <div>
    <pre class="lang:default decode:true ">&lt;?xml version="1.0" encoding="UTF-8" ?&gt;&lt;ScheduledTasks clsid="{CC63F200-7309-4ba0-B154-A71CD118DBCC}"&gt;  
&lt;TaskV2 clsid="{D8896631-B747-47a7-84A6-C155337F3BC8}" name="AHS-Add-GlobalPrinters" image="1" changed="2014-10-03 17:15:15" uid="{EDDCD78F-46AC-414A-92B2-65D37D12E3F9}" bypassErrors="0" userContext="0" removePolicy="1" desc="Create Network Printer Queues on Cancer Control XenApp Servers" policyApplied="1"&gt; 
&lt;Properties action="R" name="AHS-Add-GlobalPrinters" runAs="NT AUTHORITY\SYSTEM" logonType="S4U"&gt; 
&lt;Task version="1.2"&gt; 
&lt;RegistrationInfo&gt; 
&lt;Author&gt;HEALTHY\ssalehianadm&lt;/Author&gt; 
&lt;Description&gt;Create Network Printer Queues on Cancer Control XenApp Servers&lt;/Description&gt;&lt;/RegistrationInfo&gt; 
&lt;Principals&gt; 
&lt;Principal id="Author"&gt; 
&lt;RunLevel&gt;HighestAvailable&lt;/RunLevel&gt; 
&lt;LogonType&gt;S4U&lt;/LogonType&gt; 
&lt;GroupId&gt;NT AUTHORITY\SYSTEM&lt;/GroupId&gt;&lt;/Principal&gt;&lt;/Principals&gt; 
&lt;Settings&gt; 
&lt;IdleSettings&gt; 
&lt;Duration&gt;PT10M&lt;/Duration&gt; 
&lt;WaitTimeout&gt;PT1H&lt;/WaitTimeout&gt; 
&lt;StopOnIdleEnd&gt;true&lt;/StopOnIdleEnd&gt; 
&lt;RestartOnIdle&gt;false&lt;/RestartOnIdle&gt;&lt;/IdleSettings&gt; 
&lt;MultipleInstancesPolicy&gt;IgnoreNew&lt;/MultipleInstancesPolicy&gt; 
&lt;DisallowStartIfOnBatteries&gt;true&lt;/DisallowStartIfOnBatteries&gt; 
&lt;StopIfGoingOnBatteries&gt;true&lt;/StopIfGoingOnBatteries&gt; 
&lt;AllowHardTerminate&gt;false&lt;/AllowHardTerminate&gt; 
&lt;AllowStartOnDemand&gt;true&lt;/AllowStartOnDemand&gt; 
&lt;Enabled&gt;true&lt;/Enabled&gt; 
&lt;Hidden&gt;false&lt;/Hidden&gt; 
&lt;ExecutionTimeLimit&gt;PT0S&lt;/ExecutionTimeLimit&gt; 
&lt;Priority&gt;7&lt;/Priority&gt;&lt;/Settings&gt; 
&lt;Triggers&gt;&lt;/Triggers&gt; 
&lt;Actions&gt; 
&lt;Exec&gt; 
&lt;Command&gt;C:\Windows\System32\cmd.exe&lt;/Command&gt; 
&lt;Arguments&gt;/c C:\PublishedApplications\AHS-Add-GlobalPrinters.cmd WSLBLPRINT01&lt;/Arguments&gt;&lt;/Exec&gt;&lt;/Actions&gt;&lt;/Task&gt;&lt;/Properties&gt; 
&lt;Filters&gt;</pre>
  </div>
  
  <div style="font-family: Helvetica; font-size: 12px;">
  </div>
  
  <div>
    <div>
      The difference that I can see:
    </div>
    
    <div>
      <<span style="text-decoration: underline;"><strong>GroupId</strong></span>>NT AUTHORITY\SYSTEM</<span style="text-decoration: underline;"><strong>GroupId</strong></span>>
    </div>
    
    <div>
    </div>
    
    <div>
      The SYSTEM account is NOT a group. � We changed how we selected the SYSTEM account by &#8220;Browsing&#8221; AD, going into the root of the domain, going into the Builtin OU, and selecting SYSTEM. � This populated as &#8220;NT AUTHORITY\Well-Known-Security-Id-System&#8221;. � This will fail because there is no such user account called &#8220;Well-Known-Security-Id-System&#8221;. � At this point we renamed it to &#8220;NT AUTHORITY\SYSTEM&#8221;.
    </div>
    
    <div>
    </div>
    
    <div>
      Boom, GPP Scheduled task now worked without issue. � Checking the XML the difference by manually selecting the SYSTEM account changed
    </div>
    
    <div>
      <<span style="text-decoration: underline;"><strong>GroupId</strong></span>>NT AUTHORITY\SYSTEM</<span style="text-decoration: underline;"><strong>GroupId</strong></span>>
    </div>
    
    <div>
      To
    </div>
    
    <div>
      <<span style="text-decoration: underline;"><strong>UserId</strong></span>>NT AUTHORITY\SYSTEM</<span style="text-decoration: underline;"><strong>UserId</strong></span>� >
    </div>
    
    <div>
    </div>
    
    <div>
      SO.
    </div>
    
    <div>
    </div>
    
    <div>
      If you are having issues with your GPP Scheduled task item running as the SYSTEM account I would HIGHLY recommend you check your XML file and confirm it is set as &#8220;<span style="text-decoration: underline;"><strong>NT AUTHORITY\SYSTEM</strong></span>&#8221; and it is surrounded by UserId� <span style="text-decoration: underline;"><strong>NOT</strong></span>� � GroupId.
    </div>
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->