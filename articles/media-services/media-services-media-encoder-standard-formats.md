<properties 
	pageTitle="メディア エンコーダーの標準形式とコーデック" 
	description="このトピックでは、Azure メディア エンコーダーの標準形式とコーデックの概要を示します。" 
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
	ms.date="07/08/2015" 
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

<table border="1">
<tr><th>ファイル形式</th><th>ファイル拡張子</th></tr>
<tr><td>(H.264 および AAC コーデックでの) FLV </td><td>.flv</td></tr>
<tr><td>MP4/ISMV</td><td>*.ismv</td></tr>
<tr><td>MPEG2-PS、MPEG2-TS、3GP</td><td>.ts、.ps、.3gp</td></tr>
<tr><td>MXF</td><td>.mxf</td></tr>
<tr><td>WMV/ASF</td><td>.mwv、.asf</td></tr>
<tr><td>DVR-MS</td><td>.dvr-ms </td></tr>
<tr><td>.AVI</td><td>.avi</td></tr>
<tr><td>Matroska</td><td>.mkv</td></tr>
<tr><td>GXF</td><td>.gxf</td></tr>
<tr><td>WAVE/WAV </td><td>.wav</td></tr>
</table>

##<a id="export_formats"></a>メディア エンコーダーのエクスポート形式

次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。


<table border="1">
<tr><th>ファイル形式</th><th>ビデオ コーデック</th><th>オーディオ コーデック</th></tr>
<tr><td>MP4 (*.mp4)<br/><br/>(マルチビットレートの MP4 コンテナーを含む) </td><td>H.264 (High、Main、Baseline Profile)</td><td>AAC-LC、HE-AAC v1、HE-AAC v2 </td></tr>
<tr><td>MPEG2-TS </td><td>H.264 (High、Main、Baseline Profile)</td><td>AAC-LC、HE-AAC v1、HE-AAC v2 </td></tr>
</table>

##関連項目

[Azure メディア エンコーダーを使用してオンデマンド コンテンツをエンコードする](media-services-encode-asset.md)

<!---HONumber=July15_HO3-->