<properties 
	pageTitle="メディア エンコーダーの標準形式とコーデック" 
	description="このトピックでは、Azure Media Encoder の標準形式とコーデックの概要を示します。" 
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
	ms.date="08/11/2015"
	ms.author="juliako"/>

#メディア エンコーダーの標準形式とコーデック


このドキュメントでは、メディア エンコーダーの標準で使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。


[メディア エンコーダーのインポート形式](#import_formats)

[メディア エンコーダーのエクスポート形式](#export_formats)


##<a id="import_formats"></a>メディア エンコーダーのインポート形式 

次のセクションでは、インポートでサポートされるコーデックとファイル形式の一覧を示します。


##ビデオ コーデック:

- MPEG-2
- H.264
- YUV420 非圧縮または中間
- DNxHD
- VC-1/WMV9
- MPEG-4 Part 2
- JPEG 2000
- Theora

###オーディオ コーデック

- PCM 0
- AAC (AAC-LC、AAC-HE、AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###形式

ファイル形式|ファイル拡張子
---|---
(H.264 および AAC コーデックでの) FLV |.flv
MP4/ISMV|*.ismv
MPEG2-PS、MPEG2-TS、3GP|.ts、.ps、.3gp
MXF|.mxf
WMV/ASF|.mwv、.asf
DVR-MS|.dvr-ms 
.AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>メディア エンコーダーのエクスポート形式

次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。


ファイル形式|ビデオ コーデック|オーディオ コーデック
---|---|---
MP4 (*.mp4)<br/><br/>(マルチビットレートの MP4 コンテナーを含む) |H.264 (High、Main、Baseline Profile)|AAC-LC、HE-AAC v1、HE-AAC v2 
MPEG2-TS |H.264 (High、Main、Baseline Profile)|AAC-LC、HE-AAC v1、HE-AAC v2 

##関連項目

[Azure メディア エンコーダーを使用してオンデマンド コンテンツをエンコードする](media-services-encode-asset.md)

<!---HONumber=August15_HO7-->