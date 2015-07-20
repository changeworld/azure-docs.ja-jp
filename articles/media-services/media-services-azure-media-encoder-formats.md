<properties 
	pageTitle="Azure メディア エンコーダーの形式とコーデック" 
	description="このトピックでは、Azure メディア エンコーダーの形式とコーデックの概要を示します。" 
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

#Azure メディア エンコーダーの形式とコーデック

エンコーダーは、コーデックを使用してデジタル メディアを圧縮します。エンコーダーには、通常、生成されるメディアのプロパティ (使用するコーデック、ファイル形式、解像度、ビットレートなど) を指定する各種設定があります。ファイル形式は、圧縮されたビデオおよびビデオの圧縮に使用されたコーデックに関する情報を保持するコンテナーです。

コーデックには 2 つのコンポーネントがあります。デジタル メディア ファイルを転送用に圧縮するコンポーネントと、デジタル メディア ファイルを再生用に展開するコンポーネントです。オーディオを圧縮および展開するオーディオ コーデックと、ビデオを圧縮および展開するビデオ コーデックがあります。コーデックは、無損失圧縮または損失圧縮を使用できます。無損失コーデックでは、圧縮時にすべての情報が維持されます。ファイルを展開したときに、その結果は入力メディアと同じファイルになるため、無損失コーデックの作成はアーカイブおよびストレージに適しています。損失コーデックでは、エンコード時に情報の一部が失われ、元のファイルよりも小さいファイルが生成されて、ビデオ品質が低下します。これは、インターネットでのストリーミングに適しています。Azure メディア エンコーダーでエンコードに使用される 2 つの主なコーデックは、H.264 と VC-1 です。その他のコーデックは、マイクロソフトのパートナー エコシステムのエンコーダーで利用可能です。

コーデックとファイル形式の違いについて理解することは重要です。コーデックは圧縮/展開アルゴリズムを実装するソフトウェアで、ファイル形式は圧縮されたビデオを保持するコンテナーです。詳細については、「[エンコーディングとパッケージング](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/)」をご覧ください。

このドキュメントでは、Azure メディア エンコーダーで使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。


