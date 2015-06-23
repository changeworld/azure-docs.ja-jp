<properties 
	pageTitle="コンテンツの再生" 
	description="このトピックでは、コンテンツの再生に使用できる既存のプレーヤーのリストを示します。" 
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
	ms.date="04/08/2015" 
	ms.author="juliako"/>


# 既存のプレーヤーによるコンテンツの再生

Azure Media Services は、スムーズ ストリーミング、HTTP ライブ ストリーミング、Mpeg-dash など、人気のある多くのストリーミング形式をサポートします。このトピックでは、ストリームのテストに使用できる既存のプレーヤーを示します。  

>[AZURE.NOTE]動的にパッケージ化または暗号化されたコンテンツを再生するには、コンテンツを配信する予定のストリーミング エンドポイントのストリーミング ユニットを必ず 1 つ以上取得するようにしてください。ストリーミング ユニットの規模の設定については、「[ストリーミング ユニットの拡張](media-services-manage-origins.md#scale_streaming_endpoints)」ご覧ください。

### Azure 管理ポータル Media Services コンテンツ プレーヤー

ビデオは、**Azure 管理ポータル**にあるコンテンツ プレ-ヤーを使用してテストできます。

目的のビデオをクリックし ([発行済](media-services-manage-content.md#publish)であることを確認します)、ポータルの下部の **[再生]** ボタンをクリックします。 
 
いくつかの考慮事項が適用されます。

- **Media Services  コンテンツ プレイヤー**を既定のストリーミング エンドポイントから再生します。既定以外のストリーミング エンドポイントから再生する場合は、別のプレーヤーを使用します([Azure Media Services プレーヤー](http://amsplayer.azurewebsites.net/azuremediaplayer.html)など)。
 

![AMSPlayer][AMSPlayer]

### Azure Media Services プレーヤー

[Azure Media Services プレイヤー](http://amsplayer.azurewebsites.net/azuremediaplayer.html)を使用して、次のいずれかの形式でコンテンツ (平文のコンテンツまたは保護されたコンテンツ) を再生します。

- スムーズ ストリーミング
- MPEG DASH
- HLS
- Progressive MP4


### Flash Player

#### トークンを使用した AES 暗号化 

[http://aestoken.azurewebsites.net]("http://aestoken.azurewebsites.net)

### Silverlight Players

#### 監視

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### トークンを使用した PlayReady

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### DASH Players

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### その他

次を使用して HLS URL をテストすることもできます。

- **Safari** (iOS デバイス) または
- **3ivx HLS Player** (Windows)

## ビデオ プレーヤーの開発

独自のプレーヤーの開発方法については、「[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)」をご覧ください。
 
[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png


<!--HONumber=52--> 