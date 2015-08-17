<properties 
	pageTitle="Azure メディア エンコーダーを使用してオンデマンド コンテンツをエンコードする" 
	description="このトピックでは、Media Services でのオンデマンド コンテンツのエンコードの概要について説明します。" 
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
	ms.date="07/23/2015" 
	ms.author="juliako"/>

#Azure メディア エンコーダーを使用してオンデマンド コンテンツをエンコードする

このトピックは、[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)の一部です。

##概要

Media Services は、次のエンコーダーをサポートしてます。

- [メディア エンコーダー スタンダード](#media_encoder_standard)
- [Azure Media Encoder](#azure_media_encoder)
- [メディア エンコーダー プレミアム ワークフロー](#media_encoder_premium_workflow)

[次のセクション](#compare_encoders)では、サポートされるエンコーダーのエンコード機能を比較します。

既定では、1 つの Media Services アカウントにつき、同時に 1 つのアクティブなエンコーディング タスクを実行できます。エンコード ユニットを予約して、複数のエンコード タスク (購入したエンコード予約ユニットごとに 1 つ) を同時に実行できます。エンコード ユニットの拡張の詳細については、**ポータル**と **.NET** のトピックをご覧ください。

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>メディア エンコーダー スタンダード

[メディア エンコーダー スタンダード でサポートされる形式](media-services-media-encoder-standard-formats.md): **メディア エンコーダー スタンダード**でサポートされるファイル形式とストリーム形式について説明します。

**メディア エンコーダー スタンダード**は、[ここ](http://go.microsoft.com/fwlink/?LinkId=618336)で説明したエンコーダー プリセット、または[これらの](http://go.microsoft.com/fwlink/?LinkId=618336)プリセットに基づいたカスタム プリセットのいずれかを使用して構成します。

詳細については、[この投稿](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)を参照してください。

##<a id="azure_media_encoder"></a>Azure Media Encoder

[Media Services Encoder でサポートされる形式](media-services-azure-media-encoder-formats.md): **Azure Media Encoder** でサポートされるファイル形式とストリーム形式について説明します。

**Azure Media Encoder** は、[ここ](https://msdn.microsoft.com/library/azure/dn619392.aspx)で説明されているエンコーダーのプリセット文字列のいずれかを使用して構成されます。実際の Azure Media Encoder のプリセット ファイルは、[ここ](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)で入手できます。

**Azure の管理ポータル**、**.NET**、または **REST API** を使用して、**Azure Media Encoder** でエンコードします。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####その他の関連トピック

[動的パッケージ](https://msdn.microsoft.com/library/azure/jj889436.aspx): アダプティブ ビットレート MP4 のエンコード方法と、スムーズ ストリーミング、Apple HLS、MPEG-DASH を動的に提供する方法について説明します。

[Media Services エンコーダーの出力ファイル名の制御](https://msdn.microsoft.com/library/azure/dn303341.aspx): Azure Media Encoder で使用されるファイルの名前付け規則と、出力ファイル名の変更方法について説明します。

[ドルビー デジタル プラスを使ったメディアのエンコード](media-services-encode-with-dolby-digital-plus.md): ドルビー デジタル プラスのエンコーディングを使用してオーディオ トラックをエンコードする方法について説明します。


##<a id="media_encoder_premium_wokrflow"></a>メディア エンコーダー プレミアム ワークフロー 

**注** このトピックで説明するメディア エンコーダー プレミアム ワークフロー メディア プロセッサは中国では提供されません。

[メディア エンコーダー プレミアム ワークフローでサポートされる形式](media-services-premium-workflow-encoder-formats.md): **メディア エンコーダー プレミアム ワークフロー**でサポートされるファイルの形式とコーデックについて説明します。

**メディア エンコーダー プレミアム ワークフロー**は複雑なワークフローを使用して構成されています。ワークフローのファイルは、[ワークフロー デザイナー](media-services-workflow-designer.md) ツールを使用して作成します。

既定のワークフロー ファイルは[こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)から入手できます。フォルダーにはこれらのファイルの説明も含まれています。

**.NET** を使用して**メディア エンコーダー プレミアム ワークフロー**でエンコードします。詳細については、「[メディア エンコーダー プレミアム ワークフローでの高度なエンコード](media-services-encode-with-premium-workflow.md)」を参照してください。
 

##<a id="compare_encoders"></a>エンコーダーの比較

###<a id="billing"></a>各エンコーダーで使用される課金メーター

メディア プロセッサ名|適用される料金|メモ
---|---|---
**Microsoft Azure Media Encoder** |従来のエンコーダー|エンコード タスクは、[こちら][1]の「従来のエンコーダー」列に指定されている料金で、入力資産と出力資産のサイズ合計 (GB) に従って課金されます。
**Azure Media Encoder** |エンコーダー|エンコード タスクは、[こちら][1]の「エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。
**メディア エンコーダー スタンダード** |エンコーダー|エンコード タスクは、[こちら][1]の「エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。
**メディア エンコーダー プレミアム ワークフロー** |プレミアム エンコーダー|エンコード タスクは、[こちら][1]の「PREMIUM エンコーダー」列に指定されている料金で、出力資産のサイズ (GB) に従って課金されます。



このセクションでは、**Azure Media Encoder**、**メディア エンコーダー プレミアム ワークフロー**、および**メディア エンコーダー スタンダード**のエンコード機能を比較します。


###入力形式

入力コンテナー/ファイル形式

入力コンテナー/ファイル形式|メディア エンコーダー プレミアム ワークフロー|Azure Media Encoder|メディア エンコーダー スタンダード
---|---|---|---
Adobe® Flash® F4V|あり|いいえ|あり
MXF/SMPTE 377M|あり|制限あり|あり
GXF|あり|いいえ|あり
MPEG-2 トランスポート ストリーム|あり|あり|あり
MPEG-2 プログラム ストリーム|あり|あり|あり
MPEG-4/MP4|あり|あり|あり
Windows Media/ASF|あり|あり|あり
AVI (非圧縮 8-bit/10-bit)|あり|あり|あり
3GPP/3GPP2|いいえ|あり|あり
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|いいえ|あり|あり
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|いいえ|いいえ|あり
Matroska/WebM|いいえ|いいえ|あり

入力ビデオ コーデック

入力ビデオ コーデック|メディア エンコーダー プレミアム ワークフロー|Azure Media Encoder|メディア エンコーダー スタンダード
---|---|---|---
AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む)|あり|8 ビット 4:2:0 のみ|8 ビット 4:2:0 および 4:2:2
Avid DNxHD (MXF)|あり|いいえ|あり
DVCPro/DVCProHD (MXF)|あり|いいえ|あり
JPEG2000|あり|いいえ|あり
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|あり|最大 422 プロファイル|最大 422 プロファイル
MPEG-1|あり|あり|あり
Windows Media ビデオ/VC-1|あり|あり|あり
Canopus HQ/HQX|いいえ|あり|いいえ
MPEG-4 Part 2|いいえ|いいえ|あり
[Theora](https://en.wikipedia.org/wiki/Theora)|いいえ|いいえ|あり

入力オーディオ コーデック

入力オーディオ コーデック|メディア エンコーダー プレミアム ワークフロー|Azure Media Encoder|メディア エンコーダー スタンダード
---|---|---|---
AES (SMPTE 331M および 302M、AES3-2003)|あり|いいえ|いいえ
Dolby® E|あり|いいえ|いいえ
Dolby® Digital (AC3)|あり|あり|いいえ
Dolby® Digital Plus (E-AC3)|あり|いいえ|いいえ
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|あり|あり|あり
MPEG Layer 2|あり|あり|あり
MP3 (MPEG-1 Audio Layer 3)|あり|あり|あり
Windows Media オーディオ|あり|あり|あり
WAV/PCM|あり|あり|あり
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|いいえ|いいえ|あり
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |いいえ|いいえ|あり
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|いいえ|いいえ|あり

###出力形式

出力コンテナー/ファイル形式

出力コンテナー/ファイル形式|メディア エンコーダー プレミアム ワークフロー|Azure Media Encoder|メディア エンコーダー スタンダード
---|---|---|---
Adobe® Flash® F4V|あり|いいえ|いいえ
MXF (OP1a、XDCAM および AS02)|あり|いいえ|いいえ
DPP (AS11 を含む)|あり|いいえ|いいえ
GXF|あり|いいえ|いいえ
MPEG-4/MP4|あり|あり|あり
MPEG TS|あり|いいえ|あり
Windows Media/ASF|あり|あり|いいえ
AVI (非圧縮 8-bit/10-bit)|あり|いいえ|いいえ
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|あり|あり|いいえ

出力ビデオ コーデック

出力ビデオ コーデック|メディア エンコーダー プレミアム ワークフロー|Azure Media Encoder|メディア エンコーダー スタンダード
---|---|---|---
AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)|あり|8 ビット 4:2:0 のみ (最大 1080p)|8 ビット 4:2:0 のみ
Avid DNxHD (MXF)|あり|いいえ|いいえ
DVCPro/DVCProHD (MXF)|あり|いいえ|いいえ
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|あり|いいえ|いいえ
MPEG-1|あり|いいえ|いいえ
Windows Media ビデオ/VC-1|あり|あり|いいえ
JPEG サムネール作成|あり|あり|いいえ

出力オーディオ コーデック

出力オーディオ コーデック|メディア エンコーダー プレミアム ワークフロー|Azure Media Encoder|メディア エンコーダー スタンダード
---|---|---|---
AES (SMPTE 331M および 302M、AES3-2003)|あり|いいえ|いいえ
Dolby® Digital (AC3)|あり|あり|いいえ
Dolby® Digital Plus (E-AC3) 最大 7.1|あり|最大 5.1|いいえ
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|あり|あり|あり
MPEG Layer 2|あり|いいえ|いいえ
MP3 (MPEG-1 Audio Layer 3)|あり|いいえ|いいえ
Windows Media オーディオ|あり|あり|いいえ

##関連記事:

- [Azure Media Services のプレミアム エンコーディングの紹介 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Azure Media Services でプレミアム エンコーディングを使用する方法 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [クォータと制限](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO6-->