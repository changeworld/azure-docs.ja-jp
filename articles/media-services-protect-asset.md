<properties 
	pageTitle="メディア サービスでアセットを暗号化する方法 - Azure" 
	description="Microsoft PlayReady Protection を使用して メディア サービスでアセットを暗号化する方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#方法:PlayReady または AES 128 で PlayReady を使用してアセットを暗号化します。

この記事は、[メディア サービスのビデオ オンデマンド ワークフロー](../media-services-video-on-demand-workflow) と [Media Services Live Streaming workflow](../media-services-live-streaming-workflow) シリーズの一部です。
  
##概要

Microsoft Azure メディア サービスでは、高度暗号化標準 (AES) (128 ビット暗号化キーを使用) と PlayReady DRM を使用して動的に暗号化したコンテンツを配信できます。メディア サービスでは、承認されたクライアントにキーと PlayReady ライセンスを配信するためのサービスも提供しています。保護されたコンテンツを配信するには、Tキー承認ポリシーとアセットの配信のポリシーを構成する必要があります。

##構成

コンテンツ キー承認ポリシーを構成する方法を参照して、 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

アセットの配信ポリシーを構成する
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 
<!--HONumber=45--> 
