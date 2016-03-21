<properties 
	pageTitle="メディア エンコーダー プレミアム ワークフローの形式とコーデック" 
	description="このトピックでは、メディア エンコーダー プレミアム ワークフローの形式とコーデックについて説明します。" 
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
	ms.date="02/03/2016"  
	ms.author="juliako"/>

#メディア エンコーダー プレミアム ワークフローの形式とコーデック


>[AZURE.NOTE]プレミアム エンコーダーに関する質問がある場合は、mepd@microsoft.com まで電子メールでお送りください。
>
>このトピックで説明するメディア エンコーダー プレミアム ワークフロー メディア プロセッサは中国では提供されません。

このドキュメントには、以前のパブリック プレビュー バージョンの**メディア エンコーダー プレミアム ワークフロー**でサポートされる入力ファイルおよび出力ファイルの形式とコーデックの一覧が含まれています。

[Media Encoder Premium Worflow の入力の形式とコーデック](#input_formats)

[Media Encoder Premium Worflow の出力の形式とコーデック](#output_formats)

**メディア エンコーダー プレミアム ワークフロー**では、[この](#closed_captioning)セクションで説明されているクローズド キャプションがサポートされています。


##<a id="input_formats"></a>メディア エンコーダー プレミアム ワークフローの入力の形式とコーデック

次のセクションに、このメディア プロセッサの入力としてサポートされたコーデックとファイル形式の一覧を示します。

###入力コンテナー/ファイル形式

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2 トランスポート ストリーム
- MPEG-2 プログラム ストリーム
- MPEG-4/MP4
- Windows Media/ASF
- AVI (非圧縮 8-bit/10-bit)

###入力ビデオ コーデック

- AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む)
- Avid DNxHD (MXF)
- DVCPro/DVCProHD (MXF)
- JPEG2000
- MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)
- MPEG-1
- Windows Media ビデオ/VC-1

###入力オーディオ コーデック

- AES (SMPTE 331M および 302M、AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media オーディオ
- WAV/PCM
 
##<a id="output_format"></a>メディア エンコーダー プレミアム ワークフローの出力の形式とコーデック

次のセクションに、このメディア プロセッサからの出力としてサポートされたコーデックとファイル形式の一覧を示します。

###出力コンテナー/ファイル形式

- Adobe® Flash® F4V
- MXF (OP1a、XDCAM および AS02)
- DPP (AS11 を含む)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (非圧縮 8-bit/10-bit)
- スムーズ ストリーミング ファイル形式 (PIFF 1.3)
- MPEG TS 


###出力ビデオ コーデック

- AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)
- Avid DNxHD (MXF)
- DVCPro/DVCProHD (MXF)
- MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)
- MPEG-1
- Windows Media ビデオ/VC-1
- JPEG サムネール作成

###出力オーディオ コーデック

- AES (SMPTE 331M および 302M、AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) 最大 7.1
- AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media オーディオ

##<a id="closed_captioning"></a>クローズド キャプションのサポート

取り込み側時は、**メディア エンコーダー プレミアム ワークフロー**で次がサポートされます。

1. SCC ファイル
1. SMPTE-TT ファイル
1. CEA 608/CEA 708 – ユーザー データ (H.264 基本ストリームの SEI メッセージ、ATSC/53、SCTE20) として送信、または MXF/GXF ファイルでの補助データとして送信
1. STL サブタイトル ファイル

出力時は、次のオプションを使用できます。

1. CEA-608 から CEA-708 への変換
1. CEA-608/CEA-708 パス スルー (H.264 基本ストリームの SEI メッセージに埋め込むか、MXF ファイルの補助データとして実行)
1. SCC
1. SMPTE RP2052 によるソース CEA-608 の SMPTE Timed Text (DFXP ファイル作成を含む)
1. SRT サブタイトル ファイル
1. DVB サブタイトル ストリーム

注: 上記の出力形式のすべてが、Azure Media Services のストリーミングを通じた配信でサポートされているわけではありません。

##既知の問題

入力ビデオにクローズド キャプションが含まれない場合でも、出力アセットには空の TTML ファイルが含まれます。


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->