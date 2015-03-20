<properties 
	pageTitle="メディア エンコーダー プレミアム ワークフローの形式とコーデック" 
	description="このトピックでは、メディア エンコーダー プレミアム ワークフローの形式とコーデックについて説明します。" 
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
	ms.date="03/04/2015" 
	ms.author="juliako"/>

#メディア エンコーダー プレミアム ワークフローの形式とコーデック


**注** このトピックで説明するメディア エンコーダー プレミアム ワークフロー メディア プロセッサは中国では提供されません。

このドキュメントには、以前のパブリック プレビュー バージョンの**メディア エンコーダー プレミアム ワークフロー** エンコーダーでサポートされる入力および出力ファイルの形式とコーデックの一覧が含まれています。

[Media Encoder Premium Worflow の入力の形式とコーデック](#input_formats)

[Media Encoder Premium Worflow の出力の形式とコーデック](#output_formats)

**メディア エンコーダー プレミアム ワークフロー**は、[この](#closed_captioning) で説明するクローズド キャプションをサポートします。 


##<a id="input_formats"></a>Media Encoder Premium Worflow の入力の形式とコーデック

次のセクションに、このメディア プロセッサの入力としてサポートされたコーデックとファイル形式の一覧を示します。

###入力コンテナー/ファイル形式

- Adobe(r) Flash(r) F4V
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
- MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs(r)、D10 など、最大 422 プロファイルおよびハイ レベル)
- MPEG-1
- Windows Media ビデオ/VC-1

###入力オーディオ コーデック

- AES (SMPTE 331M および 302M、AES3-2003)
- Dolby(r) E
- Dolby(r) Digital (AC3)
- Dolby(r) Digital Plus (E-AC3)
- AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media オーディオ
- WAV/PCM
 
##<a id="output_format"></a>Media Encoder Premium Worflow の出力の形式とコーデック

次のセクションに、このメディア プロセッサからの出力としてサポートされたコーデックとファイル形式の一覧を示します。

###出力コンテナー/ファイル形式

- Adobe(r) Flash(r) F4V
- MXF (OP1a、XDCAM および AS02)
- DPP (AS11 を含む)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (非圧縮 8-bit/10-bit)
- スムーズ ストリーミング ファイル形式 (PIFF 1.3)


###出力ビデオ コーデック

- AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)
- Avid DNxHD (MXF)
- DVCPro/DVCProHD (MXF)
- MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs(r)、D10 など、最大 422 プロファイルおよびハイ レベル)
- MPEG-1
- Windows Media ビデオ/VC-1
- JPEG サムネール作成

###出力オーディオ コーデック

- AES (SMPTE 331M および 302M、AES3-2003)
- Dolby(r) Digital (AC3)
- Dolby(r) Digital Plus (E-AC3) 最大 7.1
- AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media オーディオ

##<a id="closed_captioning"></a>クローズド キャプションのサポート

現在、**Media Encoder Premium Workkflow** メディア プロセッサは、ソースまたは入力メディア ファイルへのインターリーブ時のクローズド キャプションをサポートしています: 基本的にユーザー データ (H.264 基本ストリームの SEI メッセージ、ATSC/53、SCTE20) として CEA-608/CEA-708 を実行、または MXF/GXF ファイルで補助データとして実行。

出力時は、次のオプションを使用して、入力で CEA 608/708 を再パッケージ化でできます。

- CEA-608 から CEA-708 への変換
- CEA-608/CEA-708 パス スルー (H.264 基本ストリームの SEI メッセージに埋め込むか、MXF ファイルの補助データとして実行)
- SCC
- SMPTE RP2052 によるソース CEA-608 の SMPTE Timed Text (DFXP ファイル作成を含む)
- SRT サブタイトル ファイル
- DVB サブタイトル ストリーム

注: 上記の出力形式のすべてが、Azure Media Services のストリーミングを通じた配信でサポートされているわけではありません。
<!--HONumber=47-->
