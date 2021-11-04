---
title: サポートされる空間分析操作 - Azure
description: このリファレンス記事では、Azure Video Analyzer でサポートされる空間分析操作のさまざまなプロパティについて詳しく説明します。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 11062ebbe48e8c7e2451ff448770055073d7e792
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560149"
---
# <a name="supported-spatial-analysis-operations"></a>サポートされる空間分析操作

空間分析により、カメラ デバイスから取得したリアルタイム ストリーミング ビデオの分析が可能になります。 構成するカメラ デバイスごとに、操作を実行すると Azure Video Analyzer に送信される JSON メッセージの出力ストリームが生成されます。 

Video Analyzer では、次の空間分析操作が実装されます。 

| 操作識別子| 説明|
|---------|---------|
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation | 人がゾーンに出入りするときに _personZoneEnterExitEvent_ イベントを発行し、交差したゾーンの番号付き側に方向情報を提供します。 人がゾーンを出るときに _personZoneDwellTimeEvent_ を発行し、方向情報と人がゾーン内で過ごしたミリ秒数を提供します。 |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation | 人がカメラの視野内の指定されたラインを越えた時点を追跡します。  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation | 人が距離ルールに違反した時点を追跡します。  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation | カメラの視野内の指定されたゾーン内の人数をカウントします。 PersonCount によって正確な合計を記録するには、ゾーンを 1 台のカメラで完全にカバーする必要があります。  |
| Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation | 上記にあるすべてのシナリオを実行する場合に使用できる汎用的な操作。 このオプションは、同じカメラで複数のシナリオを実行したり、システム リソース (GPU など) をより効率的に使用したりする場合に便利です。 |



## <a name="person-zone-crossing"></a>Person Zone Crossing

**操作識別子**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation`

GitHub のサンプルから、[Person Zone Crossing Operation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json) の例を参照してください。

#### <a name="parameters"></a>パラメーター:

| 名前                      | 種類    | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | ゾーンのリスト。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                     | string  | このゾーンのフレンドリ名。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 その人がゾーン内のこのピクセル数よりも大きい場合に threshold float イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。 |
| `eventType`                 | string  | cognitiveservices.vision.spatialanalysis-personcrossingpolygon の場合、これは `zonecrossing` または `zonedwelltime` である必要があります。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `trigger`                   | string  | イベントを送信するためのトリガーの種類。 サポートされている値: "event": だれかがゾーンに入ったときまたはゾーンから出たときに発生します。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `focus`                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 既定値は 16 です。 これは、最大の精度を実現するために推奨される値です。                                                                                                |
| `enableFaceMaskClassifier`  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | すべての空間分析操作の TRACKER_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

### <a name="orientation-parameter-settings"></a>向きパラメーターの設定

DETECTOR_NODE_CONFIG パラメーター設定を使用して向きの計算を構成できます
```json
{
    "enable_orientation": true,
}

```
| 名前                      | 種類    | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_orientation`                     | bool    | 検出された人の向きを計算するかどうかを示します。 `enable_orientation` は既定で True に設定されています。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
### <a name="speed-parameter-settings"></a>速度パラメーターの設定

TRACKER_NODE_CONFIG パラメーター設定を使用して、速度計算を構成できます
```json
{
    "enable_speed": true,
}

```
| 名前                      | 種類    | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_speed`                     | bool    | 検出された人の速度を計算するかどうかを示します。 `enable_speed` は既定で True に設定されています。 速度と向きの両方に最適な推定値を設定することを強くお勧めします。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

#### <a name="output"></a>Output:
```json
{
  "body": {
    "timestamp": 147026846756730,
    "inferences": [
      {
        "type": "entity",
        "inferenceId": "8e8269c1a9584b3a8f16a3cd7a2cd45a",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.9511422
          },
          "box": {
            "l": 0.59845686,
            "t": 0.35958588,
            "w": 0.11951797,
            "h": 0.50172085
          }
        },
        "extensions": {
          "centerGroundPointY": "0.0",
          "footprintY": "inf",
          "centerGroundPointX": "0.0",
          "mappedImageOrientation": "inf",
          "groundOrientationAngle": "inf",
          "footprintX": "inf",
          "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
          "speed": "0.0"
        }
      },
      {
        "type": "entity",
        "inferenceId": "c54c9f92dd0d442b8d1840756715a5c7",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.92762595
          },
          "box": {
            "l": 0.8098704,
            "t": 0.47707137,
            "w": 0.18019487,
            "h": 0.48659682
          }
        },
        "extensions": {
          "footprintY": "inf",
          "groundOrientationAngle": "inf",
          "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
          "mappedImageOrientation": "inf",
          "speed": "0.0",
          "centerGroundPointX": "0.0",
          "centerGroundPointY": "0.0",
          "footprintX": "inf"
        }
      },
      {
        "type": "event",
        "inferenceId": "aad2778756a94afd9055fdbb3a370d62",
        "relatedInferences": [
          "8e8269c1a9584b3a8f16a3cd7a2cd45a"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
            "zone": "retailstore",
            "status": "Enter"
          }
        }
      },
      {
        "type": "event",
        "inferenceId": "e30103d3af28485688d7c77bbe10b5b5",
        "relatedInferences": [
          "c54c9f92dd0d442b8d1840756715a5c7"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
            "status": "Enter",
            "zone": "retailstore"
          }
        }
      }
    ]
  }
