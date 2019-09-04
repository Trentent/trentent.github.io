---
id: 1114
title: Execute Windows XP klist.exe purge without user intervention
date: 2016-04-25T08:23:24-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/648-revision-v1/
permalink: /2016/04/25/648-revision-v1/
---
Using AutoIt:

<pre class="lang:autoit decode:true ">; Script written by Trentent Tye
; May 03, 2013

; This scripts purpose is to execute the "klist.exe purge" command

; silently without user intervention

; This script will cause a window to become visible for a few seconds While

; the "yes" command is passed to klist.exe 

;

; Because klist.exe does not use standard IO when outputting to the command

; prompt until all interactions are complete it's not possible to programmatically

; key in on waiting for its prompts. Due to this, I have this script send 

; "y" to the window that klist.exe is executing in until klist.exe is terminatedy





; Demonstrates the use of StdinWrite()

#include 



;Kills klist.exe first if it exists

$sCommandKill = "taskkill.exe /im klist.exe /f"

Run("taskkill.exe /im klist.exe /f", @SW_SHOW, "")

If ProcessExists("klist.exe") Then

ProcessClose("klist.exe")

EndIf



;setting up the command

$sCommand = "klist.exe purge"

$sTitle = "Klist Run Command"



;executes klist.exe with the purge command in a command prompt with a window

;title we can key on.

Run(@ComSpec & " /C title " & $sTitle & "
" & $sCommand, "C:\", @SW_SHOW, "")



sleep(1000)

Do

WinActivate ( "Klist Run Command")

Send ("y")

;purge each entry

sleep(1000)

;if the process exists then we keep going

if ProcessExists ( "klist.exe" ) Then

$i = 1

Else

$i = 0

EndIf



Until $i =0
</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->