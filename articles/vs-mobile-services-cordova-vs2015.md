<properties title="Using Mobile Services with Cordova Projects" pageTitle="" metaKeywords="Azure, Cordova, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/11/2014" ms.author="ghogen" />

### Visual Studio 2015 Preview の Cordova プロジェクトでの Mobile Services の使用

Visual Studio 2015 Preview の Cordova プロジェクトで Azure Mobile Services を使用するには、次の回避策を適用する必要があります。

1. Visual Studio 2015 Preview の Cordova プロジェクトで、Config.xml を開き、[**プラグイン**] タブで、**Microsoft Azure Mobile Services** プラグインを有効にします。<br/>
![][1]

2. Index.html で、**MobileServices.Web-1.2.2.min.js** を参照している行を削除します。<br/>

<PRE style="prettyprint">
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>MyCordovaApp>/title>

    <!--MyCordovaApp references -->
    <link href="css/index.css" rel="stylesheet" />
</head>
<body>
    <p>Hello world!&lt/p>

    <!--Cordova reference, this is added to your app when it's build. -->
    <script src="cordova.js"></script>
    <script src="scripts/platformOverrides.js"></script>

    <script src="scripts/index.js"></script>

    <!-- yourservicename references -->
    <span style="background-color:yellow"><script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js">&lt/script></span>
    <script src="/services/mobileservices/settings/yourservicename.js"></script>
</body>
</html>
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
