---
title: Azure Media Services でのフィルターの定義
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372960"
---
# <a name="define-account-filters-and-asset-filters"></a>アカウント フィルターとアセット フィルターの定義  

コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、資産の既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、コンテンツのアセット フィルターとアカウント フィルターを定義することができます。 

フィルターは、次のような操作を顧客に許可するサーバー側のルールです。 

- ビデオ全体を再生するのではなく、ビデオの 1 つのセクションのみを再生する。 例: 
  - マニフェストを減らして、ライブ イベントのサブクリップのみを表示する場合 (「サブクリップ フィルター処理」)、または
  - ビデオの開始をトリミングする場合 (「ビデオのトリミング」)
- コンテンツの再生に使用するデバイスでサポートされている演奏や言語のトラックのみを指定して配信する ("演奏フィルタ―処理")。 
- プレゼンテーション ウィンドウ (DVR) を調整し、プレーヤーの DVR ウィンドウの長さを限定する ("プレゼンテーション ウィンドウの調整")。

Media Services には、定義済みのフィルターに基づいた[動的マニフェスト](filters-dynamic-manifest-overview.md)が用意されています。 フィルターを定義すると、クライアントからストリーミング URL で使用できるようになります。 フィルターは、アダプティブ ビットレート ストリーミング プロトコル(Apple HTTP Live Streaming (HLS)、MPEG DASH、Smooth Streaming) に適用できます。

次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>フィルターを定義する

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
|**liveBackoffDuration**|ライブのみが対象となります。 このプロパティは、ライブ再生位置を定義するために使用します。 このルールを使用すると、ライブ再生位置を遅らせ、プレーヤーのためにサーバー側のバッファーを作成することができます。 LiveBackoffDuration は、ライブ位置を基準とします。 最大ライブ バックオフ期間は 300 秒です。|
|**presentationWindowDuration**|ライブが対象となります。 プレイリストにスライディング ウィンドウを適用するには、**presentationWindowDuration** を使用します。 たとえば、2 分のスライディング ウィンドウを適用するには、presentationWindowDuration を 1200000000 に設定します。 ライブ エッジの 2 分以内のメディアがプレイリストに含められます。 その境界をフラグメントがまたいだ場合、フラグメント全体がプレイリストに含められます。 最小プレゼンテーション ウィンドウ期間は 60 秒です。|
|**startTimestamp**|VOD またはライブ ストリームが対象となります。 この値は、ストリームの絶対的な開始点を表します。 この値は最も近い次の GOP 開始に丸められます。<br/><br/>プレイリスト (マニフェスト) をトリミングするには、**startTimestamp** と **endTimestamp** を使用します。 たとえば、startTimestamp を 40000000、endTimestamp を 100000000 とした場合、startTimestamp から endTimestamp までのメディアを含んだプレイリストが生成されます。 その境界をフラグメントがまたいだ場合、フラグメント全体がマニフェストに含められます。|
|**timescale**|VOD またはライブ ストリームが対象となります。 上記のタイムスタンプと期間によって使用されるタイムスケールです。 既定のタイムスケールは 10000000 です。 代替タイムスケールを使用できます。 既定値は 10000000 HNS (Hundred NanoSecond: 100 ナノ秒) です。|

### <a name="tracks"></a>tracks

動的に作成されたマニフェストに含めるべきストリーム (ライブまたはビデオ オン デマンド) のトラックに基づいて、一連のフィルター トラック プロパティ条件 (FilterTrackPropertyConditions) を指定します。 フィルターは、**AND** と **OR** の論理演算を使用して組み合わせます。

フィルター トラック プロパティ条件は、トラックの種類、値 (以下の表で説明します)、演算 (Equal、NotEqual) を記述したものです。 

|Name|説明|
|---|---|
|**Bitrate**|フィルタリングにトラックのビットレートを使用します。<br/><br/>推奨される値は、bps (1 秒あたりのビット数) で表したビットレートの範囲です。 たとえば、「0-2427000」と指定します。<br/><br/>注: 特定のビットレート値、たとえば 250000 (bps) を使用することもできますが、厳密なビットレートはアセットごとに変動するので、この方法はお勧めしません。|
|**FourCC**|フィルタリングにトラックの FourCC 値を使用します。<br/><br/>この値は、[RFC 6381](https://tools.ietf.org/html/rfc6381) で規定されたコーデック形式の最初の要素です。 現在は、次のコーデックがサポートされています。 <br/>ビデオ: "avc1"、"hev1"、"hvc1"<br/>オーディオ: "mp4a"、"ec-3"<br/><br/>アセットのトラックの FourCC 値を調べるには、[マニフェスト ファイルを取得して調査](#get-and-examine-manifest-files)します。|
|**Language**|フィルタリングにトラックの言語を使用します。<br/><br/>この値は、含めたい言語のタグです (RFC 5646 で規定)。 例: "en"。|
|**Name**|フィルタリングにトラックの名前を使用します。|
|**Type**|フィルタリングにトラックの種類を使用します。<br/><br/>"video"、"audio"、"text" のいずれかの値を指定できます。|

## <a name="example"></a>例

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

## <a name="next-steps"></a>次の手順

次の記事では、プログラムを使ってフィルターを作成する方法が紹介されています。  

- [REST API を使用したフィルターの作成](filters-dynamic-manifest-rest-howto.md)
- [.NET を使用したフィルターの作成](filters-dynamic-manifest-dotnet-howto.md)
- [CLI を使用したフィルターの作成](filters-dynamic-manifest-cli-howto.md)

