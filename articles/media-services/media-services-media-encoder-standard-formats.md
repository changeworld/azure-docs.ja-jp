<properties 
	pageTitle="Media Encoder Standard の形式とコーデック" 
	description="このトピックでは、Azure Media Encoder Standard の形式とコーデックの概要を示します。" 
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
	ms.date="09/03/2015"
	ms.author="juliako"/>

#Media Encoder Standard の形式とコーデック


このドキュメントでは、Media Encoder Standard で使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。


##入力コンテナー/ファイル形式

ファイル形式 (ファイル拡張子)|サポートされています
---|---|---|---
(H.264 および AAC コーデックでの) FLV (.flv) |あり 
MXF (.mxf) |あり 
GXF (.gxf) |あり 
MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |あり 
Windows Media Video (WMV)/ASF (.wmv、.asf) |あり 
AVI (非圧縮 8-bit/10-bit) (.avi)|あり 
MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv)|あり 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |あり 
Matroska/WebM (.mkv) |あり 
WAVE/WAV (.wav) |あり 
 

##入力ビデオ コーデック

入力ビデオ コーデック|サポートされています
---|---|---|---
AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 および 4:2:2 
Avid DNxHD (MXF) |あり 
DVCPro/DVCProHD (MXF) |あり 
JPEG 2000 |あり 
MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)|最大 422 プロファイル 
MPEG-1 |あり 
VC-1/WMV9 |あり 
Canopus HQ/HQX |いいえ 
MPEG-4 Part 2 |あり 
[Theora](https://en.wikipedia.org/wiki/Theora) |あり 
YUV420 非圧縮または中間 |あり


##入力オーディオ コーデック

入力オーディオ コーデック|サポートされています
---|---|---|---
AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)|あり 
MPEG Layer 2|あり 
MP3 (MPEG-1 Audio Layer 3)|あり 
Windows Media オーディオ|あり 
WAV/PCM|あり 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|あり 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |あり 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|あり 
AMR (アダプティブ マルチ レート)|あり
AES (SMPTE 331M および 302M、AES3-2003) |いいえ 
Dolby® E |いいえ 
Dolby® Digital (AC3) |いいえ 
Dolby® Digital Plus (E-AC3) |いいえ 


##出力形式とコーデック

次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。


ファイル形式|ビデオ コーデック|オーディオ コーデック
---|---|---
MP4 <br/><br/>(マルチビットレートの MP4 コンテナーを含む) |H.264 (High、Main、Baseline Profile)|AAC-LC、HE-AAC v1、HE-AAC v2 
MPEG2-TS |H.264 (High、Main、Baseline Profile)|AAC-LC、HE-AAC v1、HE-AAC v2 


##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##関連項目

[Azure Media Services を使用してオンデマンド コンテンツをエンコードする](media-services-encode-asset.md)

[メディア エンコーダー スタンダードを使用したエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=Sept15_HO2-->