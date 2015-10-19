<properties 
	pageTitle="パートナーを使用して Azure Media Services に Widevine ライセンスを配信する" 
	description="この記事では、PlayReady と Widevine DRM の両方を使用して AMS で動的に暗号化されたストリームを、Azure Media Services (AMS) を使用して配信する方法について説明します。PlayReady ライセンスは Media Services PlayReady サーバーから取得し、Widevine ライセンスは castLabs ライセンス サーバーから取得します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#パートナーを使用して Azure Media Services に Widevine ライセンスを配信する

##概要

Microsoft Azure Media Services を使用すると、Widevine DRM で保護された MPEG-DASH を配信できます。Widevine DRM は、共通暗号化 (CENC) 仕様に従って暗号化されています。

>[AZURE.NOTE]現在、Media Services では、Widevine のライセンス サーバーは提供されません。Widevine ライセンスは、AMS パートナーの [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/) を通して配信できます。

##castLabs

[castLabs](http://castlabs.com/company/partners/azure/) を使用して Widevine ライセンスを配信できます。詳細については、「[castLabs を使用して Azure Media Services に DRM ライセンスを配信する](media-services-castlabs-integration.md)」を参照してください。

##Axinom

[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) を使用して Widevine ライセンスを配信できます。詳細については、「[Axinom を使用して Azure Media Services に DRM ライセンスを配信する](media-services-axinom-integration.md)」を参照してください。


##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##関連項目

[PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)

[Mingfei のブログ](https://azure.microsoft.com/ja-JP/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Oct15_HO2-->