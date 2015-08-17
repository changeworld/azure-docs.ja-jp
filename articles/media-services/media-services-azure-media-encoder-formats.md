<properties 
	pageTitle="Azure Media Encoder の形式とコーデック" 
	description="このトピックでは、Azure Media Encoder の形式とコーデックの概要を示します。" 
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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Azure Media Encoder の形式とコーデック

エンコーダーは、コーデックを使用してデジタル メディアを圧縮します。エンコーダーには、通常、生成されるメディアのプロパティ (使用するコーデック、ファイル形式、解像度、ビットレートなど) を指定する各種設定があります。ファイル形式は、圧縮されたビデオおよびビデオの圧縮に使用されたコーデックに関する情報を保持するコンテナーです。

コーデックには 2 つのコンポーネントがあります。デジタル メディア ファイルを転送用に圧縮するコンポーネントと、デジタル メディア ファイルを再生用に展開するコンポーネントです。オーディオを圧縮および展開するオーディオ コーデックと、ビデオを圧縮および展開するビデオ コーデックがあります。コーデックは、無損失圧縮または損失圧縮を使用できます。無損失コーデックでは、圧縮時にすべての情報が維持されます。ファイルを展開したときに、その結果は入力メディアと同じファイルになるため、無損失コーデックの作成はアーカイブおよびストレージに適しています。損失コーデックでは、エンコード時に情報の一部が失われ、元のファイルよりも小さいファイルが生成されて、ビデオ品質が低下します。これは、インターネットでのストリーミングに適しています。Azure Media Encoder でエンコードに使用される 2 つの主なコーデックは、H.264 と VC-1 です。その他のコーデックは、マイクロソフトのパートナー エコシステムのエンコーダーで利用可能です。

コーデックとファイル形式の違いについて理解することは重要です。コーデックは圧縮/展開アルゴリズムを実装するソフトウェアで、ファイル形式は圧縮されたビデオを保持するコンテナーです。詳細については、「[エンコーディングとパッケージング](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/)」をご覧ください。

このドキュメントでは、Azure Media Encoder で使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。


[Azure Media Encoder のインポート形式](#import_formats)

[Azure Media Encoder のエクスポート形式](#export_formats)


##<a id="import_formats"></a>Azure Media Encoder のインポート形式 

次のセクションでは、インポートでサポートされるコーデックとファイル形式の一覧を示します。

###ビデオ コーデック

- H.264 (Baseline、Main、High Profile)
- MPEG-1 (MPEG-PS を含む)
- MPEG-2 (Simple および Main Profile と 4:2:2 Profile)
- MPEG-4 v2 (Simple Visual Profile と Advanced Simple Profile)
- VC-1 (Simple、Main、Advanced Profile)
- Windows Media Video (Simple、Main、Advanced Profile)
- DV (DVC、DVHD、DVSD、DVSL)
- Grass Valley HQ/HQX
 
###オーディオ コーデック

- AC-3 (Dolby Digital オーディオ)
- AAC (AAC-LC、HE-AAC v1 with AAC-LC コア、HE-AAC v2 with AAC-LC コア)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard、Windows Media Audio Professional、Windows Media Audio Lossless)

###ビデオ ファイル形式
 
ファイル形式|ファイル拡張子
---|---
3GPP、3GPP2|.3gpｍ.3g2、.3gp2
Advanced Systems Format (ASF)|.asf
Advanced Video Coding High Definition (AVCHD) [MPEG-2 Transport Stream]|.mts、.m2ts
Audio-Video Interleaved (AVI)|.avi
デジタル カムコーダー MPEG-2 (MOD)|.mod
DVD トランスポート ストリーム (TS) ファイル|.ts
DVD ビデオ オブジェクト (VOB) ファイル|.vob
Expression Encoder Screen Capture Codec ファイル|.xesc
MP4|.mp4
MPEG-1 System Stream|.mpeg、.mpg
MPEG-2 ビデオ ファイル|.m2v
スムーズ ストリーミング ファイル形式 (PIFF 1.3)|.ismv
Windows Media Video (WMV)|.wmv


一部の非圧縮形式がサポートされます。詳細については、「[サポートされている非圧縮ビデオ形式](#uncompressed)」をご覧ください。

###オーディオ ファイル形式

ファイル形式|ファイル拡張子
---|---
AC-3 (Dolby Digital) オーディオ|.ac3
Audio Interchange File Format (AIFF)|.aiff
Broadcast Wave Format|.bwf
MP3 (MPEG-1 Audio Layer 3)|.mp3
MP4 オーディオ|.m4A
MPEG-4 オーディオ ブック|.m4b
WAVE ファイル|.wav
Windows Media オーディオ|.wma

###イメージ ファイル形式

ファイル形式|ファイル拡張子
---|---
ビットマップ|.bmp
GIF、アニメーション GIF|.gif
JPEG|.jpeg、.jpg
PNG|.png
TIFF|.tif
WPF Canvas XAML|.xaml


##<a id="export_formats"></a>Azure Media Encoder のエクスポート形式

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

<!---HONumber=August15_HO6-->