```

## <a name="person-line-crossing"></a>Person Line Crossing

**操作識別子**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation`

GitHub のサンプルから、[Person Line Crossing Operation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json) の例を参照してください。

#### <a name="parameters"></a>パラメーター:

| 名前                      | 種類    | 説明                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lines`                     | list    | ラインのリスト。                                                                                                                                                                                                                                                                |
| `name`                      | string  | このラインのフレンドリ名。                                                                                                                                                                                                                                                  |
| `line`                      | string  | 各値ペアは、ラインの開始点と終了点を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。                            |
| `start`                      | 値のペア  | ラインの始点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。                            |
| `end`                      | 値のペア  | ラインの終点の x、y 座標。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。                            |
| `type`                     | string  | これは `linecrossing` である必要があります。 |
| `trigger`                     | string  | イベントを送信するためのトリガーの種類。 サポートされている値: "event": だれかがラインを越えたときに発生します。|
| `outputFrequency`           | INT     | イベントが送信される頻度。 outputFrequency = X の場合、X おきにイベントが送信されます。たとえば、 outputFrequency = 2 は、イベントが 1 つおきに出力されることを意味します。 outputFrequency は、イベントと間隔の両方に適用されます。                                                       |
| `focus`                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。 |
| `threshold`                 | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 既定値は 16 です。 これは、最大の精度を実現するために推奨される値です。                                                                                                |
| `enableFaceMaskClassifier`  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。          |
| `detectorNodeConfiguration` | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                      |
| `trackerNodeConfiguration` | string  | すべての空間分析操作の TRACKER_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="person-distance"></a>Person Distance

**操作識別子**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation`

GitHub のサンプルから、[Person Distance Operation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json) の例を参照してください。

#### <a name="parameters"></a>パラメーター:

| 名前                      | 種類    | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | ゾーンのリスト。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | string  | このゾーンのフレンドリ名。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 その人がゾーン内のこのピクセル数よりも大きい場合に threshold float イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。 |
| `trigger`           | string     | イベントを送信するためのトリガーの種類。 サポートされている値は、event (カウントが変わったときにイベントを送信する場合)、または interval (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。                                                                                                                                                                                                                                                                                                                                                     |
| `focus`                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。                                                                                                                                                                                                                                                                                               |
| `threshold`                | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `outputFrequency`           | INT     | イベントが送信される頻度。 outputFrequency = X の場合、X おきにイベントが送信されます。たとえば、 outputFrequency = 2 は、イベントが 1 つおきに出力されることを意味します。 outputFrequency は、イベントと間隔の両方に適用されます。                                                                                                                                                                                                                                                                                                                                                     |
| `minimumDistanceThreshold`  | float   | 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `maximumDistanceThreshold`  | float   | 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `aggregationMethod`         | string  | 個人距離の結果を集計するためのメソッド。 aggregationMethod は、モードと平均の両方に適用できます。                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `enableFaceMaskClassifier`  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | すべての空間分析操作の TRACKER_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

## <a name="person-count"></a>Person Count

**操作識別子**: `Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation`

GitHub のサンプルから、[Person Count Operation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json) の例を参照してください。

#### <a name="parameters"></a>パラメーター:

| 名前                      | 種類    | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | ゾーンのリスト。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | string  | このゾーンのフレンドリ名。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 その人がゾーン内のこのピクセル数よりも大きい場合に threshold float イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。 |
| `outputFrequency`           | INT     | イベントが送信される頻度。 outputFrequency = X の場合、X おきにイベントが送信されます。たとえば、 outputFrequency = 2 は、イベントが 1 つおきに出力されることを意味します。 outputFrequency は、イベントと間隔の両方に適用されます。                                                                                                                                                                                                                                                                                                                                                     |
| `trigger`                   | string  | イベントを送信するためのトリガーの種類。 サポートされている値は、event (カウントが変わったときにイベントを送信する場合)、または interval (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。                                                                                                                                                                                                                                                                                                                                                           |
| `focus`                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `enableFaceMaskClassifier`  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | すべての空間分析操作の TRACKER_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

## <a name="custom-operation"></a>カスタム操作

**操作識別子**: `Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation`

GitHub のサンプルから、[Custom Operation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json) の例を参照してください。

#### <a name="parameters"></a>パラメーター:

| 名前                   | 種類   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | string | 操作の JSON 表現。 |

#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```
