<properties 
	pageTitle="顧客へのコンテンツの配信に関する概要" 
	description="このトピックでは、Azure Media Services を使用したコンテンツの配信に関する概要を説明します。" 
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


#顧客へのコンテンツの配信に関する概要

##概要

Media Services を操作する際の一般的なシナリオには次のようなものがあります。

1. 入力ファイル (中間ファイル) をアセットにアップロードする。(例: H.264、MP4、WMV)。
1. アダプティブ ビットレート mp4 セットにそのアセットをエンコードする。
1. アセットを公開する。 
2. [動的パッケージ](http://msdn.microsoft.com/library/azure/jj889436.aspx)を使用して次のいずれかの形式でクライアントにコンテンツを配信する: MPEG DASH、Apple HLS、Smooth Streaming。 

このトピックでは、主な[概念](media-services-deliver-content.md#concepts) の概要と、コンテンツの配信[作業](media-services-deliver-content.md#tasks).の実行方法を示すトピックへのリンクを紹介します。

##<a id="concepts"></a>概念

メディアを配信する際に知っておくと役立つ用語や概念を以下に示します。

###ロケーター

コンテンツのストリーミングやダウンロードに使用できる URL をユーザーに提供するには、まずロケーターを作成してアセットを「発行」する必要があります。ロケーターは、アセットに含まれているファイルにアクセスするためのエントリ ポイントになります。Media Services では、2 種類のロケーターがサポートされています。 

- **OnDemandOrigin** ロケーターは、メディアのストリーム (MPEG DASH、HLS、スムーズ ストリーミングなど) やファイルのプログレッシブ ダウンロードに使用します。
-  **SAS** (アクセス署名) URL ロケーターは、メディア ファイルをローカル コンピューターにダウンロードする際に使用します。

**アクセス ポリシー**は、アクセス許可 (読み取り、書き込み、一覧など) や、クライアントが特定のアセットにアクセスできる期間を定義する際に使用します。一覧表示のアクセス許可 (AccessPermissions.List) は、OrDemandOrigin ロケーターを作成するときには使用しないでください。

ロケーターには、有効期限があります。ポータルを使用してアセットを発行する場合、100 年の有効期限の日付を含むロケーターが作成されます。 

>[AZURE.NOTE] 2015 年 3 月以前にポータルを使用してロケーターを作成した場合は、ロケーターの有効期限は 2 年間になります。  

ロケーターの有効期限を更新するには、[REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) API か [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。SAS ロケーターの有効期限を更新する場合は URL が変更されることにご注意ください。 
 
ロケーターは、ユーザーごとのアクセス制御を管理するためのものではありません。個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。詳細については、「[メディアの保護](http://msdn.microsoft.com/library/azure/dn282272.aspx)」をご覧ください。

ロケーターを作成する際、Azure ストレージに必要な記憶域や伝達プロセスの関係上 30 秒の遅延が生じる場合があります。


###アダプティブ ストリーミング 

アダプティブ ビットレート テクノロジでは、ビデオ再生アプリケージョンでネットワークの状態を特定し、複数のビットレートの中から選択できます。ネットワーク通信のパフォーマンスが低下した場合、クライアントはそれより低いビットレートを選択して、より低い画質レベルでビデオの再生をプレーヤーで継続できます。ネットワークの状態が改善したら、顧客はより高画質なより高いビットレートに切り替えることができます。Azure Media Services は、次のアダプティブ ビットレート テクノロジをサポートします。HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS。

ユーザーにストリーミング URL を提供するには、最初に OnDemandOrigin ロケーターを作成する必要があります。ロケーターを作成すると、ストリーミングするコンテンツが含まれているアセットの基本パスが提供されます。ただし、このコンテンツをストリーミングするためには、このパスをさらに変更する必要があります。ストリーミング マニフェスト ファイルの完全な URL を構築するには、ロケーターの Path の値とマニフェスト (filename.ism) ファイルの名前を連結する必要があります。その後、/Manifest と適切な形式 (必要な場合) をロケーターのパスに付加します。 

SSL 接続経由でコンテンツのストリーミングもできます。そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。 

SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。ストリーミング URL の基になるストリーミング エンドポイントの作成日が 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" (新形式) が含まれています。"origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、SSL がサポートされません。URL が旧形式である場合、SSL ストリーミングに対応するためには、新しいストリーミング エンドポイントを作成してください。SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいて作成された URL を使用する必要があります。 


####ストリーミング URL の形式:

**ストリーミング URL の形式**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

例:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


**MPEG DASH 形式**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

例

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Apple HTTP Live Streaming (HLS) V4 形式**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Apple HTTP Live Streaming (HLS) V3 形式**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**HDS (Adobe PrimeTime/Access ライセンスのみ)**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


###動的パッケージ

メディア サービスには動的パッケージ化機能があり、アダプティブ ビットレート MP4 やスムーズ ストリーミングでエンコードされたコンテンツを、メディア サービスでサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信できます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。 

動的パッケージ化機能を利用するには、次の作業が必要となります。

- 中間 (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルやアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードする。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[メディア サービスの規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints/)」 をご覧ください。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。メディア サービスがクライアントからの要求に応じて適切な応答を構築して返します。 

動的パッケージ化機能を使用できることに加え、オンデマンド ストリーミング占有ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できます。既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。オンデマンド ストリーミングのスループットを高めるために、オンデマンド ストリーミング占有ユニットの購入をお勧めします。

###プログレッシブ ダウンロード 

プログレッシブ ダウンロードでは、ファイル全体がダウンロードされる前に、メディアの再生を開始することができます。.ism* (ismv, isma, ismt, ismc) ファイルのプログレッシブ ダウンロードはできません。 

コンテンツをプログレッシブ ダウンロードするには、OnDemandOrigin のロケーター型を使用します。次の例は、OnDemandOrigin のロケーター型に基づいた URL を示しています。

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

次の考慮事項が適用されます。

- プログレッシブ ダウンロードで元のサービスからストリームするには、ストレージで暗号化されたアセットの暗号化を解除する必要があります。


###ダウンロード

クライアント デバイスにコンテンツをダウンロードするには、SAS ロケーターを作成する必要があります。SAS ロケーターでは、ファイルが配置されている Azure ストレージ コンテナーにアクセスできます。ダウンロード URL を作成するには、ホストと SAS 署名の間にファイル名を埋め込む必要があります。 

次の例は、SAS ロケーターに基づいている URL を示しています。

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

次の考慮事項が適用されます。

- プログレッシブ ダウンロードで元のサービスからストリームするには、ストレージで暗号化されたアセットの暗号化を解除する必要があります。
- 12 時間以内に完了しないダウンロードは失敗します。



###ストリーミング エンドポイント

**ストリーミング エンドポイント**は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のためのコンテンツ配信ネットワーク (CDN) に直接配信するストリーミング サービスを表します。ストリーミング エンドポイント サービスからの送信ストリームには、Media Services アカウントのライブ ストリームやオンデマンド ビデオ アセットがあります。さらに、ストリーミング占有ユニットを調整することで、ストリーミング エンドポイント サービスの容量を制御し、帯域幅の増化ニーズに対応することができます。実稼働環境のアプリケーションに、1 つ以上の予約済みユニットを割り当てる必要があります。詳細については、「[メディア サービスの規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints)」をご覧ください。

##<a id="tasks"></a>アセット配信に関連する作業

###アセット配信ポリシーの構成

**.NET** または **REST API** を使用してアセット配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

###アセットの公開

**Azure 管理ポータル** または **.NET** を使用してアセットを公開します (ロケータを作成して)。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##関連トピック

[ストレージ キーの展開後に Media Services ロケーターを更新する](media-services-roll-storage-access-keys.md)

<!--HONumber=52-->