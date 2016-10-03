<properties 
	pageTitle="Azure オンデマンド メディア エンコーダーの概要と比較 | Microsoft Azure" 
	description="このトピックでは、Azure オンデマンド メディア エンコーダーの概要を説明し、エンコーダーを比較します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
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

#Azure オンデマンド メディア エンコーダーの概要と比較

##エンコードの概要

Azure Media Services には、クラウド内のメディア エンコーディングに使用できる複数のオプションが用意されています。

Media Services を使い始める場合、コーデックとファイル形式の違いを理解することが重要です。コーデックは圧縮/展開アルゴリズムを実装するソフトウェアで、ファイル形式は圧縮されたビデオを保持するコンテナーです。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

[動的パッケージ化](media-services-dynamic-packaging-overview.md)機能を利用するには、次の作業が必要となります。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードします (エンコードの手順は、このチュートリアルの後半で説明しています)。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[How to Scale On-Demand Streaming Reserved Unit (オンデマンド ストリーミング占有ユニットの規模変更方法)](media-services-portal-manage-streaming-endpoints.md)」をご覧ください。

Media Services は次のオンデマンド エンコーダーをサポートしてます。これらについてはこの記事で説明します。

- [メディア エンコーダー スタンダード](media-services-encode-asset.md#media-encoder-standard)
- [メディア エンコーダー Premium ワークフロー](media-services-encode-asset.md#media-encoder-premium-workflow)

この記事には、オンデマンド メディア エンコーダーの簡単な説明と、詳しい情報を提供する記事のリンクが含まれています。また、このトピックではエンコーダーを比較します。

既定では、1 つの Media Services アカウントにつき、同時に 1 つのアクティブなエンコーディング タスクを実行できます。エンコード ユニットを予約して、複数のエンコード タスク (購入したエンコード予約ユニットごとに 1 つ) を同時に実行できます。詳細については、「[エンコード ユニットの拡大/縮小](media-services-scale-media-processing-overview.md)」を参照してください。

##メディア エンコーダー スタンダード

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

詳細については、「[サムネイルを生成する](media-services-custom-mes-presets-with-dotnet.md#thumbnails)」をご覧ください。

###動画をトリミングする (クリッピング)

詳細については、「[動画をトリミングする (クリッピング)](media-services-custom-mes-presets-with-dotnet.md#trim_video)」をご覧ください。

###オーバーレイを作成する

詳細については、「[オーバーレイを作成する](media-services-custom-mes-presets-with-dotnet.md#overlay)」をご覧ください。

###関連項目

[Media Services ブログ](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##メディア エンコーダー Premium ワークフロー

###Overview

[Azure Media Services の Premium Encoding の紹介 (ブログの投稿)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###使用方法

メディア エンコーダー プレミアム ワークフロー は複雑なワークフローを使用して構成されています。Workflow ファイルは、[Workflow Designer](media-services-workflow-designer.md) ツールを使用して作成して更新できます。

[Azure Media Services で Premium Encoding を使用する方法 (ブログの投稿)](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###既知の問題

入力ビデオにクローズド キャプションが含まれない場合でも、出力アセットには空の TTML ファイルが含まれます。


##<a id="compare_encoders"></a>エンコーダーの比較

###<a id="billing"></a>各エンコーダーで使用される課金メーター

メディア プロセッサ名|適用される価格|メモ
---|---|---
**メディア エンコーダー スタンダード** |エンコーダー|Encoding タスクは、[こちら][1]の「エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。
**メディア エンコーダー プレミアム ワークフロー** |プレミアム エンコーダー|Encoding タスクは、[こちら][1]の「PREMIUM エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。


このセクションでは、**Media Encoder Standard** と **Media Encoder Premium Workflow** のエンコード機能を比較します。


###入力コンテナー/ファイル形式

入力コンテナー/ファイル形式|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー
---|---|---
Adobe® Flash® F4V |はい|はい
MXF/SMPTE 377M |はい|はい
GXF |はい|はい
MPEG-2 トランスポート ストリーム |はい|はい
MPEG-2 プログラム ストリーム |はい|はい
MPEG-4/MP4 |はい|はい
Windows Media/ASF |はい|はい
AVI (非圧縮 8-bit/10-bit)|はい|はい
3GPP/3GPP2 |はい|なし
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|はい|なし
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|はい|なし
Matroska/WebM |はい|なし
QuickTime (.mov) |はい|なし

###入力ビデオ コーデック

入力ビデオ コーデック|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー
---|---|---
AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 および 4:2:2|はい
Avid DNxHD (MXF) |はい|はい
DVCPro/DVCProHD (MXF) |はい|はい
JPEG2000 |はい|はい
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|最大 422 プロファイル|はい
MPEG-1 |はい|はい
Windows Media ビデオ/VC-1 |はい|はい
Canopus HQ/HQX |なし|なし
MPEG-4 Part 2 |はい|なし
[Theora](https://en.wikipedia.org/wiki/Theora) |はい|なし
Apple ProRes 422 |はい|なし
Apple ProRes 422 LT |はい|なし
Apple ProRes 422 HQ |はい|なし
Apple ProRes プロキシ|はい|なし
Apple ProRes 4444 |はい|なし
Apple ProRes 4444 XQ |はい|なし

###入力オーディオ コーデック

入力オーディオ コーデック|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー
---|---|---
AES (SMPTE 331M および 302M、AES3-2003) |なし|はい
Dolby® E |なし|はい
Dolby® Digital (AC3) |なし|はい
Dolby® Digital Plus (E-AC3) |なし|はい
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|はい|はい
MPEG Layer 2|はい|はい
MP3 (MPEG-1 Audio Layer 3)|はい|はい
Windows Media オーディオ|はい|はい
WAV/PCM|はい|はい
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|はい|なし
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |はい|なし
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|はい|なし


###出力コンテナー/ファイル形式

出力コンテナー/ファイル形式|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー
---|---|---
Adobe® Flash® F4V|なし|はい
MXF (OP1a、XDCAM および AS02)|なし|はい
DPP (AS11 を含む)|なし|はい
GXF|なし|はい
MPEG-4/MP4|はい|はい
MPEG TS|はい|はい
Windows Media/ASF|なし|はい
AVI (非圧縮 8-bit/10-bit)|なし|はい
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|なし|はい

###出力ビデオ コーデック

出力ビデオ コーデック|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー
---|---|---
AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)|8 ビット 4:2:0 のみ|はい
Avid DNxHD (MXF)|なし|はい
DVCPro/DVCProHD (MXF)|なし|はい
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|なし|はい
MPEG-1|なし|はい
Windows Media ビデオ/VC-1|なし|はい
JPEG サムネール作成|なし|はい

###出力オーディオ コーデック

出力オーディオ コーデック|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー
---|---|---
AES (SMPTE 331M および 302M、AES3-2003)|なし|はい
Dolby® Digital (AC3)|なし|はい
Dolby® Digital Plus (E-AC3) 最大 7.1|なし|はい
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|はい|はい
MPEG Layer 2|なし|はい
MP3 (MPEG-1 Audio Layer 3)|なし|はい
Windows Media オーディオ|なし|はい


##エラー コード  

次の表は、エンコード タスクの実行中にエラーが発生した場合に返される可能性があるエラー コードの一覧です。.NET コードのエラーの詳細を取得するには、[ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) クラスを使用します。REST コードのエラーの詳細を取得するには、[ErrorDetails](https://msdn.microsoft.com/library/jj853026.aspx) REST API を使用します。

ErrorDetail.Code|考えられる原因
-----|-----------------------
Unknown| タスクの実行中に発生する不明なエラー
ErrorDownloadingInputAssetMalformedContent|無効なファイル名、サイズ 0 のファイル、不正なファイル形式など、入力資産のダウンロード中に発生するエラーのカテゴリ
ErrorDownloadingInputAssetServiceFailure|ダウンロード中のネットワーク エラーやストレージ エラーなど、サービス側での問題に関するエラーのカテゴリ
ErrorParsingConfiguration|構成が有効なシステム プリセットではない、構成に無効な XML が含まれているなど、タスク <see cref="MediaTask.PrivateData"/> (構成) が有効でない場合に発生するエラーのカテゴリ
ErrorExecutingTaskMalformedContent|入力メディア ファイル内の問題が障害の原因となり、タスクの実行中に発生するエラーのカテゴリ
ErrorExecutingTaskUnsupportedFormat|メディア形式がサポートされていない、または構成と一致しないなど、提供されたファイルをメディア プロセッサが処理できない場合に発生するエラーのカテゴリ(映像のみの資産から音声のみの出力を生成しようとした場合など)
ErrorProcessingTask|コンテンツとは無関係に、メディア プロセッサでタスクの処理中に発生するその他のエラーのカテゴリ
ErrorUploadingOutputAsset|出力資産のアップロード中に発生するエラーのカテゴリ
ErrorCancelingTask|タスクをキャンセルしようとして失敗したときに発生するエラーのカテゴリ
TransientError|一時的な問題に関するエラーのカテゴリ (Azure Storage の一時的なネットワークの問題など)


**Media Services** チームのサポートを受けるには、[サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を開いてください。



##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##関連記事

- [Media Encoder Standard のプリセットをカスタマイズし、高度なエンコード タスクを実行する](media-services-custom-mes-presets-with-dotnet.md)
- [クォータと制限](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0921_2016-->