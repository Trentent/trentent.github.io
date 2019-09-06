---
id: 576
title: APPV5 - Virtualization Template
date: 2015-02-20T14:06:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/02/20/appv5-virtualization-template/
permalink: /2015/02/20/appv5-virtualization-template/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/02/appv5-virtualization-template.html
blogger_internal:
  - /feeds/7977773/posts/default/9213139227093632222
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
Use if you want, or not. � This virtualization template is to be applied against the sequencer. � I've found it removes a lot of useless captured information that can get caught in a sequence.

<pre class="lang:default decode:true "><?xml version="1.0" encoding="utf-8"?>
<SequencerTemplate xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <AllowMU>false</AllowMU>
  <AppendPackageVersionToFilename>true</AppendPackageVersionToFilename>
  <AllowLocalInteractionToCom>false</AllowLocalInteractionToCom>
  <AllowLocalInteractionToObject>false</AllowLocalInteractionToObject>
  <FullVFSWriteMode>true</FullVFSWriteMode>
  <ExcludePreExistingSxSAndVC>false</ExcludePreExistingSxSAndVC>
  <FileExclusions>
    <string>[{CryptoKeys}]</string>
    <string>[{Common AppData}]\Microsoft\Crypto</string>
    <string>[{Common AppData}]\Microsoft\Search\Data</string>
    <string>[{Cookies}]</string>
    <string>[{History}]</string>
    <string>[{Cache}]</string>
    <string>[{Local AppData}]</string>
    <string>[{Personal}]</string>
    <string>[{Profile}]\Local Settings</string>
    <string>[{Profile}]\NTUSER.DAT</string>
    <string>[{Profile}]\NTUSER.DAT.LOG1</string>
    <string>[{Profile}]\NTUSER.DAT.LOG2</string>
    <string>[{Recent}]</string>
    <string>[{Windows}]\Installer</string>
    <string>[{Windows}]\Debug</string>
    <string>[{Windows}]\Logs\CBS</string>
    <string>[{Windows}]\Temp</string>
    <string>[{Windows}]\WinSxS\ManifestCache</string>
    <string>[{Windows}]\WindowsUpdate.log</string>
    <string>[{Windows}]\System32\config</string>
    <string>[{System}]\config</string>
    <string>[{Windows}]\ServiceProfiles</string>
    <string>[{Windows}]\Logs</string>
    <string>[{AppVPackageDrive}]\$Recycle.Bin</string>
    <string>[{AppVPackageDrive}]\Boot</string>
    <string>[{AppVPackageDrive}]\System Volume Information</string>
    <string>[{AppVSystem32Logfiles}]\Scm</string>
    <string>[{AppData}]\Microsoft\AppV</string>
    <string>[{Local AppData}]\Temp</string>
    <string>[{LocalAppDataLow}]\Microsoft\CryptnetUrlCache</string>
    <string>[{ProgramFilesX64}]\Microsoft Application Virtualization\Sequencer</string>
  </FileExclusions>
  <RegExclusions>
    <string>REGISTRY\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Cryptography</string>
    <string>REGISTRY\MACHINE\SOFTWARE\Microsoft\Cryptography</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer\StreamMRU</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\StreamMRU</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer\Streams</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Streams</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist</string>
    <string>REGISTRY\MACHINE\SOFTWARE\Microsoft\AppV</string>
    <string>REGISTRY\MACHINE\SOFTWARE\Wow6432Node\Microsoft\AppV</string>
    <string>REGISTRY\MACHINE\SYSTEM\CurrentControlSet\Control\MUI</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\AppV</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\ResKit</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Wow6432Node\Microsoft\AppV</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]_CLASSES\Local Settings\MuiCache</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]_CLASSES\Local Settings\Software\Microsoft\Windows\Shell\BagMRU</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]_CLASSES\Local Settings\Software\Microsoft\Windows\Shell\Bags</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Internet Explorer\Toolbar</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap</string>
    <string>REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\RestartManager</string>
    <string>REGISTRY\USER\.DEFAULT\SOFTWARE\Classes\Local Settings\MuiCache</string>
    <string>REGISTRY\USER\.DEFAULT\Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap</string>
  </RegExclusions>
  <TargetOSes />
</SequencerTemplate></pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->