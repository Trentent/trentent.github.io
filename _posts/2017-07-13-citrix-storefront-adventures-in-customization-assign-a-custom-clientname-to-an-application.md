---
id: 2508
title: Citrix Storefront – Adventures in customization – Assign a custom clientname to an application
date: 2017-07-13T11:45:25-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2508
permalink: /2017/07/13/citrix-storefront-adventures-in-customization-assign-a-custom-clientname-to-an-application/
image: /wp-content/uploads/2017/07/ClientName.png
categories:
  - Blog
tags:
  - Citrix
  - Storefront
---
I have set a personal goal to avoid creating more than a single store for Storefront. � With that, I&#8217;ve been working on customizing Storefront for our organization and occasionally a problem comes up that requires a unwieldy solution&#8230; � Like creating multiple stores.

An example of this is we have an application that configures itself based on the CLIENTNAME variable. � The current solution for � this problem is to provide ICA files with CLIENTNAME preconfigured. � The application is launched, detects the client name variable then displays the relevant data.

Really, this is a pretty terrible way for the app to configure itself.

You would hope the application would have been coded to accept a switch or something for configuring it and then just publish out the application(s) instead.

But, I&#8217;m not the developer of this application and have to come up with a solution that works within this framework. � The initial thought was we can enable &#8216;overrideicaclientname&#8217; and then modify the default.ica files with the hard coded client name. � Then we&#8217;d create as many stores as is required to support the different configurations&#8230; � Then direct users to the different URL based on their needed configuration&#8230;

If this is starting to sound ugly, it&#8217;s because it is.

