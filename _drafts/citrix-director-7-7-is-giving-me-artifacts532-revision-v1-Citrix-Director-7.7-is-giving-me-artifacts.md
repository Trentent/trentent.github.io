---
id: 971
title: Citrix Director 7.7 is giving me artifacts!
date: 2016-04-24T16:05:17-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/532-revision-v1/
permalink: /2016/04/24/532-revision-v1/
---
We are still in a pilot-preupgrade phase of Citrix Director 7.7 and found an issue where we were getting artifacts with IE11. � The artifacts manifested themselves like so:

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/desktop-director-bad-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/desktop-director-bad-1-300x125.png" width="320" height="133" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is wrong.
    </td>
  </tr>
</table>

<div>
</div>

<div>
</div>

<div>
  When the search box should look like this:
</div>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/director-good-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/director-good-1-300x169.png" width="320" height="180" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is right.
    </td>
  </tr>
</table>

<div>
</div>

<div>
  This issue appears to be caused by a policy our organization uses to enable compatibility view for sites on the Local Intranet:
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/compatib-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/compatib-1-254x300.png" width="270" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      The Culprit
    </td>
  </tr>
</table>

<div>
  Citrix says the following about Director and &#8216;Compatibility View&#8217;:
</div>

[**Director does not support Internet Explorer compatibility mode.** Please use the recommended browser settings. When you install Internet Explorer, accept the default to use the recommended security and compatibility settings. If you already installed the browser and chose not to use the recommended settings, go to Tools > Internet Options > Advanced > Reset and follow the instructions](https://www.citrix.com/blogs/2014/06/09/browser-best-practices-for-citrix-director-4/)

<div>
</div>

<div>
  Because we use a group policy to push this setting out, disabling it and re-enabling it on a site-per-site configuration isn&#8217;t acceptable. � There is an option to set the &#8216;zone assignment&#8217; of our Citrix Director server to be on &#8216;Trusted Sites&#8217; instead of &#8216;Local Intranet&#8217; but this would be another policy that would have to be pushed out to the ~80,000 workstations we have. � Instead, there is another option. � We can edit the default.html file in the Citrix Director folder and add a line in thesection to tell IE to exclude this site from compatibility mode. � To execute this:
</div>

<div>
</div>

<div>
  Edit the Default.html file (&#8220;C:inetpubwwwrootDirectordefault.html&#8221;) and add this line:
</div>

<div>
  <pre class="lang:default decode:true ">&lt;meta http-equiv="X-UA-Compatible" content="IE=9; IE=8; IE=EDGE" /&gt;</pre>
</div>

To the &#8216;head&#8217; section. � Example:

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-19-2Bat-2B3.03.26-2BPM-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-19-2Bat-2B3.03.26-2BPM-1-300x135.png" width="320" height="143" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Add the line around here
    </td>
  </tr>
</table>

<div>
  Delete your cache and now your website will work without issue.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->