<properties 
	pageTitle="" 
	description="Using mobile services in Cordova projects" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/11/2014" 
	ms.author="patshea"/>

### Visual Studio 2015 Preview の Cordova プロジェクトでの Mobile Services の使用

Visual Studio 2015 Preview の Cordova プロジェクトで Azure Mobile Services を使用するには、次の回避策を適用する必要があります。

1. Visual Studio 2015 Preview の Cordova プロジェクトで、Config.xml を開き、**[プラグイン]** タブで、**Microsoft Azure Mobile Services** プラグインを有効にします。<br/>
![][1]

2. Index.html で、**MobileServices.Web-1.2.2.min.js** を参照している行を削除します。<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;meta charset="utf-8" /&gt;
    &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</PRE>

3. services\mobileServices\settings フォルダーにある {yourservicename}.js を開き、既存のスニペットを次に置き換えます。

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 

<!--HONumber=42-->
