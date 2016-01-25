<properties 
	pageTitle="Azure オンデマンド メディア エンコーダーの概要と比較" 
	description="このトピックでは、Azure オンデマンド メディア エンコーダーの概要と比較を提供します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2015"  
	ms.author="juliako"/>

#Azure オンデマンド メディア エンコーダーの概要と比較

##エンコードの概要

Azure Media Services には、クラウド内のメディア エンコーディングに使用できる複数のオプションが用意されています。

Media Services を使い始める場合、コーデックとファイル形式の違いを理解することが重要です。コーデックは圧縮/展開アルゴリズムを実装するソフトウェアで、ファイル形式は圧縮されたビデオを保持するコンテナーです。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

[動的パッケージ化](media-services-dynamic-packaging-overview.md)機能を利用するには、次の作業が必要となります。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードします (エンコードの手順は、このチュートリアルの後半で説明しています)。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[How to Scale On-Demand Streaming Reserved Unit (オンデマンド ストリーミング占有ユニットの規模変更方法)](media-services-manage-origins.md#scale_streaming_endpoints/)」をご覧ください。

Media Services は次のオンデマンド エンコーダーをサポートしてます。これらについてはこの記事で説明します。

- **メディア エンコーダー スタンダード**
- **Azure Media Encoder**
- **メディア エンコーダー プレミアム ワークフロー**

この記事には、オンデマンド メディア エンコーダーの簡単な説明と、詳しい情報を提供する記事のリンクが含まれています。このトピックではまた、エンコーダーを比較します。

既定では、1 つの Media Services アカウントにつき、同時に 1 つのアクティブなエンコーディング タスクを実行できます。エンコード ユニットを予約して、複数のエンコード タスク (購入したエンコード予約ユニットごとに 1 つ) を同時に実行できます。詳細については、「[エンコード ユニットの拡大/縮小](media-services-portal-encoding-units.md)」を参照してください。

##メディア エンコーダー スタンダード

###概要

メディア エンコーダー スタンダードを使用することをお勧めします。ただし、これは現在、Azure クラシック ポータルでは公開されていません。

Azure Media Encoder と比較すると、このエンコーダーではサポートされる入力または出力の形式とコーデックの数が多くなっています。他には次のような利点があります。

- 入力ファイルの作成方法に対する許容範囲が広い
- Azure Media Encoder より H.264 コーデックの品質が優れている
- より新しく、より柔軟なパイプラインの上に構築される
- より堅牢で回復力が高い

###使用方法

[メディア エンコーダー スタンダードを使用したエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)

###形式

[形式とコーデック](media-services-media-encoder-standard-formats.md)

###プリセット

Media Encoder Standard は、[ここ](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)で説明されているエンコーダーのプリセット文字列のいずれかを使用して構成されます。

###入力メタデータと出力メタデータ

エンコーダーの入力メタデータの説明は[ここ](http://msdn.microsoft.com/library/azure/dn783120.aspx)にあります。

エンコーダーの出力メタデータの説明は[ここ](http://msdn.microsoft.com/library/azure/dn783217.aspx)にあります。

###サムネイルを生成する

詳細については、「[Media Encoder Standard を使用してサムネイルを生成する方法](media-services-custom-mes-presets-with-dotnet.md#thumbnails)」を参照してください。

###動画をトリミングする (クリッピング)

詳細については、「[Media Encoder Standard を使用して動画をトリミングする方法](media-services-custom-mes-presets-with-dotnet.md#trim_video)」を参照してください。

###オーバーレイを作成する

詳細については、「[Media Encoder Standard を使用してオーバーレイを作成する方法](media-services-custom-mes-presets-with-dotnet.md#overlay)」を参照してください。

###関連項目

[Media Services ブログ](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Azure Media Encoder

###概要

Azure Media Encoder は Media Services がサポートするエンコーダーの 1 つです。2015 年 7 月以降は、[Media Encoder Standard](media-services-encode-asset.md#media_encoder_standard) を使用することをお勧めします。

###使用方法

[Azure Media Encoder を使用したエンコード方法](media-services-dotnet-encode-asset.md)

###形式

[形式とコーデック](media-services-azure-media-encoder-formats.md)

###プリセット

Azure Media Encoder は、[ここ](https://msdn.microsoft.com/library/azure/dn619392.aspx)で説明されているエンコーダーのプリセットのいずれかを使用して構成されています。実際の Azure Media Encoder のプリセット ファイルは、[ここ](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)で入手できます。

###入力メタデータと出力メタデータ

エンコーダーの入力メタデータの説明は[ここ](http://msdn.microsoft.com/library/azure/dn783120.aspx)にあります。

エンコーダーの出力メタデータの説明は[ここ](http://msdn.microsoft.com/library/azure/dn783217.aspx)にあります。

###サムネイル

[サムネイルの作成](https://msdn.microsoft.com/library/hh973624.aspx)

###オーディオまたはビデオのオーバーレイ

[オーバーレイの作成](media-services-azure-media-customize-ame-presets.md#creating-overlays)

###命名規則

[出力ファイル名を変更する方法](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###関連項目

[ドルビー デジタル プラスを使ったメディアのエンコード](media-services-encode-with-dolby-digital-plus.md)

##メディア エンコーダー プレミアム ワークフロー

###概要

[Azure Media Services の Premium Encoding の紹介 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

###使用方法

メディア エンコーダー プレミアム ワークフロー は複雑なワークフローを使用して構成されています。Workflow ファイルは、[Workflow Designer](media-services-workflow-designer.md) ツールを使用して作成して更新できます。

[Azure Media Services で Premium Encoding を使用する方法 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

##<a id="compare_encoders"></a>エンコーダーの比較

###<a id="billing"></a>各エンコーダーで使用される課金メーター

メディア プロセッサ名|適用される価格|メモ
---|---|---
**メディア エンコーダー スタンダード** |エンコーダー|Encoding タスクは、[こちら][1]の「エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。
**Azure Media Encoder** |エンコーダー|Encoding タスクは、[こちら][1]の「エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。
**メディア エンコーダー プレミアム ワークフロー** |プレミアム エンコーダー|Encoding タスクは、[こちら][1]の「PREMIUM エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。


このセクションでは、**メディア エンコーダー スタンダード**、**Azure Media Encoder**、**メディア エンコーダー プレミアム ワークフロー**のエンコード機能を比較します。


###入力コンテナー/ファイル形式

入力コンテナー/ファイル形式|メディア エンコーダー スタンダード|Azure Media Encoder|メディア エンコーダー プレミアム ワークフロー
---|---|---|---
Adobe® Flash® F4V |あり|なし |あり
MXF/SMPTE 377M |あり|制限あり|あり
GXF |あり|なし |あり
MPEG-2 トランスポート ストリーム |あり|あり |あり
MPEG-2 プログラム ストリーム |あり|あり |あり
MPEG-4/MP4 |あり|あり |あり
Windows Media/ASF |あり|あり |あり
AVI (非圧縮 8-bit/10-bit)|あり|あり |あり
3GPP/3GPP2 |あり|はい |いいえ
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|あり|はい|いいえ
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|あり|いいえ|いいえ
Matroska/WebM |あり|いいえ|いいえ
QuickTime (.mov) |あり|いいえ|いいえ

###入力ビデオ コーデック

入力ビデオ コーデック|メディア エンコーダー スタンダード|Azure Media Encoder|メディア エンコーダー プレミアム ワークフロー
---|---|---|---
AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 および 4:2:2|8 ビット 4:2:0 のみ|あり
Avid DNxHD (MXF) |あり|なし|あり
DVCPro/DVCProHD (MXF) |あり|なし|あり
JPEG2000 |あり|なし|あり
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|最大 422 プロファイル|最大 422 プロファイル|あり
MPEG-1 |あり|あり|あり
Windows Media ビデオ/VC-1 |あり|あり|あり
Canopus HQ/HQX |いいえ|あり|いいえ
MPEG-4 Part 2 |あり|いいえ|いいえ
[Theora](https://en.wikipedia.org/wiki/Theora) |あり|いいえ|いいえ
Apple ProRes 422 |あり|いいえ|いいえ
Apple ProRes 422 LT |あり|いいえ|いいえ
Apple ProRes 422 HQ |あり|いいえ|いいえ
Apple ProRes プロキシ|あり|いいえ|いいえ
Apple ProRes 4444 |あり|いいえ|いいえ
Apple ProRes 4444 XQ |あり|いいえ|いいえ

###入力オーディオ コーデック

入力オーディオ コーデック|メディア エンコーダー スタンダード|Azure Media Encoder|メディア エンコーダー プレミアム ワークフロー
---|---|---|---
AES (SMPTE 331M および 302M、AES3-2003) |いいえ|いいえ|あり
Dolby® E |いいえ|いいえ|あり
Dolby® Digital (AC3) |いいえ|あり|あり
Dolby® Digital Plus (E-AC3) |いいえ|いいえ|あり
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|あり|あり|あり
MPEG Layer 2|あり|あり|あり
MP3 (MPEG-1 Audio Layer 3)|あり|あり|あり
Windows Media オーディオ|あり|あり|あり
WAV/PCM|あり|あり|あり
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|あり|いいえ|いいえ
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |あり|いいえ|いいえ
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|あり|いいえ|いいえ


###出力コンテナー/ファイル形式

出力コンテナー/ファイル形式|メディア エンコーダー スタンダード|Azure Media Encoder|メディア エンコーダー プレミアム ワークフロー
---|---|---|---
Adobe® Flash® F4V|いいえ|いいえ|あり
MXF (OP1a、XDCAM および AS02)|いいえ|いいえ|あり
DPP (AS11 を含む)|いいえ|いいえ|あり
GXF|いいえ|いいえ|あり
MPEG-4/MP4|あり|あり|あり
MPEG TS|あり|なし|あり
Windows Media/ASF|いいえ|あり|あり
AVI (非圧縮 8-bit/10-bit)|いいえ|いいえ|あり
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|いいえ|あり|あり

###出力ビデオ コーデック

出力ビデオ コーデック|メディア エンコーダー スタンダード|Azure Media Encoder|メディア エンコーダー プレミアム ワークフロー
---|---|---|---
AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)|8 ビット 4:2:0 のみ|8 ビット 4:2:0 のみ (最大 1080p)|あり
Avid DNxHD (MXF)|いいえ|いいえ|あり
DVCPro/DVCProHD (MXF)|いいえ|いいえ|あり
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|いいえ|いいえ|あり
MPEG-1|いいえ|いいえ|あり
Windows Media ビデオ/VC-1|いいえ|あり|あり
JPEG サムネール作成|いいえ|あり|あり

###出力オーディオ コーデック

出力オーディオ コーデック|メディア エンコーダー スタンダード|Azure Media Encoder|メディア エンコーダー プレミアム ワークフロー
---|---|---|---
AES (SMPTE 331M および 302M、AES3-2003)|いいえ|いいえ|あり
Dolby® Digital (AC3)|いいえ|あり|あり
Dolby® Digital Plus (E-AC3) 最大 7.1|いいえ|最大 5.1|あり
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|あり|あり|あり
MPEG Layer 2|いいえ|いいえ|あり
MP3 (MPEG-1 Audio Layer 3)|いいえ|いいえ|あり
Windows Media オーディオ|いいえ|あり|あり


##エラー コード  

次の表は、エンコード タスクの実行中にエラーが発生した場合に返される可能性があるエラー コードの一覧です。エラーの詳細を取得するには、.NET コードの場合は [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) クラスを、REST コードの場合は [ErrorDetails](https://msdn.microsoft.com/library/jj853026.aspx) REST API を使用します。

ErrorDetail.Code|考えられる原因
-----|-----------------------
Unknown| タスクの実行中に発生する不明なエラー
ErrorDownloadingInputAssetMalformedContent|無効なファイル名、サイズ 0 のファイル、不正なファイル形式など、入力資産のダウンロード中に発生するエラーのカテゴリ
ErrorDownloadingInputAssetServiceFailure|ダウンロード中のネットワーク エラーやストレージ エラーなど、サービス側での問題に関するエラーのカテゴリ
ErrorParsingConfiguration|構成が有効なシステム プリセットでない、構成に無効な XML が含まれているなど、タスク <see cref="MediaTask.PrivateData"/> (構成) が有効でない場合に発生するエラーのカテゴリ
ErrorExecutingTaskMalformedContent|入力メディア ファイル内の問題が障害の原因となり、タスクの実行中に発生するエラーのカテゴリ
ErrorExecutingTaskUnsupportedFormat|メディア形式がサポートされていない、または構成と一致しないなど、提供されたファイルをメディア プロセッサが処理できない場合に発生するエラーのカテゴリ(映像のみの資産から音声のみの出力を生成しようとした場合など)
ErrorProcessingTask|コンテンツとは無関係に、メディア プロセッサでタスクの処理中に発生するその他のエラーのカテゴリ
ErrorUploadingOutputAsset|出力資産のアップロード中に発生するエラーのカテゴリ
ErrorCancelingTask|タスクをキャンセルしようとして失敗したときに発生するエラーのカテゴリ
TransientError|一時的な問題に関するエラーのカテゴリ (Azure Storage の一時的なネットワークの問題など)


**Media Services** チームのサポートを受けるには、[サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を開いてください。



##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##関連記事:

- [Media Encoder Standard のプリセットをカスタマイズし、高度なエンコード タスクを実行する](media-services-custom-mes-presets-with-dotnet.md)
- [クォータと制限](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0114_2016-->