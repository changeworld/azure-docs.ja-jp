<properties 
	pageTitle="Azure クラシック ポータルを使用して Azure Media Services でメディア コンテンツを管理する方法" 
	description="Azure Media Services でメディア コンテンツを管理する方法について説明します。ここで取り上げる内容は、アップロード、インデックスの作成、エンコード、暗号化、および発行についてです。" 
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
	ms.date="02/04/2016"   
	ms.author="juliako"/>


# Azure クラシック ポータルを使用した Azure Media Services でのコンテンツの管理


このトピックでは、Azure クラシック ポータルを使用して、Media Services アカウントのメディア コンテンツを管理する方法について説明します。

このトピックでは、ポータルから直接、次のコンテンツの操作を実行する方法を説明します。

- コンテンツ情報 (発行状態、発行 URL、サイズ、日付、最終更新日時、資産の暗号化の有無など) も確認できます。
- 新しいコンテンツのアップロード
- コンテンツのインデックス作成
- コンテンツのエンコード
- 暗号化
- コンテンツの発行および発行の取り消し
- コンテンツの再生


##<a id="upload"></a>方法: コンテンツをアップロードする


[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]


1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)で、**[Media Services]** をクリックし、Media Services アカウント名をクリックします。
2. [コンテンツ] ページを選択します。 
3. ページまたはポータルの下部にある **[アップロード]** をクリックします。 
4. **[コンテンツのアップロード]** ダイアログで、目的の資産ファイルを見つけます。ファイルをクリックして、**[開く]** をクリックするか、**Enter** キーを押します。

	![UploadContentDialog][uploadcontent]

5. [コンテンツのアップロード] ダイアログで、チェック ボタンをクリックして、ファイル名とコンテンツ名をそのまま使用します。
6. アップロードが開始され、進捗状況はポータルの下部で確認できます。  

	![JobStatus][status]

アップロードが完了すると、コンテンツ一覧に新しい資産が表示されます。通常、名前の末尾に "**-Source**" が付加され、エンコード タスクのソース コンテンツとして新しいコンテンツを見つけやすくなっています。

![ContentPage][contentpage]

アップロード処理が終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** をクリックします。これにより資産のファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。

##<a id="index"></a>方法: コンテンツのインデックスを作成する

