<properties 
	pageTitle="メディア サービスのライブ ストリーミングのワークフロー" 
	description="このトピックでは、一般的なメディア サービスのライブ ストリーミングのワークフローの手順を説明します。" 
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
	ms.date="02/18/2015" 
	ms.author="juliako"/>


#メディア サービスのライブ ストリーミングのワークフロー

##概要

このトピックでは、一般的な Azure Media Services (AMS) のライブ ストリーミングのワークフローの手順を説明します。各手順は関連するトピックにリンクしています。さまざまなテクノロジを使用して達成できるタスクでは、任意のテクノロジへリンクするボタンがあります (たとえば、.NET または REST)。   

メディア サービスは既存のツールとプロセスに統合できます。たとえば、コンテンツをオンサイトでエンコードして、複数の形式にトランスコードするためにメディア サービスにアップロードし、Azure CDN やサードパーティ CDN を通じて配信します。 

次の図は、ビデオ オンデマンド ワークフローに関連するメディア サービス プラットフォームの主要な部分を示しています。
![Live workflow][live-overview]


詳細については、「[Media Services Overview (Media Services の概要)]」をご覧ください(../media-services-overview)。

##メディア サービス アカウントの作成

**Azure 管理ポータル**を使用して [メディア サービス アカウントを作成]します(../media-services-create-account/)。

##ストリーミング エンドポイントの構成

[制作中]

##開発環境の設定  

開発環境の **.NET** または **REST API** を選択します。

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##プログラムによる接続  

**.NET** または **REST API** を選択して、プログラムによって Azure メディア サービスに接続します。

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##ライブ トランスコーダの操作

詳細については、「[Azure Media Services でのサード パーティ ライブ エンコーダーの使用](https://msdn.microsoft.com/library/azure/dn783464.aspx)」をご覧ください。

##チャネル、プログラム、アセットの管理

詳細については、「[ライブ ストリーミング](https://msdn.microsoft.com/library/azure/dn783466.aspx)」をご覧ください。

##コンテンツ保護とコンテンツ キー承認ポリシーの構成 

**.NET** または **REST API** を使用してコンテンツ キー承認ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##アセット配信ポリシーの構成

**.NET** または **REST API** を使用してアセット配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##アセットの公開

**Azure 管理ポータル** または **.NET** を使用してアセットを公開します (ロケータを作成して)。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##メディア サービス アカウントのスケーリング

準備するアカウントの **ストリーミング占有ユニット** の数を指定して、**メディア サービス** の規模を設定できます。 

ストリーミング ユニットの規模の設定については、[ストリーミング占有ユニットの規模変更方法]をご覧ください(../media-services-manage-origins#scale_streaming_endpoints)。



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
<!--HONumber=47-->
