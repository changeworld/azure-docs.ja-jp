<properties 
	pageTitle="Azure Media Encoder の形式とコーデック" 
	description="このトピックでは、Azure Media Encoder の形式とコーデックの概要を示します。" 
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
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Azure Media Encoder の形式とコーデック

このドキュメントでは、Azure Media Encoder で使用できる一般的な入力および出力ファイルの形式とコーデックの一覧を示しています。


##入力ファイル形式 (コンテナー)
 
ファイル形式 (ファイル拡張子)|サポートされています
---|---
3GPP、3GPP2 (.3gp、.3g2、.3gp2) |あり
Advanced Systems Format (ASF) (.asf) |あり
Advanced Video Coding High Definition (AVCHD) [MPEG-2 Transport Stream] (.mts、.m2ts) |あり
Audio-Video Interleaved (AVI) (.avi) |あり
デジタル カムコーダー MPEG-2 (MOD) (.mod) |あり
DVD トランスポート ストリーム (TS) ファイル (.ts) |あり
DVD ビデオ オブジェクト (VOB) ファイル (.vob) |あり
Expression Encoder Screen Capture Codec ファイル (.xesc) |あり
MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |あり
MPEG-1 System Stream (.mpeg, .mpg) |あり
MPEG-2 ビデオ ファイル (.m2v) |あり
Windows Media Video (WMV) (.wmv) |あり
AC-3 (Dolby Digital) オーディオ (.ac3)|あり
Audio Interchange File Format (AIFF) (.aiff)|あり
Broadcast Wave Format(.bwf)|あり
MP3 (MPEG-1 Audio Layer 3)(.mp3)|あり
MPEG-4 オーディオ ブック (.m4b)|あり
WAVE ファイル (.wav)|あり
Windows Media オーディオ (.wma)|あり
Adobe® Flash® F4V |いいえ		
MXF/SMPTE 377M |制限あり 
GXF |いいえ		 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|いいえ
Matroska/WebM |いいえ


一部の非圧縮形式がサポートされます。詳細については、「[サポートされている非圧縮ビデオ形式](#uncompressed)」をご覧ください。

##入力ビデオ コーデック

入力ビデオ コーデック|サポートされています
---|--- 
H.264 (Baseline、Main、High Profile) |あり
AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 のみ
Avid DNxHD (MXF) |いいえ
DVCPro/DVCProHD (MXF) |いいえ
JPEG2000 |いいえ
MPEG-2 (Simple および Main Profile と 4:2:2 Profile) |最大 4:2:2 プロファイル
MPEG-1 (MPEG-PS を含む) |あり
Windows Media ビデオ/VC-1 |あり
Canopus HQ/HQX |あり
MPEG-4 v2 (Simple Visual Profile と Advanced Simple Profile) |あり
[Theora](https://en.wikipedia.org/wiki/Theora) |いいえ
VC-1 (Simple、Main、Advanced Profile) |あり
Windows Media Video (Simple、Main、Advanced Profile) |あり
DV (DVC、DVHD、DVSD、DVSL) |あり
Grass Valley HQ/HQX |あり
 

##入力オーディオ コーデック

入力オーディオ コーデック|サポートされています
---|---
AES (SMPTE 331M および 302M、AES3-2003) |いいえ
Dolby® E |いいえ
Dolby® Digital (AC3) |あり
Dolby® Digital Plus (E-AC3) |いいえ
AAC (AAC-LC、HE-AAC v1 with AAC-LC コア、HE-AAC v2 with AAC-LC コア、最大 5.1)|あり
MPEG Layer 2|あり|あり|あり
MP3 (MPEG-1 Audio Layer 3)|あり
Windows Media Audio 9 (Windows Media Audio Standard、Windows Media Audio Professional、Windows Media Audio Lossless) |あり
WAV/PCM|あり
[FLAC](https://en.wikipedia.org/wiki/FLAC)|いいえ
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |いいえ
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|いいえ


##イメージ ファイル形式

ファイル形式 (ファイル拡張子) | サポートされています
---|---
ビットマップ (.bmp) | あり
GIF、アニメーション GIF (.gif)| あり
JPEG (.jpeg、.jpg)| あり
PNG (.png)| あり
TIFF (.tif)| あり
WPF Canvas XAML (.xaml)| あり


##出力形式とコーデック

次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。

ファイル形式|ビデオ コーデック|オーディオ コーデック
---|---|---
Windows Media (*.wmv、*.wma)|VC-1 (Advanced、Main、Simple Profile)|Windows Media Audio Standard、Windows Media Audio Professional、Windows Media Audio Voice、Windows Media Audio Lossless
MP4 (*.mp4)|H.264 (High、Main、Baseline Profile)|AAC-LC、HE-AAC v1、HE-AAC v2、Dolby Digital Plus
Smooth Streaming File Format (PIFF 1.1) (*.ismv、*.isma)|VC-1 (Advanced Profile)<p>H.264 (High、Main、Baseline Profiles) |Windows Media Audio Standard、Windows Media Audio Professional<p><p>AAC-LC、HE-AAC v1、HE-AAC v2

Media Services でサポートされるその他のコーデックとフィルターについては、「[Windows DirectShow フィルター](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx)」をご覧ください。

##<a id="uncompressed"></a>サポートされている非圧縮ビデオ形式 

Azure Media Services は、非圧縮ビデオ データのインポートをサポートします。

これは、サポートされている非圧縮形式の一覧の一部です。

非圧縮ビデオ形式|説明
---|---
標準の YVU9 形式の非圧縮データ|平面の YUV 形式。各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル、各垂直線の Y サンプル、4 番目ごとの垂直線の U および V サンプル。ピクセルあたり 9 ビット。
YUV 411 形式データ|各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。バイト順 (昇順) は、U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 12 バイトのブロックは、画像の 8 ピクセルです。
Y41P 形式データ|各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。バイト順 (昇順) は、U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 12 バイトのブロックは、画像の 8 ピクセルです。
YUY2 形式データ|UYVY と同じですが、ピクセルの順序が異なります。バイト順 (昇順) は、Y0、U0、Y1、V0、Y2、U2、Y3、V2、Y4、U4、Y5、V4 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 2 ピクセルです。
YVYU 形式データ|パックされた YUV 形式。UYVY と同じですが、ピクセルの順序が異なります。バイト順 (昇順) は、Y0、V0、Y1、U0、Y2、V2、Y3、U2、Y4、V4、Y5、U4 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 2 ピクセルです。
UYVY 形式データ|パックされた YUV 形式。各ピクセルの Y サンプル、各行の水平の 2 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。さまざまな YUV 4:2:2 形式のうち、最もよく使用されます。バイト順 (昇順) は、U0、Y0、V0、Y1、U2、Y2、V2、Y3、U4、Y4、V4、Y5 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 2 ピクセルです。
YUV 211 形式データ|パックされた YUV 形式。2 番目ごとのピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。バイト順 (昇順) は、Y0、U0、Y2、V0、Y4、U4、Y6、V4、Y8、U8、Y10、V8 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 4 ピクセルです。
Cirrus Logic Jr YUV 411 形式|Y、U、および V サンプルあたり 8 ビット未満の Cirrus Logic Jr YUV 411 形式です。各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。
Indeo で生成した YVU9 形式|最後のフレームとの違いに関する追加情報を持つ、Indeo で生成した YVU9 形式です。1 ピクセルあたり 9.5 ビットですが、9 ビットとして報告されます。


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->