[Citrix provides some guidance for manipulating the CLIENTNAME](https://www.citrix.com/blogs/2015/07/01/rewriting-the-session-clientname-from-storefront/). � Unfortunately, the manipulation of the CLIENTNAME in the example would not be sufficient. � We need to configure it to be a specific string and all the examples within are dynamically generated.

Fortunately, Citrix [CTP Sam Jacobs](https://www.mycugc.org/blog/passing-parameters-to-xenapp/xendesktop-by-rewriting-the-storefront-clientname) has come to the rescue and extended the work of [Simon Frost.](https://www.citrix.com/blogs/author/simonf/)� Sam&#8217;s addition was to accept a custom HEADER and configure the CLIENTNAME on that value. � This sounds perfect. � I think I can configure a custom header during the launch of a specific application, and StoreFront will take that name, configure the CLIENTNAME and voila!

So how would you do this? � [Jason Samuel](http://www.jasonsamuel.com/2017/03/02/how-to-rewrite-the-client-name-in-citrix-storefront-3-9-using-storefront-sdk/) goes over the configuration of the necessary steps (it&#8217;s in both Sam and Simon&#8217;s posts as well, but I found Jason&#8217;s post more clean and clear). � The exception is (step 6 in Jason&#8217;s post) the clientNameRewriteRule needs to be set with the custom header we are going to look for.

My instructions are:

  1. Edit &#8220;C:\inetpub\wwwroot\Citrix\Store\web.config&#8221; (note: this is the &#8220;STORE&#8221; web.config and not the STOREWEB)  
    Configure a section &#8220;appSettings&#8221; to be like so:</p> <pre class="lang:xhtml decode:true ">&lt;appSettings&gt;
  &lt;add key="clientNameRewriteRule" value="$H'NewClientName' " /&gt;
&lt;/appSettings&gt;</pre>
    
<img class="aligncenter size-full wp-image-2509" src="http://theorypc.ca/wp-content/uploads/2017/07/Store_WebConfig.png" alt="" width="978" height="192" srcset="http://theorypc.ca/wp-content/uploads/2017/07/Store_WebConfig.png 978w, http://theorypc.ca/wp-content/uploads/2017/07/Store_WebConfig-300x59.png 300w, http://theorypc.ca/wp-content/uploads/2017/07/Store_WebConfig-768x151.png 768w" sizes="(max-width: 978px) 100vw, 978px" /> </li> 
    
      * Find the &#8220;overrideIcaClientName&#8221; and ensure it&#8217;s set to &#8220;on&#8221;: <pre class="lang:xhtml decode:true ">&lt;launch setNoLoadBiasFlag="off" addressResolutionType="DNS-port" requestICAClientSecureChannel="Detect-AnyCiphers" ignoreClientProvidedClientAddress="off" overlayAutoLoginCredsWithTicket="off" overrideIcaClientName="on" requireLaunchReference="on" allowFontSmoothing="on" showDesktopViewer="off" allowSpecialFolderRedirection="off" vdaLogonDataProvider=""&gt;
</pre>
        
<img class="aligncenter size-large wp-image-2511" src="http://theorypc.ca/wp-content/uploads/2017/07/overrideIcaClientName-1600x136.png" alt="" width="1140" height="97" srcset="http://theorypc.ca/wp-content/uploads/2017/07/overrideIcaClientName-1600x136.png 1600w, http://theorypc.ca/wp-content/uploads/2017/07/overrideIcaClientName-300x25.png 300w, http://theorypc.ca/wp-content/uploads/2017/07/overrideIcaClientName-768x65.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> </li> 
        
          * Edit &#8220;C:\inetpub\wwwroot\Citrix\StoreWeb\web.config&#8221;� (note: this is the &#8220;STOREWEB&#8221; web.config)  
            Add a &#8216;forwardedHeaders&#8217; section under &#8220;communication&#8221;</p> <pre class="lang:xhtml decode:true ">&lt;forwardedHeaders&gt;
   &lt;header name="NewClientName" /&gt;
&lt;/forwardedHeaders&gt;</pre>
            
<img class="aligncenter size-full wp-image-2510" src="http://theorypc.ca/wp-content/uploads/2017/07/forwardedHeaders.png" alt="" width="732" height="120" srcset="http://theorypc.ca/wp-content/uploads/2017/07/forwardedHeaders.png 732w, http://theorypc.ca/wp-content/uploads/2017/07/forwardedHeaders-300x49.png 300w" sizes="(max-width: 732px) 100vw, 732px" /> </li> 
            
              * Edit the� &#8220;C:\inetpub\wwwroot\Citrix\StoreWeb\custom\script.js&#8221; file and add the following: <pre class="lang:js decode:true">CTXS.Extensions.doLaunch =  function(app, action) {
	//check for Notepad and configure clientname
	if (app.name == "Notepad") {
		$.ajaxSetup({
			headers : {
				'NewClientName':'ThisIsATest'
			}
		});
	}
	//check for Internet Explorer and configure clientname
	if (app.name == "Internet Explorer 11 - Test") {
		console.log("Customizing Client Name");
		$.ajaxSetup({
			headers : {
				'NewClientName':'DifferentClientName'
			}
		});
	}
    action();
	delete $.ajaxSettings.headers["NewClientName"]; //Remove header
};
</pre>
            
              * Download [Sam&#8217;s &#8220;StoreCustomization_Input.dll&#8221; from here](https://samjacobs.sharefile.com/d-s6907f6e68f94f1e8) and overwrite the placeholder located here:  
                &#8220;C:\inetpub\wwwroot\Citrix\Store\bin&#8221;</ol> 
            
            And? � Did it work?!
            
<img class="aligncenter size-full wp-image-2512" src="http://theorypc.ca/wp-content/uploads/2017/07/ClientNameHardCode.png" alt="" width="556" height="203" srcset="http://theorypc.ca/wp-content/uploads/2017/07/ClientNameHardCode.png 556w, http://theorypc.ca/wp-content/uploads/2017/07/ClientNameHardCode-300x110.png 300w" sizes="(max-width: 556px) 100vw, 556px" /> 
            
            Look at that! � It works! � Splendid!
            
            And now I can configure ClientName based on the application that is launched! � So now we do not need to create multiple stores, we can keep this to a single store and it&#8217;s more dynamic and cleaner (IMHO). � The only caveat is upgrading Storefront these steps may need to be done again as the web.config files may be cleaned or reset.
            
            UDPATE! I found that customizations were being inconsistently applied! [� Check this post for further details and a possible fix!](https://theorypc.ca/2017/07/14/citrix-storefront-adventures-in-customization-customization-breaks-in-internet-explorer/)
            
            <!-- AddThis Advanced Settings generic via filter on the_content -->
            
            <!-- AddThis Share Buttons generic via filter on the_content -->