[メディア エンコーダーのインポート形式](#import_formats)

[メディア エンコーダーのエクスポート形式](#export_formats)


##<a id="import_formats"></a>メディア エンコーダーのインポート形式 

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
 
<table border="1">
<tr><th>ファイル形式</th><th>ファイル拡張子</th></tr>
<tr><td>3GPP、3GPP2</td><td>.3gpｍ.3g2、.3gp2</td></tr>
<tr><td>Advanced Systems Format (ASF)</td><td>.asf</td></tr>
<tr><td>Advanced Video Coding High Definition (AVCHD) [MPEG-2 Transport Stream]</td><td>.mts、.m2ts</td></tr>
<tr><td>Audio-Video Interleaved (AVI)</td><td>.avi</td></tr>
<tr><td>デジタル カムコーダー MPEG-2 (MOD)</td><td>.mod</td></tr>
<tr><td>DVD トランスポート ストリーム (TS) ファイル</td><td>.ts</td></tr>
<tr><td>DVD ビデオ オブジェクト (VOB) ファイル</td><td>.vob</td></tr>
<tr><td>Expression Encoder Screen Capture Codec ファイル</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>MPEG-1 System Stream</td><td>.mpeg、.mpg</td></tr>
<tr><td>MPEG-2 ビデオ ファイル</td><td>.m2v</td></tr>
<tr><td>スムーズ ストリーミング ファイル形式 (PIFF 1.3)</td><td>.ismv</td></tr>
<tr><td>Windows Media Video (WMV)</td><td>.wmv</td></tr>
</table>

一部の非圧縮形式がサポートされます。詳細については、「[サポートされている非圧縮ビデオ形式](#uncompressed)」をご覧ください。

###オーディオ ファイル形式

<table border="1">
<tr><th>ファイル形式</th><th>ファイル拡張子</th></tr>
<tr><td>AC-3 (Dolby Digital) オーディオ</td><td>.ac3</td></tr>
<tr><td>Audio Interchange File Format (AIFF)</td><td>.aiff</td></tr>
<tr><td>Broadcast Wave Format</td><td>.bwf</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>.mp3</td></tr>
<tr><td>MP4 オーディオ</td><td>.m4A</td></tr>
<tr><td>MPEG-4 オーディオ ブック</td><td>.m4b</td></tr>
<tr><td>WAVE ファイル</td><td>.wav</td></tr>
<tr><td>Windows Media オーディオ</td><td>.wma</td></tr>   
</table>

###イメージ ファイル形式

<table border="1">
<tr><th>ファイル形式</th><th>ファイル拡張子</th></tr>
<tr><td>ビットマップ</td><td>.bmp</td></tr>
<tr><td>GIF、アニメーション GIF</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg、.jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF Canvas XAML</td><td>.xaml</td></tr>
</table>


##<a id="export_formats"></a>メディア エンコーダーのエクスポート形式

次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。


<table border="1">
<tr><th>ファイル形式</th><th>ビデオ コーデック</th><th>オーディオ コーデック</th></tr>
<tr><td>Windows Media (*.wmv、*.wma)</td><td>VC-1 (Advanced、Main、Simple Profile)</td><td>Windows Media Audio Standard、Windows Media Audio Professional、Windows Media Audio Voice、Windows Media Audio Lossless</td></tr>
<tr><td>MP4 (*.mp4)</td><td>H.264 (High、Main、Baseline Profile)</td><td>AAC-LC、HE-AAC v1、HE-AAC v2、Dolby Digital Plus</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.1) (*.ismv、*.isma)</td><td>VC-1 (Advanced Profile)<br/><br/>
H.264 (High、Main、Baseline Profile)</td><td>Windows Media Audio Standard、Windows Media Audio Professional<br/><br/>
AAC-LC、HE-AAC v1、HE-AAC v2</td></tr>
</table>

Media Services でサポートされるその他のコーデックとフィルターについては、「[Windows DirectShow フィルター](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx)」をご覧ください。

##<a id="uncompressed"></a>サポートされている非圧縮ビデオ形式 

Azure Media Services は、非圧縮ビデオ データのインポートをサポートします。

これは、サポートされている非圧縮形式の一覧の一部です。

<table border="1">
<tr><th>非圧縮ビデオ形式</th><th>説明</th></tr>
<tr><td>標準の YVU9 形式の非圧縮データ</td><td>平面の YUV 形式。各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル、各垂直線の Y サンプル、4 番目ごとの垂直線の U および V サンプル。ピクセルあたり 9 ビット。</td></tr>
<tr><td>YUV 411 形式データ</td><td>各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。バイト順 (昇順) は、U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 12 バイトのブロックは、画像の 8 ピクセルです。</td></tr>
<tr><td>Y41P 形式データ</td><td>各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。バイト順 (昇順) は、U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 12 バイトのブロックは、画像の 8 ピクセルです。</td></tr>
<tr><td>YUY2 形式データ</td><td>UYVY と同じですが、ピクセルの順序が異なります。バイト順 (昇順) は、Y0、U0、Y1、V0、Y2、U2、Y3、V2、Y4、U4、Y5、V4 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 2 ピクセルです。</td></tr>
<tr><td>YVYU 形式データ</td><td>パックされた YUV 形式。UYVY と同じですが、ピクセルの順序が異なります。バイト順 (昇順) は、Y0、V0、Y1、U0、Y2、V2、Y3、U2、Y4、V4、Y5、U4 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 2 ピクセルです。</td></tr>
<tr><td>UYVY 形式データ</td><td>パックされた YUV 形式。各ピクセルの Y サンプル、各行の水平の 2 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。さまざまな YUV 4:2:2 形式のうち、最もよく使用されます。バイト順 (昇順) は、U0、Y0、V0、Y1、U2、Y2、V2、Y3、U4、Y4、V4、Y5 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 2 ピクセルです。</td></tr>
<tr><td>YUV 211 形式データ</td><td>パックされた YUV 形式。2 番目ごとのピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。バイト順 (昇順) は、Y0、U0、Y2、V0、Y4、U4、Y6、V4、Y8、U8、Y10、V8 です。ここで、サフィックス 0 は左端のピクセルで、増加する数字は左から右へ増えるピクセルです。各 4 バイトのブロックは、画像の 4 ピクセルです。</td></tr>
<tr><td>Cirrus Logic Jr YUV 411 形式</td><td>Y、U、および V サンプルあたり 8 ビット未満の Cirrus Logic Jr YUV 411 形式です。各ピクセルの Y サンプル、各行の水平の 4 番目ごとのピクセルの U および V サンプル。各垂直線がサンプリングされます。</td></tr>
<tr><td>Indeo で生成した YVU9 形式</td><td>最後のフレームとの違いに関する追加情報を持つ、Indeo で生成した YVU9 形式です。1 ピクセルあたり 9.5 ビットですが、9 ビットとして報告されます。</td></tr>
</table>

<!---HONumber=July15_HO2-->