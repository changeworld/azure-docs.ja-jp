<properties
	pageTitle="顧客へのコンテンツ配信 | Microsoft Azure"
	description="このトピックでは、Azure Media Services を使用したコンテンツの配信の概要を説明します。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="juliako"/>


# 顧客へのコンテンツ配信
ストリーミング コンテンツまたはビデオ オン デマンド コンテンツを顧客に配信するときの目標は、さまざまなネットワーク条件にある多様なデバイスに高品質のビデオを配信することにあります。

この目標を実現するために、次の手順を実行することができます。

- ストリームをマルチビットレート (アダプティブ ビットレート) ビデオ ストリームにエンコードします。この手順では、品質とネットワークの条件が考慮されます。
- Microsoft Azure Media Services の[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)を使用して、ストリームをさまざまなプロトコルに動的に再パッケージ化します。この手順では、さまざまなデバイスでのストリーミングが考慮されます。Media Services で配信がサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、MPEG-DASH、HDS (Adobe Primetime/Access のライセンスが必要) です。

この記事では、コンテンツ配信の重要な概念の概要を説明します。

既知の問題を確認するには、「[既知の問題](media-services-deliver-content-overview.md#known-issues)」をご覧ください。

## 動的パッケージ

Media Services が提供するダイナミック パッケージを使用すると、アダプティブ ビットレートの MP4 または Smooth Streaming でエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG-DASH、HLS、Smooth Streaming、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再パッケージ化する必要がありません。ダイナミック パッケージを使用してコンテンツを配信することをお勧めします。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルやアダプティブ ビットレート Smooth Streaming ファイルにエンコードします。
- コンテンツ配信元となるストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[オンデマンド ストリーミング予約ユニットのスケールの変更方法](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

ダイナミック パッケージを使用すると、単一のストレージ形式のファイルに対して保存と支払を行います。Media Services は要求に応じて適切な応答を作成して返します。

オンデマンド ストリーミング予約ユニットを使用すると、ダイナミック パッケージ機能を使用できるだけでなく、専用の送信容量を 200 Mbps 単位で購入できます。既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。オンデマンド ストリーミング予約ユニットを購入することで、オンデマンド ストリーミングのスループットを高めることができます。

詳細については、「[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)」を参照してください。

## フィルターと動的マニフェスト

Media Services を使用すると、資産にフィルターを定義できます。これらのフィルターは、ビデオの特定セクションのみの再生や、顧客のデバイスで処理できるオーディオやビデオ演奏のサブセットの特定 (資産に関連付けられているすべての演奏ではなく) などを顧客が実行できるようにする、サーバー側のルールです。このフィルター処理は "動的マニフェスト" によって実行されます。これは、顧客が 1 つまたは複数の指定したフィルターに基づいてビデオをストリーミングするようにリクエストした場合に作成されます。

詳細については、「[フィルターと動的マニフェスト](media-services-dynamic-manifest-overview.md)」を参照してください。

## ロケーター

コンテンツのストリーミングやダウンロードに使用できる URL をユーザーに提供するには、まず、ロケーターを作成して資産を公開する必要があります。ロケーターは、資産に含まれているファイルにアクセスするためのエントリ ポイントになります。Media Services では、2 種類のロケーターがサポートされています。

- OnDemandOrigin ロケーターは、メディアのストリーミング (MPEG-DASH、HLS、Smooth Streaming など) やファイルのプログレッシブ ダウンロードに使用します。
-  Shared Access Signature (SAS) URL ロケーターは、メディア ファイルをローカル コンピューターにダウンロードする際に使用します。

"アクセス ポリシー" は、アクセス許可 (読み取り、書き込み、一覧表示など) や、クライアントが特定の資産にアクセスできる期間を定義するために使用します。一覧表示のアクセス許可 (AccessPermissions.List) は、OnDemandOrigin ロケーターを作成するときには使用しないでください。

ロケーターには有効期限があります。Azure ポータルにより、ロケーターの有効期限の日付が 100 年後に設定されます。

>[AZURE.NOTE] 2015 年 3 月より前に Azure ポータルを使用してロケーターを作成した場合は、ロケーターの有効期限が 2 年後に設定されていました。

ロケーターの有効期限を更新するには、[REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) API または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

ロケーターは、ユーザーごとのアクセス制御を管理するためのものではありません。デジタル著作権管理 (DRM) ソリューションを使用することで、個々のユーザーに異なるアクセス権限を付与できます。詳細については、「[メディアの保護](http://msdn.microsoft.com/library/azure/dn282272.aspx)」をご覧ください。

ロケーターを作成する際、Azure Storage に必要な記憶域や伝達プロセスの関係上 30 秒の遅延が生じる場合があります。


## アダプティブ ストリーミング

アダプティブ ビットレート テクノロジにより、ビデオ再生アプリケーションでネットワークの状態を判断し、複数のビットレートから選択することができます。ネットワーク通信のパフォーマンスが低下した場合、低い画質レベルでビデオの再生を継続できるように、クライアントは以前より低いビットレートを選択することができます。ネットワークの状態が改善したら、クライアントはより高画質なより高いビットレートに切り替えることができます。Azure Media Services でサポートされるアダプティブ ビットレート テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、MPEG-DASH、HDS です。

ユーザーにストリーミング URL を提供するには、最初に OnDemandOrigin ロケーターを作成する必要があります。ロケーターを作成すると、ストリーミングするコンテンツが含まれている資産の基本パスが提供されます。ただし、このコンテンツをストリーミングするためには、このパスをさらに変更する必要があります。ストリーミング マニフェスト ファイルの完全な URL を構築するには、ロケーターのパスの値とマニフェスト ファイル (filename.ism) の名前を連結する必要があります。その後、**/Manifest** と適切な形式 (必要な場合) をロケーターのパスに付加します。

>[AZURE.NOTE]SSL 接続経由でコンテンツのストリーミングもできます。そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。

SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。ストリーミング URL の基になるストリーミング エンドポイントの作成日が 2014 年 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" が含まれます。 "origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、SSL がサポートされません。URL が旧形式である場合、SSL ストリーミングに対応するためには、新しいストリーミング エンドポイントを作成してください。SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいた URL を使用する必要があります。


## ストリーミング URL の形式:

### MPEG-DASH 形式

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



### Apple HTTP Live Streaming (HLS) V4 形式

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### Apple HTTP Live Streaming (HLS) V3 形式

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### Apple HTTP Live Streaming (HLS) 形式 (オーディオ専用フィルター付き)

既定では、HLS マニフェストにはオーディオ専用トラックのみが含まれます。これは、Apple ストアのセルラー ネットワーク用の認定で必要です。この場合、クライアントの帯域幅が十分でないか、2G 接続を超えて接続されると、再生はオーディオ専用に切り替わります。このため、バッファリングされることなく引き続きコンテンツがストリーミングされますが、画像は表示されません。シナリオによっては、オーディオ専用よりもプレーヤーのバッファリングのほうが好ましい場合があります。オーディオ専用トラックを削除する場合は、**audio-only=false** を URL に追加します。

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

詳細については、[動的マニフェストの構成のサポートと HLS 出力の追加機能](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)に関するページをご覧ください。


### ストリーミング URL の形式

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest

例:

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 マニフェスト (レガシ マニフェスト)

既定では、Smooth Streaming のマニフェスト形式には、繰り返しタグ (r タグ) が含まれています。ただし、一部のプレーヤーは、r タグをサポートしていません。これらのプレーヤーを使用するクライアントは、r タグを無効にする形式を使用できます。

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=fmp4-v20)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### HDS (Adobe PrimeTime/Access ライセンスのみ)

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## プログレッシブ ダウンロード

プログレッシブ ダウンロードでは、ファイル全体がダウンロードされる前に、メディアの再生を開始できます。.ism* (ismv、isma、ismt、ismc) ファイルのプログレッシブ ダウンロードはできません。

コンテンツをプログレッシブ ダウンロードするには、OnDemandOrigin のロケーター型を使用します。次の例は、OnDemandOrigin のロケーター型に基づいた URL を示しています。

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

プログレッシブ ダウンロードで元のサービスからストリーミングするには、ストレージで暗号化された資産の暗号化を解除する必要があります。

## ダウンロード

クライアント デバイスにコンテンツをダウンロードするには、SAS ロケーターを作成する必要があります。SAS ロケーターでは、ファイルが配置されている Azure ストレージ コンテナーにアクセスできます。ダウンロード URL を作成するには、ホストと SAS 署名の間にファイル名を埋め込む必要があります。

次の例は、SAS ロケーターに基づいている URL を示しています。

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

次の考慮事項が適用されます。

- プログレッシブ ダウンロードで元のサービスからストリーミングするには、ストレージで暗号化された資産の暗号化を解除する必要があります。
- 12 時間以内に完了しないダウンロードは失敗します。

## ストリーミング エンドポイント

ストリーミング エンドポイントは、コンテンツをクライアント プレーヤー アプリケーションや、再配布のためのコンテンツ配信ネットワーク (CDN) に直接配信するストリーミング サービスを表します。ストリーミング エンドポイント サービスからの送信ストリームには、Media Services アカウントのライブ ストリームやビデオ オンデマンドの資産があります。ストリーミング予約ユニットを調整することで、ストリーミング エンドポイント サービスの容量を制御し、帯域幅の増化ニーズに対応することもできます。運用環境のアプリケーションに、1 つ以上の予約ユニットを割り当てる必要があります。詳細については、[Media Services の規模の設定方法](media-services-portal-manage-streaming-endpoints.md)に関するトピックをご覧ください。

## 既知の問題

### スムーズ ストリーミング マニフェスト バージョンへの変更

2016 年 7 月より前のサービス リリースでは、Media Encoder Standard、メディア エンコーダー プレミアム ワークフロー、または以前の Azure Media Encoder によって生成された資産は、ダイナミック パッケージを使用してストリーミングされていました。返される Smooth Streaming のマニフェストはバージョン 2.0 に対応します。バージョン 2.0 では、フラグメントの継続期間では、いわゆる繰り返し ("r") タグを使用しません。次に例を示します。

<?xml version="1.0" encoding="UTF-8"?> <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000"> <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000"> <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" /> <c t="0" d="2000" n="0" /> <c d="2000" /> <c d="2000" /> <c d="2000" /> </StreamIndex> </SmoothStreamingMedia>

2016 年 7 月のサービス リリースでは、生成される Smooth Streaming のマニフェストがバージョン 2.2 に対応しており、フラグメントの継続期間で繰り返しタグを使用できます。次に例を示します。

	<?xml version="1.0" encoding="UTF-8"?>
	<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
		<StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
			<QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
			<c t="0" d="2000" r="4" />
		</StreamIndex>
	</SmoothStreamingMedia>

一部のレガシ Smooth Streaming クライアントは繰り返しタグをサポートしいない場合があり、マニフェストを読み込むことができません。この問題を軽減するには、レガシ マニフェスト形式のパラメーター **(format=fmp4 v20)** を使用するか、繰り返しタグをサポートする最新バージョンにクライアントを更新します。詳細については、[Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20) に関するセクションをご覧ください。

## Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## 関連トピック

[ストレージ キーの展開後に Media Services ロケーターを更新する](media-services-roll-storage-access-keys.md)

<!---HONumber=AcomDC_0921_2016-->