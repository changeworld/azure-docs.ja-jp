<properties 
	pageTitle="castLabs を使用して Azure Media Services に DRM ライセンスを配信する" 
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
	ms.date="08/11/2015"  
	ms.author="juliako"/>


#castLabs を使用して Azure Media Services に DRM ライセンスを配信する

##概要

この記事では、PlayReady と Widevine DRM の両方を使用して AMS で動的に暗号化されたストリームを、Azure Media Services (AMS) を使用して配信する方法について説明します。PlayReady ライセンスは Media Services PlayReady サーバーから取得し、Widevine ライセンスは **castLabs** ライセンス サーバーから取得します。

次の図は、Azure Media Services と castLabs の統合アーキテクチャを大まかに示しています。

![統合](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##一般的なシステムの設定

- メディア コンテンツは AMS に格納されます。
- コンテンツ キーのキー ID は castLabs と AMS の両方に格納されます。
- castLabs と AMS はどちらもトークン認証が組み込まれています。次のセクションでは、認証トークンについて説明します。 
- クライアントからビデオのストリーミング要求があると、そのコンテンツが **Common Encryption** (CENC) によって動的に暗号化されたうえで、AMS によって Smooth Streaming や DASH として動的にパッケージ化されます。また、HLS ストリーミング プロトコル用に PlayReady M2TS エレメンタリ ストリームの暗号化も行います。
- PlayReady ライセンスは AMS ライセンス サーバーから取得し、Widevine ライセンスは castLabs ライセンス サーバーから取得します。 
- Media Player ではクライアントのプラットフォームの機能に基づいて、フェッチするライセンスを自動的に決定します。 

##ライセンスを取得するための認証トークンの生成

CastLabs と AMS の両方でライセンスの承認に使用する JWT (JSON Web Token) トークン形式をサポートしています。

###AMS での JWT トークン 

次の表では AMS での JWT トークンについて説明しています。

Issuer|選択したからセキュリティ トークン サービス (STS) から取得した発行者の文字列
---|---
対象ユーザー|使用される STS から取得した対象文字列
Claims|一連の要求
NotBefore|トークンの有効性開始
Expires|トークンの有効性終了
SigningCredentials|PlayReady ライセンス サーバー、castLabs ライセンス サーバー、STS の間でキーが共有されている場合、対称キーまたは非対称キーのどちらかになります。

###CastLabs での JWT トークン

次の表では castLabs での JWT トークンについて説明してます。

名前|説明
---|---
optData|ユーザーに関する情報が含まれる JSON 文字列 
crt|アセット、ライセンス情報、再生権に関する情報が含まれる JSON 文字列
iat|エポックの現在の日時
jti|このトークンの一意の識別子 (どのトークンも castLabs システムで 1 度しか使用できません)

##サンプル ソリューションの設定 

[サンプル ソリューション](https://github.com/AzureMediaServicesSamples/CastlabsIntegration)は次の 2 つのプロジェクトで構成されます。

-	PlayReady と Widevine の両方について、既に取り込まれたアセットへの DRM 制限の設定に使用できるコンソール アプリケーション
-	トークンを渡す Web アプリケーション (非常に簡略化された STS として表示される)


コンソール アプリケーションを使用するには、次の手順に従います。

1.	app.config を変更して、AMS 資格情報、castLabs 資格情報、STS 構成、共有キーをセットアップします。
2.	AMS にアセットをアップロードします。
3.	アップロードされたアセットから UUID を取得し、Program.cs ファイルで行 32 を変更します。

		 var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.	AssetId を使用して castLabs システムでアセットに名前を付けます (Program.cs ファイルでは行 44)。

	**castLabs** に AssetId を設定する必要があります。文字列には一意の英数字を使用してください。

5.	プログラムを実行します。


Web アプリケーション (STS) を使用するには、次の手順に従います。

1.	web.config を変更して、castlabs の商業 ID、STS 構成、共有キーを設定します。
2.	Azure Websites にデプロイします。
3.	Web サイトに移動します。

##ビデオを再生する

共通暗号化 (PlayReady) で暗号化されたビデオを再生するために、[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用できます。コンソール アプリケーションを実行すると、コンテンツ キー ID とマニフェスト URL がエコーされます。

1.	新しいタブを開き、次の STS を起動します。http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]
2.	[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) に移動します。
3.	ストリーミング URL に貼り付けます。
4.	**[詳細オプション]** チェックボックスをクリックします。
5.	**[保護]** ドロップダウンから PlayReady を選択します。
6.	STS から取得したトークンを [トークン] テキストボックスに貼り付けます。
7.	プレーヤーを更新します。
8.	ビデオが再生されるはずです。

castLabs プレイヤーを使用して Chrome の HTML5 で保護されたビデオを再生する場合は、プレイヤーへのアクセス権について yanmf@microsoft.com にお問い合わせください。アクセス権がある場合、次の 2 点にご注意ください。

1.	castLabs プレーヤーは MPEG DASH のマニフェスト ファイルにアクセスする必要があるため、(format=mpd-time-csf) をマニフェスト ファイルに追加して、既定の Smooth Streaming のファイルではなく MPEG DASH マニフェスト ファイルを取得してください。

2.	castLab ライセンス サーバーの場合は、トークンの前に “Bearer=” プレフィックスを置く必要はありません。トークンを送信する前にプレフィックスを削除してください。

 

<!---HONumber=August15_HO7-->