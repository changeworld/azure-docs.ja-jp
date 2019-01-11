---
title: フィルターと Azure Media Services の動的マニフェスト | Microsoft Docs
description: このトピックでは、クライアントがストリームの特定のセクションをストリームする際に使用できるフィルターを作成する方法について説明します。 Media Services では、動的マニフェストを作成してこの選択型ストリーミングをアーカイブします。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 7dc2136fe6ee28da0583ebdb2b2749ddf1c37049
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728042"
---
# <a name="filters-and-dynamic-manifests"></a>フィルターと動的マニフェスト

コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、資産の既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、コンテンツのアセット フィルターとアカウント フィルターを定義することができます。 

フィルターは、次のような操作を顧客に許可するサーバー側のルールです。 

- ビデオ全体を再生するのではなく、ビデオの 1 つのセクションのみを再生する。 例: 

    - ライブ イベントのサブクリップを表示するようにマニフェストを縮小する ("サブクリップ フィルター処理")
    - ビデオの開始をトリミングする場合 (「ビデオのトリミング」)

- コンテンツの再生に使用するデバイスでサポートされている演奏や言語のトラックのみを指定して配信する ("演奏フィルタ―処理")。 
- プレゼンテーション ウィンドウ (DVR) を調整し、プレーヤーの DVR ウィンドウの長さを限定する ("プレゼンテーション ウィンドウの調整")。

