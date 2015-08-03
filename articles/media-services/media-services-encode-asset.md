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
	ms.date="07/16/2015" 
	ms.author="juliako"/>

#Azure メディア エンコーダーを使用してオンデマンド コンテンツをエンコードする

このトピックは、[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)の一部です。

##概要

Media Services は、次のエンコーダーをサポートしてます。

- [メディア エンコーダー スタンダード](#media_encoder_standard)
- [Azure メディア エンコーダー](#azure_media_encoder)
- [メディア エンコーダー プレミアム ワークフロー](#media_encoder_premium_workflow) (パブリック プレビュー)

[次のセクション](#compare_encoders)では、サポートされるエンコーダーのエンコード機能を比較します。

既定では、1 つの Media Services アカウントにつき、同時に 1 つのアクティブなエンコーディング タスクを実行できます。エンコード ユニットを予約して、複数のエンコード タスク (購入したエンコード予約済みユニットごとに 1 つ) を同時に実行できます。エンコード ユニットの拡張の詳細については、**ポータル**と **.NET** のトピックをご覧ください。

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>メディア エンコーダー スタンダード

[メディア エンコーダー スタンダード でサポートされる形式](media-services-media-encoder-standard-formats.md): **メディア エンコーダー スタンダード**でサポートされるファイル形式とストリーム形式について説明します。

**メディア エンコーダー スタンダード**は、[ここ](http://go.microsoft.com/fwlink/?LinkId=618336)で説明したエンコーダー プリセット、または[これらの](http://go.microsoft.com/fwlink/?LinkId=618336)プリセットに基づいたカスタム プリセットのいずれかを使用して構成します。

詳細については、[この投稿](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)を参照してください。

##<a id="azure_media_encoder"></a>Azure メディア エンコーダー

[Media Services Encoder でサポートされる形式](media-services-azure-media-encoder-formats.md): **Azure メディア エンコーダー**でサポートされるファイル形式とストリーム形式について説明します。

**Azure メディア エンコーダー**は、[ここ](https://msdn.microsoft.com/library/azure/dn619392.aspx)で説明されているエンコーダーのプリセット文字列のいずれかを使用して構成されます。実際の Azure メディア エンコーダーのプリセット ファイルは、[ここ](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)で入手できます。

**Azure の管理ポータル**、**.NET**、または **REST API** を使用して、**Azure Media Encoder** でエンコードします。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####その他の関連トピック

[動的パッケージ](https://msdn.microsoft.com/library/azure/jj889436.aspx): アダプティブ ビットレート MP4 のエンコード方法と、スムーズ ストリーミング、Apple HLS、MPEG-DASH を動的に提供する方法について説明します。

[Media Services エンコーダーの出力ファイル名の制御](https://msdn.microsoft.com/library/azure/dn303341.aspx): Azure メディア エンコーダーで使用されるファイルの名前付け規則と、出力ファイル名の変更方法について説明します。

[ドルビー デジタル プラスを使ったメディアのエンコード](media-services-encode-with-dolby-digital-plus.md): ドルビー デジタル プラスのエンコーディングを使用してオーディオ トラックをエンコードする方法について説明します。


##<a id="media_encoder_premium_wokrflow"></a>メディア エンコーダー プレミアム ワークフロー (プレビュー)

**注** このトピックで説明するメディア エンコーダー プレミアム ワークフロー メディア プロセッサは中国では提供されません。

[メディア エンコーダー プレミアム ワークフローでサポートされる形式](media-services-premium-workflow-encoder-formats.md): **メディア エンコーダー プレミアム ワークフロー**でサポートされるファイルの形式とコーデックについて説明します。

**メディア エンコーダー プレミアム ワークフロー**は複雑なワークフローを使用して構成されています。ワークフローのファイルは、[ワークフロー デザイナー](media-services-workflow-designer.md) ツールを使用して作成します。

既定のワークフロー ファイルは[こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)から入手できます。フォルダーにはこれらのファイルの説明も含まれています。

**.NET** を使用して**メディア エンコーダー プレミアム ワークフロー**でエンコードします。詳細については、「[メディア エンコーダー プレミアム ワークフローでの高度なエンコード](media-services-encode-with-premium-workflow.md)」を参照してください。
 

##<a id="compare_encoders"></a>エンコーダーの比較

###<a id="billing"></a>各エンコーダーで使用される課金メーター

<table border="1">
<tr><th>メディア プロセッサ名</th><th>適用される料金</th><th>メモ</th></tr>
<tr><td><b>Windows Azure メディア エンコーダー</b></td><td>従来のエンコーダー</td><td>エンコード タスクは、<a href="http://azure.microsoft.com/pricing/details/media-services/">こちら</a>の「従来のエンコーダー」列に指定されている料金で、入力アセットと出力アセットのサイズ合計 (GB) に従って課金されます。</td></tr>
<tr><td><b>Azure メディア エンコーダー</b></td><td>エンコーダー</td><td>エンコード タスクは、<a href="http://azure.microsoft.com/pricing/details/media-services/">こちら</a>の「エンコーダー」列に指定されている料金で、出力アセットのサイズ (GB) に従って課金されます。</td></tr>
<tr><td><b>メディア エンコーダー スタンダード</b></td><td>エンコーダー</td><td>エンコード タスクは、<a href="http://azure.microsoft.com/pricing/details/media-services/">こちら</a>の「エンコーダー」列に指定されている料金で、出力アセットのサイズ (GB) に従って課金されます。</td></tr>
<tr><td><b>メディア エンコーダー プレミアム ワークフロー</b></td><td>プレミアム エンコーダー</td><td>エンコード タスクは、<a href="http://azure.microsoft.com/pricing/details/media-services/">こちら</a>の「PREMIUM エンコーダー」列に指定されている料金で、出力アセットのサイズ (GB) に従って課金されます。</td></tr>
</table>


このセクションでは、**Azure メディア エンコーダー**、**メディア エンコーダー プレミアム ワークフロー**、および**メディア エンコーダー スタンダード**のエンコード機能を比較します。


###入力形式

入力コンテナー/ファイル形式

<table border="1">
<tr><th>入力コンテナー/ファイル形式</th><th>メディア エンコーダー プレミアム ワークフロー</th><th>Azure メディア エンコーダー
</th><th>メディア エンコーダー スタンダード</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>あり</td><td>いいえ</td><td>あり</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>あり</td><td>制限あり</td><td>あり</td></tr>
<tr><td>GXF</td><td>あり</td><td>いいえ</td><td>あり</td></tr>
<tr><td>MPEG-2 トランスポート ストリーム</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>MPEG-2 プログラム ストリーム</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>MPEG-4/MP4</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>Windows Media/ASF</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>AVI (非圧縮 8-bit/10-bit)</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>3GPP/3GPP2</td><td>いいえ</td><td>あり</td><td>あり</td></tr>
<tr><td>スムーズ ストリーミング ファイル形式 (PIFF 1.3)</td><td>いいえ</td><td>あり</td><td>あり</td></tr>
<tr><td><a href="https://msdn.microsoft.com/ja-jp/library/windows/desktop/dd692984(v=vs.85).aspx">Microsoft Digital Video Recording (DVR-MS)</a></td><td>いいえ</td><td>いいえ</td><td>あり</td></tr>
<tr><td>Matroska/WebM</td><td>いいえ</td><td>いいえ</td><td>あり</td></tr></table>

入力ビデオ コーデック

<table border="1">
<tr><th>入力ビデオ コーデック</th><th>メディア エンコーダー プレミアム ワークフロー</th><th>Azure メディア エンコーダー</th><th>メディア エンコーダー スタンダード</th></tr>
<tr><td>AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む)</td><td>あり</td><td>8 ビット 4:2:0 のみ</td><td>8 ビット 4:2:0 および 4:2:2</td></tr>
<tr><td>Avid DNxHD (MXF)</td><td>あり</td><td>いいえ</td><td>あり</td></tr>
<tr><td>DVCPro/DVCProHD (MXF)</td><td>あり</td><td>いいえ</td><td>あり</td></tr>
<tr><td>JPEG2000</td><td>あり</td><td>いいえ</td><td>あり</td></tr>
<tr><td>MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)</td><td>あり</td><td>最大 422 プロファイル</td><td>最大 422 プロファイル</td></tr>
<tr><td>MPEG-1</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>Windows Media ビデオ/VC-1</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>Canopus HQ/HQX</td><td>いいえ</td><td>あり</td><td>いいえ</td></tr>
<tr><td>MPEG-4 Part 2</td><td>いいえ</td><td>いいえ</td><td>あり</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Theora">Theora</a></td><td>いいえ</td><td>いいえ</td><td>あり</td></tr>
</table>

入力オーディオ コーデック

<table border="1">
<tr><th>入力オーディオ コーデック</th><th>メディア エンコーダー プレミアム ワークフロー</th><th>Azure メディア エンコーダー</th><th>メディア エンコーダー スタンダード</th></tr>
<tr><td>AES (SMPTE 331M および 302M、AES3-2003)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>Dolby® E</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>あり</td><td>あり</td><td>いいえ</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>MPEG Layer 2</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>Windows Media オーディオ</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>WAV/PCM</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/FLAC">FLAC</a></td><td>いいえ</td><td>いいえ</td><td>あり</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Opus_(audio_format)">Opus</a></td><td>いいえ</td><td>いいえ</td><td>あり</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Vorbis">Vorbis</a></td><td>いいえ</td><td>いいえ</td><td>あり</td></tr>
</table>

###出力形式

出力コンテナー/ファイル形式

<table border="1">
<tr><th>出力コンテナー/ファイル形式</th><th>メディア エンコーダー プレミアム ワークフロー</th><th>Azure メディア エンコーダー</th><th>メディア エンコーダー スタンダード</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>MXF (OP1a、XDCAM および AS02)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>DPP (AS11 を含む)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>GXF</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>MPEG-4/MP4</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>MPEG TS</td><td>あり</td><td>いいえ</td><td>あり</td></tr>
<tr><td>Windows Media/ASF</td><td>あり</td><td>あり</td><td>いいえ</td></tr>
<tr><td>AVI (非圧縮 8-bit/10-bit)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>スムーズ ストリーミング ファイル形式 (PIFF 1.3)</td><td>あり</td><td>あり</td><td>いいえ</td></tr>
</table>

出力ビデオ コーデック

<table border="1">
<tr><th>出力ビデオ コーデック</th><th>メディア エンコーダー プレミアム ワークフロー</th><th>Azure メディア エンコーダー</th><th>メディア エンコーダー スタンダード</th></tr>
<tr><td>AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)</td><td>あり</td><td>8 ビット 4:2:0 のみ (最大 1080p)</td><td>8 ビット 4:2:0 のみ</td></tr>
<tr><td>Avid DNxHD (MXF)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>DVCPro/DVCProHD (MXF)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>MPEG-1</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>Windows Media ビデオ/VC-1</td><td>あり</td><td>あり</td><td>いいえ</td></tr>
<tr><td>JPEG サムネール作成</td><td>あり</td><td>あり</td><td>いいえ</td></tr>
</table>

出力オーディオ コーデック

<table border="1">
<tr><th>出力オーディオ コーデック</th><th>メディア エンコーダー プレミアム ワークフロー</th><th>Azure メディア エンコーダー</th><th>メディア エンコーダー スタンダード</th></tr>
<tr><td>AES (SMPTE 331M および 302M、AES3-2003)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>あり</td><td>あり</td><td>いいえ</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) 最大 7.1</td><td>あり</td><td>最大 5.1</td><td>いいえ</td></tr>
<tr><td>AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)</td><td>あり</td><td>あり</td><td>あり</td></tr>
<tr><td>MPEG Layer 2</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>あり</td><td>いいえ</td><td>いいえ</td></tr>
<tr><td>Windows Media オーディオ</td><td>あり</td><td>あり&lt;/td<td>いいえ</td></tr>
</table>

##関連記事:

- [Azure Media Services のプレミアム エンコーディングの紹介 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Azure Media Services でプレミアム エンコーディングを使用する方法 (ブログの投稿](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [クォータと制限](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO4-->