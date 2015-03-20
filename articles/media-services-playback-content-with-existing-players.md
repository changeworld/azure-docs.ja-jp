<properties 
	pageTitle="コンテンツの再生" 
	description="このトピックでは、コンテンツを再生する方法について説明します。" 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


#コンテンツの再生

Azure メディア サービスは、スムーズ ストリーミング、HTTP ライブ ストリーミング、Mpeg-dash など、人気のある多くのストリーミング形式をサポートします。このトピックでは、ストリームのテストに使用できる既存のプレーヤーを示します。  

##既存のプレーヤーによるコンテンツの再生

このトピックでは、コンテンツを再生できる既存のプレーヤーを紹介します。

>[AZURE.NOTE]動的にパッケージ化または暗号化されたコンテンツを再生するには、コンテンツを配信する予定のストリーミング エンドポイントのストリーミング ユニットを必ず 1 つ以上取得するようにしてください。ストリーミング ユニットの規模の設定については、[ストリーミング占有ユニットの規模変更方法]をご覧ください(../media-services-manage-origins#scale_streaming_endpoints)。


###Azure 管理ポータル Media Services コンテンツ プレーヤー

ビデオは、**Azure 管理ポータル**にあるコンテンツ プレ-ヤーを使用してテストできます。

目的のビデオをクリックし ([公開されている]ことを確認してください(../media-services-manage-content#publish))、ポータルの下部にある **[再生]** ボタンをクリックします。 
 
以下の考慮事項があります。

- **メディア サービス コンテンツ プレイヤー**を既定のストリーミング エンドポイントから再生します。既定以外のストリーミング エンドポイントから再生する場合は、別のプレーヤーを使用します ([Azure Media Services プレーヤー](http://amsplayer.azurewebsites.net/azuremediaplayer.html)など)。
 

![AMSPlayer][AMSPlayer]

###Azure Media Services プレーヤー

[Azure Media Services プレーヤー](http://amsplayer.azurewebsites.net/azuremediaplayer.html)を使用して、コンテンツを再生します。


##ビデオ プレーヤーの開発

独自のプレーヤーの開発方法については、「[ビデオ プレーヤーの開発]」をご覧ください。(../media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