このトピックでは、[概念](#concepts)を説明すると共に[フィルターの定義を紹介](#definitions)します。 その後、[一般的なシナリオ](#common-scenarios)について詳しく取り上げます。 この記事の最後には、プログラムを使ってフィルターを作成する方法を紹介したページへのリンクを掲載しています。  

## <a name="concepts"></a>概念

### <a name="dynamic-manifests"></a>動的マニフェスト

Media Services には、定義済みの**フィルター**に基づいた[動的マニフェスト](#filters)が用意されています。 フィルターを定義しておくと、クライアントはそのフィルターを使用して、ビデオの特定の演奏やサブクリップをストリーム配信できるようになります。 ストリーミング URL にフィルターを指定することもできます。 フィルターは、アダプティブ ビットレート ストリーミング プロトコル(Apple HTTP Live Streaming (HLS)、MPEG DASH、Smooth Streaming) に適用できます。 

次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> アセットやその既定のマニフェストが動的マニフェストによって変更されることはありません。 クライアントはストリームにフィルターを使用するかしないかを選択できます。 
> 
> 

### <a name="manifest-files"></a>マニフェスト ファイル

アダプティブ ビットレート ストリーミングのアセットをエンコードすると、 **マニフェスト** (再生リスト) ファイルが作成されます (ファイルはテキスト ベースか XML ベース)。 **マニフェスト** ファイルには、トラックの種類 (オーディオ、ビデオ、テキスト)、トラック名、開始時刻と終了時刻、ビットレート (品質)、トラック言語、プレゼンテーション ウィンドウ (固定時間のスライディング ウィンドウ)、ビデオ コーデック (FourCC) などの、ストリーミング メタデータが含まれます。 また、次に再生可能なビデオ フラグメントとその場所の情報を通知して、次のフラグメントを取得するようにプレイヤーに指示します。 フラグメント (またはセグメント) とは、ビデオ コンテンツの実際の "チャンク" です。

以下に、HLS マニフェスト ファイルの例を示します。 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>マニフェスト ファイルを取得して調査する

動的に作成されたマニフェストに含めるべきストリーム (ライブまたはビデオ オン デマンド) のトラックに基づいて、一連のフィルター トラック プロパティ条件を指定します。 トラックのプロパティを取得して調査するためには、まず Smooth Streaming マニフェストを読み込む必要があります。

[.NET を使用したファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md)に関するチュートリアルでは、.NET を使用してストリーミング URL を構築する方法が紹介されています ([URL の構築](stream-files-tutorial-with-api.md#get-streaming-urls)に関するセクションを参照)。 そのアプリを実行すると、いずれかの URL が次の Smooth Streaming マニフェストを指し示します。`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/>この URL をコピーしてブラウザーのアドレス バーに貼り付けます。 該当するファイルがダウンロードされます。 それを任意のテキスト エディターで開いてください。

REST の例については、[REST を使用したファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)に関するページを参照してください。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>ビデオ ストリームのビットレートを監視する

[Azure Media Player のデモ ページ](http://aka.ms/amp)を使用して、ビデオ ストリームのビットレートを監視します。 デモ ページの **[Diagnostics]\(診断\)** タブに診断情報が表示されます。

![Azure Media Player の診断][amp_diagnostics]

## <a name="defining-filters"></a>フィルターを定義する

次の 2 種類のアセット フィルターがあります。 

* [アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters) (グローバル) - Azure Media Services アカウントのすべてのアセットに適用可能。アカウントの有効期間があります。
* [アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters) (ローカル) - 作成時にフィルターに関連付けられたアセットにのみ適用可能。アセットの有効期間があります。 

[アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters) タイプと[アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters) タイプは、フィルターの定義/記述に関して、まったく同じプロパティを持ちます。 ただし**アセット フィルター**を作成するときは、フィルターを関連付けるアセットの名前を指定する必要があります。

実際のシナリオに応じて、アセット フィルターとアカウント フィルターのどちらのタイプのフィルターが適しているかを判断することになります。 アカウント フィルターは、デバイス プロファイル (演奏フィルター処理) に適しています。一方、アセット フィルターは、特定のアセットをトリミングする目的で使用できます。

フィルターは、次のプロパティを使用して記述します。 

|Name|説明|
|---|---|
|firstQuality|フィルターの最高品質ビットレートです。|
|presentationTimeRange|プレゼンテーション時間の範囲です。 このプロパティは、マニフェストの開始/終了ポイント、プレゼンテーション ウィンドウの長さ、ライブ開始位置をフィルタリングする目的で使用します。 <br/>詳細については、「[PresentationTimeRange](#PresentationTimeRange)」を参照してください。|
|tracks|トラックの選択条件です。 詳細については、「[tracks](#tracks)」を参照してください。|

### <a name="presentationtimerange"></a>PresentationTimeRange

このプロパティは、**アセット フィルター**で使用します。 このプロパティを**アカウント フィルター**で設定することはお勧めできません。

|Name|説明|
|---|---|
|**endTimestamp**|終了時間の絶対的境界です。 ビデオ オン デマンド (VOD) が対象となります。 ライブ プレゼンテーションでは暗黙的に無視され、プレゼンテーションが終了してストリームが VOD になったときに適用されます。<br/><br/>この値は、ストリームの絶対的な終了点を表します。 最も近い次の GOP 開始に丸められます。<br/><br/>プレイリスト (マニフェスト) をトリミングするには、startTimestamp と endTimestamp を使用します。 たとえば、startTimestamp を 40000000、endTimestamp を 100000000 とした場合、startTimestamp から endTimestamp までのメディアを含んだプレイリストが生成されます。 その境界をフラグメントがまたいだ場合、フラグメント全体がマニフェストに含められます。<br/><br/>この後の「**forceEndTimestamp**」の定義も参照してください。|
|**forceEndTimestamp**|ライブ フィルターが対象となります。<br/><br/>**forceEndTimestamp** は、**endTimestamp** が有効な値に設定されたかどうかを示すブール値です。 <br/><br/>この値が **true** である場合、**endTimestamp** の値を指定する必要があります。 指定しなかった場合、無効な要求が返されます。<br/><br/>たとえば、入力ビデオの 5 分の位置を起点としてストリームの最後まで持続するフィルターを定義したいとします。その場合、**forceEndTimestamp** を false に設定し、**endTimestamp** の設定は省略することになります。|
|**liveBackoffDuration**|ライブのみが対象となります。 このプロパティは、ライブ再生位置を定義するために使用します。 このルールを使用すると、ライブ再生位置を遅らせ、プレーヤーのためにサーバー側のバッファーを作成することができます。 LiveBackoffDuration は、ライブ位置を基準とします。 最大ライブ バックオフ期間は 60 秒です。|
|**presentationWindowDuration**|ライブが対象となります。 プレイリストにスライディング ウィンドウを適用するには、**presentationWindowDuration** を使用します。 たとえば、2 分のスライディング ウィンドウを適用するには、presentationWindowDuration を 1200000000 に設定します。 ライブ エッジの 2 分以内のメディアがプレイリストに含められます。 その境界をフラグメントがまたいだ場合、フラグメント全体がプレイリストに含められます。 最小プレゼンテーション ウィンドウ期間は 120 秒です。|
|**startTimestamp**|VOD またはライブ ストリームが対象となります。 この値は、ストリームの絶対的な開始点を表します。 この値は最も近い次の GOP 開始に丸められます。<br/><br/>プレイリスト (マニフェスト) をトリミングするには、**startTimestamp** と **endTimestamp** を使用します。 たとえば、startTimestamp を 40000000、endTimestamp を 100000000 とした場合、startTimestamp から endTimestamp までのメディアを含んだプレイリストが生成されます。 その境界をフラグメントがまたいだ場合、フラグメント全体がマニフェストに含められます。|
|**timescale**|VOD またはライブ ストリームが対象となります。 上記のタイムスタンプと期間によって使用されるタイムスケールです。 既定のタイムスケールは 10000000 です。 代替タイムスケールを使用できます。 既定値は 10000000 HNS (Hundred NanoSecond: 100 ナノ秒) です。|

### <a name="tracks"></a>tracks

動的に作成されたマニフェストに含めるべきストリーム (ライブまたはビデオ オン デマンド) のトラックに基づいて、一連のフィルター トラック プロパティ条件 (FilterTrackPropertyConditions) を指定します。 フィルターは、**AND** と **OR** の論理演算を使用して組み合わせます。

フィルター トラック プロパティ条件は、トラックの種類、値 (以下の表で説明します)、演算 (Equal、NotEqual) を記述したものです。 

|Name|説明|
|---|---|
|**Bitrate**|フィルタリングにトラックのビットレートを使用します。<br/><br/>推奨される値は、bps (1 秒あたりのビット数) で表したビットレートの範囲です。 たとえば、「0-2427000」と指定します。<br/><br/>注: 特定のビットレート値、たとえば 250000 (bps) を使用することもできますが、厳密なビットレートはアセットごとに変動するので、この方法はお勧めしません。|
|**FourCC**|フィルタリングにトラックの FourCC 値を使用します。<br/><br/>この値は、[RFC 6381](https://tools.ietf.org/html/rfc6381) で規定されたコーデック形式の最初の要素です。 現在は、次のコーデックがサポートされています。 <br/>ビデオ: "avc1"、"hev1"、"hvc1"<br/>オーディオ: "mp4a"、"ec-3"<br/><br/>アセットのトラックの FourCC 値を調べるには、[マニフェスト ファイルを取得して調査](#get-and-examine-manifest-files)します。|
|**言語**|フィルタリングにトラックの言語を使用します。<br/><br/>この値は、含めたい言語のタグです (RFC 5646 で規定)。 例: "en"。|
|**名前**|フィルタリングにトラックの名前を使用します。|
|**種類**|フィルタリングにトラックの種類を使用します。<br/><br/>"video"、"audio"、"text" のいずれかの値を指定できます。|

### <a name="example"></a>例

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>演奏フィルター処理

アセットは、複数のエンコード プロファイル (H.264 Baseline、H.264 高、AACL、AACH、Dolby Digital Plus) と複数の品質ビットレートにエンコードできます。 ただし、クライアント デバイスによっては、一部のアセットのプロファイルやビットレートをサポートしていない場合があります。 たとえば、古いバージョンの Android デバイスでは H.264 Baseline+AACL のみをサポートしています。 メリットが得られないデバイスに高いビットレートを送信すると、帯域幅とデバイスの計算が無駄になります。 このようなデバイスでは、表示用にスケールダウンするためだけに、取得した情報をすべてデコードしなくてはなりません。

動的マニフェストの場合、携帯電話、コンソール、HD/SD などのデバイス プロファイルを作成し、各プロファイルの一部としてトラックや品質を含めることができます。

![演奏フィルター処理例][renditions2]

次の例では、エンコーダーを使用して中間ファイル アセットを 7 つの ISO MP4 ビデオ演奏にエンコードしました (180p ～ 1080p)。 エンコード済みのアセットは、HLS、MPEG DASH、Smooth のいずれかのストリーミング プロトコルに動的にパッケージ化できます。  図の上部には、フィルターを使用しないアセット用の HLS マニフェストが表示されています (これには全 7 演奏が含まれています)。  左下には、"ott" という名前のフィルターが適用された HLS マニフェストが表示されています。 "ott" フィルターは、1 Mbps より低いビットレートをすべて削除するように指定されているので、下位 2 つの品質レベルが応答で除去されています。 右下には、"mobile" という名前のフィルターが適用された HLS マニフェストが表示されています。 "mobile" フィルターでは、2 つの 1080 p の演奏が除去されてしまうため、解像度が 720 p を超える演奏を削除するように指定します。

![演奏フィルター処理][renditions1]

## <a name="removing-language-tracks"></a>言語トラックを削除する
アセットには、英語、スペイン語、フランス語などの複数のオーディオ言語が含まれる場合があります。通常、Player SDK マネージャーには、既定のオーディオ トラックと、ユーザーによって選択された使用可能なオーディオ トラックがあります。 このような Player SDK の開発は、デバイス固有のプレーヤー フレームワークによってさまざまな実装が必要になるため困難です。 また、一部のプラットフォームでは Player API に制限があり、オーディオ選択機能が含まれません。このため、ユーザーは既定のオーディオ トラックを選択、変更できません。アセット フィルターを使用すれば、目的のオーディオ言語のみを含むフィルターを作成することで、動作を制御できます。

![言語トラックのフィルター処理][language_filter]

## <a name="trimming-start-of-an-asset"></a>アセットのトリミングを開始する
ほとんどのライブ ストリーミング イベントでは、実際のイベントの前にオペレータがいくつかのテストを行います。 たとえば、イベントの開始前に、"プログラムが間もなく開始します" などのスレートを追加する場合があります。 プログラムをアーカイブすると、テストとスレートのデータもアーカイブされ、プレゼンテーションに追加されます。 しかし、この情報をクライアントに表示すべきではありません。 動的マニフェストを使用することで、開始時刻フィルターを作成し、マニフェストから不要なデータを削除できます。

![トリミングの開始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>ライブ アーカイブからサブクリップ (ビュー) を作成する
多くのライブ イベントは長時間実行され、ライブ アーカイブに複数のイベントが含まれる場合があります。 ライブ イベントの終了後は、ブロードキャスタが、ライブ アーカイブを論理プログラムの開始と終了シーケンスに分割できます。 次に、ライブ アーカイブの後処理や個別のアセットの作成を行わずに、これらの仮想プログラムを個別に発行します (これでは CDN にキャッシュされた既存のフラグメントを利用できません)。 このような仮想プログラムの例として、フットボールやバスケットボールの試合のクオーター、野球のイニング、スポーツ プログラムの各イベントなどが挙げられます。

動的マニフェストでは、開始時刻と終了時刻を使用したフィルターを作成し、ライブ アーカイブ上に仮想ビューを作成できます。 

![サブクリップ フィルター][subclip_filter]

フィルター処理されたアセット:

![スキー][skiing]

## <a name="adjusting-presentation-window-dvr"></a>プレゼンテーション ウィンドウ (DVR) を調整する
現在、Azure Media Services では 5 分～ 25 時間の期間を構成できる循環アーカイブを提供しています。 マニフェストのフィルタ―処理を使用して、メディアを削除せずにアーカイブ上に DVR のローリングウィンドウを作成できます。 ブロードキャスタが、限定的な DVR ウィンドウを配信してライブ エッジとともに移動しつつ、規模の大きいアーカイブ ウインドウも確保しておく必要があるといったシナリオはよくあります。 ブロードキャスターは、クリップを強調表示するために DVR ウィンドウ外のデータを使用し、さまざまなデバイスに合わせて異なる DVR ウィンドウを用意する必要があります。 たとえば、ほとんどのモバイル デバイスでは大きな DVR ウィンドウに対応していません (DVR ウィンドウはモバイル デバイスでは 2 分、デスクトップ クライアントでは 1 時間使用できます)。

![DVR ウィンドウ][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (ライブ位置) を調整する
マニフェストのフィルター処理を使用して、ライブ プログラムのライブ エッジから数秒を削除できます。 フィルター処理により、ブロードキャスタはプレビュー公開ポイントでプレゼンテーションを視聴し、視聴者がストリームを受信する前の広告の挿入ポイントを作成できます (30 秒後退します)。 ブロードキャスタは、広告のチャンスの前にクライアントが情報を受信して処理するよう、これらの広告をクライアント フレームワークにプッシュできます。

広告のサポートに加えて、LiveBackoff 設定はビューアーの位置の調整に使用することもできます。これにより、クライアントの接続が安定せず、ライブ エッジに到達した場合でも、HTTP 404 または 412 エラーを発生させずにサーバーからフラグメントを取得できます。

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>複数のルールを 1 つのフィルターに組み合わせる
複数のフィルター処理ルールを 1 つのフィルターに組み合わせることができます。 たとえば、"範囲ルール" を定義してスレートをライブ アーカイブから削除したり、使用可能なビット レートをフィルターで除外することもできます。 複数のフィルター規則を適用した場合、最終的な結果は、すべての規則の共通部分になります。

![multiple-rules][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>複数のフィルターを結合する (フィルターの構成)

1 つの URL で複数のフィルターを結合することもできます。 

次のシナリオは、フィルターを結合する理由を示しています。

1. Android や iPAD などのモバイル デバイス用にビデオ品質をフィルター処理する必要があります (ビデオ品質を制限するため)。 不要な品質を取り除くために、デバイスのプロファイルに適したアカウント フィルターを作成します。 新たに関連付けを行わなくても、同じメディア サービス アカウントに含まれるすべてのアセットにアカウント フィルターを使用できます。 
2. また、資産の開始時刻と終了時刻を調整することもできます。 これを行うには、アセット フィルターを作成し、開始時刻と終了時刻を設定します。 
3. これらの両方のフィルターを結合する必要があります (結合しない場合、品質フィルタリングをトリミング フィルターに追加する必要がありますが、このようにするとフィルターがさらに使いづらくなります)。

フィルターを結合するには、フィルター名をマニフェスト/再生リスト URL にセミコロンで区切って設定する必要があります。 品質をフィルター処理する *MyMobileDevice* という名前のフィルターと、特定の開始時刻を設定する *MyStartTime* という名前のフィルターがあると想定します。 次のようにして、これらを結合できます。

最大 3 つのフィルターを結合できます。 

詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) をご覧ください。

## <a name="considerations-and-limitations"></a>考慮事項と制限事項

- VOD フィルターの場合、**forceEndTimestamp**、**presentationWindowDuration**、**liveBackoffDuration** の値は設定しないでください。 これらは、ライブ フィルターのシナリオでのみ使用します。 
- 動的マニフェストは GOP 境界 (キー フレーム) で動作するため、トリミングの正確度は GOP に応じます。 
- アカウント フィルターとアセット フィルターには同じフィルター名を使用できます。 アセット フィルターの方が優先順位が高く、アカウント フィルターをオーバーライドします。
- フィルターを更新する場合、ストリーミング エンドポイントでルールを更新するのに最大 2 分かかります。 いくつかのフィルターを使用してコンテンツが処理された場合 (プロキシと CDN にキャッシュされている)、プレイヤーでこれらのフィルターを更新すると失敗します。 フィルターを更新した後にキャッシュをクリアすることをお勧めします。 このオプションが利用できない場合は、別のフィルターを使用することを検討してください。
- マニフェストは、顧客が手動でダウンロードして厳密な startTimestamp とタイム スケールを解析する必要があります。
    
    - アセットのトラックのプロパティを調べるには、[マニフェスト ファイルを取得して調査](#get-and-examine-manifest-files)します。
    - アセット フィルターのタイムスタンプのプロパティを設定するための数式は次のとおりです。 <br/>startTimestamp = &lt;マニフェストにおける開始時間&gt; +  &lt;所定のフィルター開始時間 (秒)&gt; * タイムスケール


## <a name="next-steps"></a>次の手順

次の記事では、プログラムを使ってフィルターを作成する方法が紹介されています。  

- [REST API を使用したフィルターの作成](filters-dynamic-manifest-rest-howto.md)
- [.NET を使用したフィルターの作成](filters-dynamic-manifest-dotnet-howto.md)
- [CLI を使用したフィルターの作成](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
