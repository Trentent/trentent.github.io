---
id: 2630
title: 'Citrix XenDesktop/XenApp 7.15 &#8211; The local host cache in action'
date: 2017-11-29T20:41:53-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/11/29/2607-revision-v1/
permalink: /2017/11/29/2607-revision-v1/
---
The Citrix Local Host Cache feature, introduced in XenDesktop/XenApp 7.12, has some nuances that maybe better demonstrated in realtime then typed out in text. � I will do both in this article to share both a &#8216;step by step&#8217; of what happens when you have a network or site database outage and what occurs as well as a realtime video highlighting the feature in action. � There are many other blogs and articles that do a great job going into the step by step details of the feature but I find seeing it in action to be very informative.

To view a video of this process, scroll to the very end, [or click here.](https://www.youtube.com/watch?v=X9GH_NDUgrQ)

To start, I&#8217;ve created a powershell script� that simulates a user querying the broker for a list of applicaitons.

<img class="aligncenter size-full wp-image-2608" src="http://theorypc.ca/wp-content/uploads/2017/11/list.png" alt="" width="232" height="223" /> 

Columns are time of the response, the payload size received (in bytes) and the total time to respond in milliseconds.

As we&#8217;re querying the broker, the broker is reaching out to the database and then responding to the user with the information requested.

<img class="aligncenter size-full wp-image-2609" src="http://theorypc.ca/wp-content/uploads/2017/11/broker.png" alt="" width="215" height="517" srcset="http://theorypc.ca/wp-content/uploads/2017/11/broker.png 215w, http://theorypc.ca/wp-content/uploads/2017/11/broker-125x300.png 125w" sizes="(max-width: 215px) 100vw, 215px" /> 

&nbsp;

Periodically, the Citrix Config Synchronizer Service will check to ensure the local host cache database is in sync with the site database. This is an event that occurs every 2 minutes during normal operation.

To show the network connection failing, I am going to setup a continuous ping to the database server

<img class="aligncenter size-full wp-image-2610" src="http://theorypc.ca/wp-content/uploads/2017/11/ping.png" alt="" width="373" height="193" srcset="http://theorypc.ca/wp-content/uploads/2017/11/ping.png 373w, http://theorypc.ca/wp-content/uploads/2017/11/ping-300x155.png 300w" sizes="(max-width: 373px) 100vw, 373px" /> 

To simulate a network failure, I&#8217;m going to use the tool [clumsy](https://jagt.github.io/clumsy/) to drop all packets to and from the database server.

Clicking start in clumsy immediately stops the simulated user from getting their list of applications.

<img class="aligncenter size-full wp-image-2611" src="http://theorypc.ca/wp-content/uploads/2017/11/clumsy.png" alt="" width="530" height="354" srcset="http://theorypc.ca/wp-content/uploads/2017/11/clumsy.png 530w, http://theorypc.ca/wp-content/uploads/2017/11/clumsy-300x200.png 300w" sizes="(max-width: 530px) 100vw, 530px" /> 

&nbsp;

And the ping&#8217;s now time out in their requests.<img class="aligncenter size-large wp-image-2612" src="http://theorypc.ca/wp-content/uploads/2017/11/pingtimeout.png" alt="" width="143" height="145" srcset="http://theorypc.ca/wp-content/uploads/2017/11/pingtimeout.png 143w, http://theorypc.ca/wp-content/uploads/2017/11/pingtimeout-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2017/11/pingtimeout-100x100.png 100w" sizes="(max-width: 143px) 100vw, 143px" />

The broker has a 20 second time out that after which it will respond to requests with what it thinks is the current status. The first timed out request receives a response of &#8220;working&#8221; and then thereafter a response of &#8220;pending failed&#8221; will be returned

<img class="aligncenter size-full wp-image-2613" src="http://theorypc.ca/wp-content/uploads/2017/11/broker_responses.png" alt="" width="268" height="161" /> 

Around 24 seconds the broker has noticed the database has failed and has logged it&#8217;s first event, 1201, &#8220;The connection between the Citrix Broker Service and the database has been lost&#8221;

<img class="aligncenter size-full wp-image-2614" src="http://theorypc.ca/wp-content/uploads/2017/11/eventlog.png" alt="" width="1380" height="55" srcset="http://theorypc.ca/wp-content/uploads/2017/11/eventlog.png 1380w, http://theorypc.ca/wp-content/uploads/2017/11/eventlog-300x12.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/eventlog-768x31.png 768w" sizes="(max-width: 1380px) 100vw, 1380px" /> 

Now one-minute thirty three seconds into the failure, other Citrix services are now reporting they cannot contact the database.

<img class="aligncenter size-full wp-image-2615" src="http://theorypc.ca/wp-content/uploads/2017/11/otherLogs.png" alt="" width="1006" height="183" srcset="http://theorypc.ca/wp-content/uploads/2017/11/otherLogs.png 1006w, http://theorypc.ca/wp-content/uploads/2017/11/otherLogs-300x55.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/otherLogs-768x140.png 768w" sizes="(max-width: 1006px) 100vw, 1006px" /> 

Just shy of 2 minutes, the broker service has now exceeded it&#8217;s timeout for contacting the database and is in the process of switching to the local host cache. It stops the &#8220;primary broker&#8221;.

<img class="aligncenter size-full wp-image-2616" src="http://theorypc.ca/wp-content/uploads/2017/11/stopped_primarybroker.png" alt="" width="1394" height="75" srcset="http://theorypc.ca/wp-content/uploads/2017/11/stopped_primarybroker.png 1394w, http://theorypc.ca/wp-content/uploads/2017/11/stopped_primarybroker-300x16.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/stopped_primarybroker-768x41.png 768w" sizes="(max-width: 1394px) 100vw, 1394px" /> 

And then the Citrix High Availability Service comes active, brokering user requests.

<img class="aligncenter size-large wp-image-2617" src="http://theorypc.ca/wp-content/uploads/2017/11/started_HABroker.png" alt="" width="1140" height="54" srcset="http://theorypc.ca/wp-content/uploads/2017/11/started_HABroker.png 1388w, http://theorypc.ca/wp-content/uploads/2017/11/started_HABroker-300x14.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/started_HABroker-768x37.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

In my simulation the amount of time it took the user to receive a response from the LHC is a little faster than the site database. The LHC response time is 80-90 milliseconds where the response time for a request that includes the site database is 90-100. This information allows us to visually see the two different modes of operation in action.

<div id="attachment_2618" style="width: 270px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2618" class="wp-image-2618 size-full" src="http://theorypc.ca/wp-content/uploads/2017/11/cutOver.png" alt="" width="260" height="519" srcset="http://theorypc.ca/wp-content/uploads/2017/11/cutOver.png 260w, http://theorypc.ca/wp-content/uploads/2017/11/cutOver-150x300.png 150w" sizes="(max-width: 260px) 100vw, 260px" /></p> 
  
  <p id="caption-attachment-2618" class="wp-caption-text">
    Top, site database response times &#8211; middle is the outage &#8211; bottom is LHC response times
  </p>
</div>

How long does it take to &#8220;fall back&#8221; to the database when connectivity is restored?

I &#8220;Stopped&#8221; clumsy to restore our network connection and started a timer.

<img class="aligncenter size-large wp-image-2620" src="http://theorypc.ca/wp-content/uploads/2017/11/stopClumsy.png" alt="" width="157" height="141" /> 

&nbsp;

We can see the ping responses from the database immediately to verify our connection is back.

<img class="aligncenter size-full wp-image-2619" src="http://theorypc.ca/wp-content/uploads/2017/11/networkIsBack.png" alt="" width="144" height="87" /> 

&nbsp;

Almost immediately, all services have noticed that they have connectivity again, including the broker service.

<img class="aligncenter size-full wp-image-2621" src="http://theorypc.ca/wp-content/uploads/2017/11/ISeeDatabase.png" alt="" width="1219" height="135" srcset="http://theorypc.ca/wp-content/uploads/2017/11/ISeeDatabase.png 1219w, http://theorypc.ca/wp-content/uploads/2017/11/ISeeDatabase-300x33.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/ISeeDatabase-768x85.png 768w" sizes="(max-width: 1219px) 100vw, 1219px" /> 

However, we do not fall back immediately.

At one minute thirty three seconds the broker has switched back to the primary broker. And all services have been restored.

<img class="aligncenter size-full wp-image-2622" src="http://theorypc.ca/wp-content/uploads/2017/11/brokerFallback.png" alt="" width="233" height="211" /> 

<img class="aligncenter size-large wp-image-2623" src="http://theorypc.ca/wp-content/uploads/2017/11/eventLogFallback.png" alt="" width="1140" height="119" srcset="http://theorypc.ca/wp-content/uploads/2017/11/eventLogFallback.png 1371w, http://theorypc.ca/wp-content/uploads/2017/11/eventLogFallback-300x31.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/eventLogFallback-768x80.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

To watch a video of this all in action, please view here:

&nbsp;

&nbsp;

<p style="text-align: center;">
</p>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->