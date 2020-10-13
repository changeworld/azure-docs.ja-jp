---
title: 空間分析操作
titleSuffix: Azure Cognitive Services
description: 空間分析操作。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 80f0d29de6b3013ad02ed1a5d34bebdf81a8766b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628215"
---
# <a name="spatial-analysis-operations"></a>空間分析操作

空間分析により、カメラ デバイスからのリアルタイム ストリーミング ビデオの分析が可能になります。 構成したカメラ デバイスごとに、空間分析の操作によって、Azure IoT Hub のインスタンスに送信される JSON メッセージの出力ストリームが生成されます。 

空間分析コンテナーは、次の操作を実装します。

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br> 人がゾーンを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |

上記のすべての操作は、処理中のビデオ フレームを視覚化する機能を備えた `.debug` バージョンでも使用できます。 ビデオ フレームとイベントの視覚化を有効にするには、ホスト コンピューターで `xhost +` を実行する必要があります。

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br> 人がゾーンを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |

空間分析は、[Live Video Analytics](https://aka.ms/lva-spatial-analysis) でビデオ AI モジュールとして実行することもできます。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 操作識別子| 説明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | カメラの視野内の指定されたゾーン内の人数をカウントします。 <br> 初期 _personCountEvent_ イベントを生成し、人数が変わると、_personCountEvent_ イベントを生成します。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br>人がラインを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。 <br> 人がゾーンを越えると、_personLineEvent_ イベントを生成し、方向情報を提供します。 |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | 人が距離ルールに違反した時点を追跡します。 <br> 各距離違反が発生した場所で _personDistanceEvent_ を定期的に生成します。 |

Live Video Analytics の操作は、処理中のビデオ フレームを視覚化する機能を備えた `.debug` バージョン (例: cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug) でも使用できます。 ビデオ フレームとイベントの視覚化を有効にするには、ホスト コンピューターで `xhost +` を実行する必要があります。

> [!IMPORTANT]
> コンピューター ビジョン AI モデルは、ビデオ映像で人の存在を検出してその位置を特定し、人体を囲む境界ボックスを使用して出力します。 これらの AI モデルは、顔の検出または個人の ID や人口統計情報の検出を試みることはありません。

これらの各空間分析操作に必要なパラメーターを次に示します。

| 操作のパラメーター| 説明|
|---------|---------|
| 操作 ID | 上記の表の操作識別子。|
| enabled | ブール値: true または false。|
| VIDEO_URL| カメラ デバイスの RTSP URL (例: `rtsp://username:password@url`)。 空間分析では、RTSP、http、または mp4 を使用して H.264 でエンコードされたストリームをサポートしています。 |
| VIDEO_SOURCE_ID | カメラ デバイスまたはビデオ ストリームのフレンドリ名。 これは、イベントの JSON 出力と共に返されます。|
| VIDEO_IS_LIVE| カメラ デバイスの場合は true、録画されたビデオの場合は false。|
| VIDEO_DECODE_GPU_INDEX| ビデオ フレームをデコードする GPU。 既定では 0 です。 `VICA_NODE_CONFIG`、`DETECTOR_NODE_CONFIG` などの他のノード構成の `gpu_index` と同じである必要があります。|
| DETECTOR_NODE_CONFIG | 検出ノードを実行する GPU を示す JSON。 `"{ \"gpu_index\": 0 }",` という形式にする必要があります。|
| SPACEANALYTICS_CONFIG | 後述するゾーンとラインの JSON 構成。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>cognitiveservices.vision.spatialanalysis-personcount のゾーン構成

 これは、ゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。多角形ポイントは、正規化された座標 (0-1) に基づいています。左上隅が (0.0, 0.0)、右下隅が (1.0, 1.0) になります。   
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcount** の場合、これは `count` である必要があります。|
| `trigger` | string| イベントを送信するためのトリガーの種類。 サポートされている値は、`event` (カウントが変わったときにイベントを送信する場合)、または `interval` (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。
| `interval` | string| イベントが発生する前に人数が集計される時間 (秒単位)。 操作では、一定の頻度でシーンの分析が続行され、その間隔内で最も一般的な人数が返されます。 集計間隔は、`event` と `interval` の両方に適用できます。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>cognitiveservices.vision.spatialanalysis-personcrossingline のライン構成

これは、ラインを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には、交差する複数のラインを構成できます。

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `lines` | list| ラインのリスト。|
| `name` | string| このラインのフレンドリ名。|
| `line` | list| ラインの定義。 これは、"入る" と "出る" を理解できるようにするための方向線です。|
| `start` | 値のペア| ラインの始点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 |
| `end` | 値のペア| ラインの終点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 |
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcrossingline** の場合、これは `linecrossing` である必要があります。|
|`trigger`|string|イベントを送信するためのトリガーの種類。<br>サポートされている値: "event": だれかがラインを越えたときに発生します。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon のゾーン構成

これは、ゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** の場合、これは `enter` または `exit` である必要があります。|
| `trigger`|string|イベントを送信するためのトリガーの種類。<br>サポートされている値: "event": だれかがゾーンに入ったときまたはゾーンから出たときに発生します。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>cognitiveservices.vision.spatialanalysis-persondistance のゾーン構成

これは、**cognitiveservices.vision.spatialanalysis-persondistance** のゾーンを構成する SPACEANALYTICS_CONFIG パラメーターの JSON 入力の例です。 この操作には複数のゾーンを構成できます。

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `zones` | list| ゾーンのリスト。 |
| `name` | string| このゾーンのフレンドリ名。|
| `polygon` | list| 各値のペアは、多角形の頂点の x、y を表します。 多角形は人数をカウントする領域を表し、人と人との距離が測定されます。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 
| `threshold` | float| AI モデルの信頼度がこの値以上の場合に、イベントが送信されます。 |
| `type` | string| **cognitiveservices.vision.spatialanalysis-persondistance** の場合、これは `people_distance` である必要があります。|
| `trigger` | string| イベントを送信するためのトリガーの種類。 サポートされている値は、`event` (カウントが変わったときにイベントを送信する場合)、または `interval` (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。
| `interval` | string | イベントが発生する前に違反数が集計される時間 (秒単位)。 集計間隔は、`event` と `interval` の両方に適用できます。|
| `output_frequency` | INT | イベントが送信される頻度。 `output_frequency` = X の場合、X おきにイベントが送信されます。たとえば、 `output_frequency` = 2 は、1 つおきにイベントが送信されることを意味します。 output_frequency は、`event` と `interval` の両方に適用できます。|
| `minimum_distance_threshold` | float| 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。|
| `maximum_distance_threshold` | float| 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。|

これは、**cognitiveservices.vision.spatialanalysis-persondistance** のゾーンを構成する DETECTOR_NODE_CONFIG パラメーターの JSON 入力の例です。

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| 名前 | Type| 説明|
|---------|---------|---------|
| `gpu_index` | string| この操作が実行される GPU インデックス。|
| `do_calibration` | string | 調整がオンになっていることを示します。 **cognitiveservices.vision.spatialanalysis-persondistance** が正しく機能するには、`do_calibration` が true である必要があります。|

ゾーンとラインの構成については、[カメラの配置](spatial-analysis-camera-placement.md)のガイドラインをご覧ください。

## <a name="spatial-analysis-operation-output"></a>空間分析操作の出力

各操作のイベントは、JSON 形式で Azure IoT Hub に送信されます。

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcount の AI 分析情報の JSON 形式

この操作によって出力されるイベントのサンプル JSON。

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| "Enter" または "Exit"|
| `side` | INT| 人が越えた多角形の辺の数|
| `zone` | string | 越えられたゾーンを表す多角形の "name" フィールド|
| `trigger` | string| SPACEANALYTICS_CONFIG の `trigger` の値に応じて、トリガーの種類は "event" または "interval" になります。|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|
| `cameraCallibrationInfo` | collection | 値のコレクション|
| `status` | string | 地面に対するカメラ調整が "Complete" であるかどうかを示します。|
| `cameraHeight` | float | 地面からのカメラの高さ (フィート単位)。 これは自動調整から推論されます。 |
| `focalLength` | float | カメラの焦点距離 (ピクセル単位)。 これは自動調整から推論されます。 |
| `tiltUpAngle` | float | 垂直線からのカメラの傾斜角度。 これは自動調整から推論されます。|

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingline の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| ラインを越える方向 ("CrossLeft" または "CrossRight")|
| `zone` | string | 越えられたラインの "name" フィールド|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|


> [!IMPORTANT]
> AI モデルは、人がカメラに顔を向けているか、カメラから顔をそらしているかに関係なく、その人を検出します。 AI モデルでは、顔検出や顔認識は実行されず、生体認証情報も生成されません。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `trackinId` | string| 検出された人の一意識別子|
| `status` | string| 多角形を越える方向 ("Enter" または "Exit")|
| `zone` | string | 越えられたゾーンを表す多角形の "name" フィールド|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>cognitiveservices.vision.spatialanalysis-persondistance の AI 分析情報の JSON 形式

この操作によって出力される検出のサンプル JSON。

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| イベント フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| イベント ID|
| `type` | string| イベントの種類|
| `detectionsId` | array| このイベントをトリガーした人検出の一意識別子のサイズ 1 の配列|
| `properties` | collection| 値のコレクション|
| `personCount` | INT| イベントの生成時に検出された人数|
| `averageDistance` | float| 検出されたすべての人の間の平均距離 (フィート単位)|
| `minimumDistanceThreshold` | float| 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。|
| `maximumDistanceThreshold` | float| 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。|
| `eventName` | string| イベント名は、`TooClose` (`minimumDistanceThreshold` に違反している場合)、`TooFar` (`maximumDistanceThreshold` に違反している場合)、または `unknown` (自動調整が完了していない場合) です。|
| `distanceViolationPersonCount` | INT| `minimumDistanceThreshold` または `maximumDistanceThreshold` の違反で検出された人数|
| `zone` | string | 人と人との間の距離について監視されていたゾーンを表す多角形の "name" フィールド|
| `trigger` | string| SPACEANALYTICS_CONFIG の `trigger` の値に応じて、トリガーの種類は "event" または "interval" になります。|

| Detections フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| 検出 ID|
| `type` | string| 検出の種類|
| `region` | collection| 値のコレクション|
| `type` | string| 領域の種類|
| `points` | collection| 領域の種類が RECTANGLE の場合の、左上と右下のポイント |
| `confidence` | float| アルゴリズムの信頼度|
| `centerGroundPoint` | 2 つの浮動小数点値| 地面上での人の推論された位置の座標の `x` および `y` 値 (フィート単位)。 `x` は、地面に投影されたカメラ画像平面に垂直なカメラからの距離です (フィート単位)。 `y` は、地面に投影された画像平面に平行なカメラからの距離です (フィート単位)。|

| SourceInfo フィールド名 | Type| 説明|
|---------|---------|---------|
| `id` | string| Camera ID (カメラ ID)|
| `timestamp` | 日付| JSON ペイロードが生成された UTC 日付|
| `width` | INT | ビデオ フレームの幅|
| `height` | INT | ビデオ フレームの高さ|
| `frameId` | INT | フレーム識別子|
| `cameraCallibrationInfo` | collection | 値のコレクション|
| `status` | string | 地面に対するカメラ調整が "Complete" であるかどうかを示します。|
| `cameraHeight` | float | 地面からのカメラの高さ (フィート単位)。 これは自動調整から推論されます。 |
| `focalLength` | float | カメラの焦点距離 (ピクセル単位)。 これは自動調整から推論されます。 |
| `tiltUpAngle` | float | 垂直線からのカメラの傾斜角度。 これは自動調整から推論されます。|


## <a name="use-the-output-generated-by-the-container"></a>コンテナーによって生成された出力を使用する

空間分析の検出またはイベントをアプリケーションに統合できます。 検討すべきいくつかの方法を次に示します。 

* 選択したプログラミング言語の Azure Event Hub SDK を使用して、Azure IoT Hub エンドポイントに接続し、イベントを受信します。 詳細については、「[デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)」をご覧ください。 
* イベントを他のエンドポイントに送信したり、イベントをデータ ストレージに保存したりするには、Azure IoT Hub の**メッセージ ルーティング**を設定します。 詳細については、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)に関する記事をご覧ください。 
* 到着したイベントをリアルタイムで処理し、視覚化を作成する Azure Stream Analytics ジョブを設定します。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>空間分析操作を大規模に展開する (複数のカメラ)

GPU のパフォーマンスと使用率を最大限に引き出すには、グラフ インスタンスを使用して複数のカメラに空間分析操作を展開できます。 次に示すのは、5 つのカメラで `cognitiveservices.vision.spatialanalysis-personcount` 操作を実行するためのサンプルです。

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| 名前 | 種類| 説明|
|---------|---------|---------|
| `batch_size` | INT | 操作で使用されるカメラの数を示します。 |

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