> [AZURE.SELECTOR]
- [.NET](media-services-index-content.md)
- [Portal](media-services-manage-content.md#index)

Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。コンテンツのインデックスは、下記の手順に従って、Azure クラシック ポータルを使用して作成できます。ただし、対象となるファイルの選択やインデックス作成ジョブの動作を細かく制御する必要がある場合は、Media Services SDK for .NET または REST API を使用してください。詳細については、「[Azure Media Indexer によるメディア ファイルのインデックス作成](media-services-index-content.md)」を参照してください。

Azure クラシック ポータルを使用してコンテンツのインデックスを作成するには、次の手順に従います。

1. インデックスの作成対象となるファイルを選択します。そのファイル タイプのインデックスを作成できる場合、[コンテンツ] ページの一番下にある [プロセス] ボタンが有効になります。
1. [プロセス] ボタンをクリックします。
2. **[プロセス]** ダイアログで **[Azure Media Indexer]** プロセッサを選択します。
3. 次に、**[プロセス]** ダイアログで、入力メディア ファイルのタイトルと**説明**を詳しく入力します。

![Process][process]

##<a id="encode"></a>方法: コンテンツをエンコードする

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST](media-services-rest-encode-asset.md)
- [Portal](media-services-manage-content.md#encode)

インターネット経由でデジタル ビデオを配信するには、メディアを圧縮する必要があります。Media Services には、コンテンツのエンコード方法 (使用するコーデック、ファイル形式、解像度、ビットレートなど) を指定できる Media Encoder が用意されています。

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。アダプティブ ビットレート ストリーミングでは、現在のネットワーク帯域幅、CPU 使用率などの条件に基づいてビデオが表示されるため、高低のビットレート ストリームの切り替えをクライアント側で行うことができます。Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードします (エンコードの手順は、このチュートリアルの後半で説明しています)。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[How to Scale On-Demand Streaming Reserved Unit (オンデマンド ストリーミング予約ユニットの規模変更方法)](media-services-manage-origins.md#scale_streaming_endpoints/)」をご覧ください。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

動的パッケージ化機能を使用できることに加え、オンデマンド ストリーミング予約ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できます。既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。オンデマンド ストリーミングのスループットを高めるために、オンデマンド ストリーミング予約ユニットの購入をお勧めします。

ここでは、Azure クラシック ポータルを使用して、Azure Media Encoder でコンテンツをエンコードする手順について説明します。

1.  エンコードの対象となるファイルを選択します。

  そのファイル タイプのエンコードがサポートされている場合、[コンテンツ] ページの一番下にある [プロセス] ボタンが有効になります。4.**[プロセス]** ダイアログで **[Media Encoder Standard]** プロセッサを選択します。5.いずれかの**エンコーディング構成**を選択します。

![Process2][process2]


[Media Encoder Standard 用のタスク プリセット文字列](https://msdn.microsoft.com/library/mt269960)に関するトピックでは、各プリセットの意味を説明しています。

5. 次に、わかりやすい出力コンテンツ名を入力するか、既定値をそのまま使用します。チェック ボタンをクリックすると、エンコード処理が開始され、進捗状況はポータルの下部で確認できます。
6. [OK] をクリックします。

エンコードが完了すると、[コンテンツ] ページにエンコード済みのファイルが表示されます。

エンコード ジョブの進行状況を確認するには、**[ジョブ]** ページに切り替えます。

エンコードが終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** をクリックします。これにより出力資産のファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。

##<a id="encrypt"></a>方法: コンテンツを暗号化する


Media Services で AES キーまたは PlayReady DRM を使用して資産を暗号化するには、次の操作を実行します。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレートスムーズ ストリーミング ファイルにエンコードします (エンコーディングの手順は [エンコード](#encode) セッションで説明しています)
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[オンデマンド ストリーミング予約ユニットのスケールの変更方法](media-services-manage-origins.md#scale_streaming_endpoints/)」をご覧ください。
- 「既定の AES クリア キー サービス ポリシー」または「既定 PlayReady ライセンス サービス ポリシー」を構成します。詳細については、「[コンテンツ キー承認ポリシーを構成する](media-services-portal-configure-content-key-auth-policy.md)」をご覧ください。  


	暗号化を有効にする準備ができたら、**[コンテンツ]** ページの下部にある **[暗号化]** ボタンを押します。

	![Encrypt][encrypt]

	暗号化を有効にすると、プレーヤーからストリームが要求されるたびに、Media Services は指定されたキーを使用して、AES または PlayReady 暗号化でコンテンツを動的に暗号化します。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

関連トピック:

- [PlayReady DRM による保護](media-services-rest-deliver-streaming-content.md)
- [AES-128 キーによる保護](media-services-protect-with-aes128.md)

##<a id="publish"></a>方法: コンテンツを発行する

> [AZURE.SELECTOR]
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)
- [Portal](media-services-manage-content.md#publish)

###概要

ストリーミングかダウンロードに使用できる URL を提供するには、まず、ロケーターを作成して資産を "発行" する必要があります。資産に含まれているファイルには、ロケーターを通じてアクセスできます。Media Services では、2 種類のロケーターがサポートされています。OnDemandOrigin ロケーターはメディアのストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなど) に、Access Signature (SAS) ロケーターはメディア ファイルのダウンロードに使用します。

Azure クラシック ポータルを使用して資産を発行すると、ロケーターが作成され、OnDemantOrigin ベースの URL (資産に .ism ファイルが含まれている場合) または SAS URL が提供されます。

SAS URL には次の形式があります。

	{blob container name}/{asset name}/{file name}/{SAS signature}

ストリーミング URL には次の形式があり、スムーズ ストリーミング資産の再生に使用できます。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS ストリーミング URL を作成するには、(format=m3u8-aapl) を URL に追加します。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH ストリーミング URL を作成するには、(format=mpd-time-csf) を URL に追加します。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


ロケーターには有効期限があります。ポータルを使用して資産を発行すると、有効期限が 100 年のロケーターが作成されます。

>[AZURE.NOTE] 2015 年 3 月以前にポータルを使用してロケーターを作成した場合、有効期限が 2 年のロケーターが作成されています。

ロケーターの有効期限を更新するには、[REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

###Publish

ポータルを使用して資産を発行するには、次の操作を行います。

1. 資産を選択します。 
2. 次に発行ボタンをクリックします。 
	
 ![PublishedContent][publishedcontent]


## 方法: ポータルでコンテンツを再生する

**Azure クラシック ポータル**には、ビデオのテストに使用できるコンテンツ プレーヤーが用意されています。

目的のビデオをクリックし、ポータルの下部にある **[再生]** をクリックします。
 
いくつかの考慮事項が適用されます。

- ビデオが発行されたことを確認します。
- **Media Services コンテンツ プレーヤー**を既定のストリーミング エンドポイントから再生します。既定以外のストリーミング エンドポイントから再生する場合は、別のプレーヤーを使用します([Azure Media Services プレーヤーなど](http://amsplayer.azurewebsites.net/azuremediaplayer.html))。

![AMSPlayer][AMSPlayer]

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-manage-content/media-services-portal-player.png

<!---HONumber=AcomDC_0211_2016-->