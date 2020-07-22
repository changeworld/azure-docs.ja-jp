---
title: 任意の AI を利用したライブ ビデオの分析 - Azure
description: この記事では、IoT Edge で Live Video Analytics と統合できる IoT Edge モジュールを構築し、任意のコンピューター ビジョン モデルを使用してライブ ビデオを分析する方法について説明します。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182867"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>任意の AI を利用したライブ ビデオの分析

この記事では、IoT Edge で Live Video Analytics と統合できる IoT Edge モジュールを構築し、任意のコンピューター ビジョン モデルを使用してライブ ビデオを分析する方法について説明します。 

## <a name="pre-reading"></a>事前読み取り

[メディア グラフの概念](media-graph-concept.md)

## <a name="media-graph-extension"></a>メディア グラフ拡張機能

IoT Edge の Live Video Analytics では、グラフ拡張機能を使用して、メディア グラフ処理機能を独自のメディア分析コンポーネントに拡張できる機能拡張モデルを定義します。 分析コンポーネントでは、従来のイメージ処理手法またはコンピューター ビジョンの AI モデルを利用できます。 グラフ拡張機能を有効にするには、[HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor) ノードをメディア グラフに含めます。 HTTP 拡張プロセッサは、ユーザーが指定した HTTP エンドポイントにビデオ フレームをリレーし、それを介してコンポーネントへのインターフェイスとして機能します。 ローカルまたはリモートのエンドポイントに接続でき、必要に応じて認証と TLS 暗号化によって保護することができます。 また、プロセッサでは、ビデオ フレームをリレーする前に、オプションでスケーリングとエンコードを行うことができます。

ONNX、TensorFlow、PyTorch、などの任意の推論ランタイム、または独自の Docker コンテナーで、推定モデルを実行できます。 また、任意のプログラミング言語とライブラリを使用して、独自のコンテナーの HTTP サーバーでもイメージ推論機能を公開できます。 最適なパフォーマンスを得るには、推論コンテナーを Live Video Analytics の Edge モジュールと共にデプロイする必要があります。その後、グラフ トポロジに含まれる HTTP 拡張プロセッサを使用して推論コンテナーを起動します。
さらに、必要に応じて、[モーション検出プロセッサ](media-graph-concept.md#motion-detection-processor)と[フレーム レート フィルター プロセッサ](media-graph-concept.md#frame-rate-filter-processor)を HTTP 拡張プロセッサに追加することで、カスタム モジュールへ呼び出される頻度を調整できます。

次の図は、高レベルのデータ フローを示しています。

![エッジ デバイス](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

これにより、独自のビジネス ニーズに合わせて、任意の AI モデルを使用してビデオを分析することができます。 オープンソース コミュニティ、社内のデータ サイエンティスト、または専門の AI プロバイダーからの AI モデルを使用できます。

## <a name="media-graph-http-extension-contract-definitions"></a>メディア グラフ HTTP 拡張コントラクトの定義

このセクションでは、データ フローを定義する HTTP コントラクトを定義します。

### <a name="http-extensibility-protocol"></a>HTTP 拡張プロトコル  

HTTP コントラクトは次のように定義されます。

* モジュールは HTTP サーバーとして機能します。
* IoT Edge モジュールの Live Video Analytics は、HTTP クライアントとして機能します。

### <a name="request"></a>Request

Live Video Analytics モジュールからモジュールへの要求は、次のようになります。

| Key | 値 |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Accept|application/json、 */*|
|承認| Basic、Digest、ベアラー (カスタム ヘッダーのサポートを通じて)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length|本文の長さ (バイト単位)|
|User-Agent|Azure Media Services|
|Body|サポートされているコンテンツ タイプの 1 つでバイナリ エンコードされたイメージ バイト。|

#### <a name="example"></a>例

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Response

モジュールから Live Video Analytics モジュールへの応答は次のようになります

| Key | 値 |
|---|---|
|状態コード|200 OK - 推論の結果が見つかりました<br/>204 No Content - AI によってコンテンツが見つかりませんでした<br/>400 Bad Request - 想定されていません<br/>500 Internal Server Error - 想定されていません<br/>503 Server Busy - AMS は "Retry-After" ヘッダーに基づいて、またはケース ヘッダーが事前に設定されていない場合の既定の時間に基づいてバックオフされます。|
|Content-Type|application/json|
|Content-Length|    本文の長さ (バイト単位)|
|Body|単一の "inferences" プロパティを持つ JSON オブジェクト。|

#### <a name="example"></a>例

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

以下で定義した事前に定義されたスキーマに従って、有効な JSON ドキュメントを使用して応答を返すことを強くお勧めします。 これにより、他のコンポーネント、および Live Video Analytics モジュールに今後追加される可能性がある機能との相互運用性が向上します。

モジュールによってコンテンツ タイプが "application/json" でない応答が返される場合、Live Video Analytics では、メッセージが base 64 コンテンツとしてエンコードされ、非透過の JSON ペイロードとしてシリアル化されます。

モジュールによってコンテンツ タイプが "application/json" の応答が返されたが、JSON スキーマが[下記の推定メタデータ スキーマに従っていない](#inference-metadata-schema)場合、メッセージ ペイロードはパイプラインを介して転送されますが、相互運用性は低下します。

> [!NOTE]
> モジュールが結果を生成しない場合、応答本文が空の HTTP 204 状態コード (No Content) が返されます。 Live Video Analytics はこれを空の結果として認識し、パイプライン全体でイベントを転送しません。

### <a name="inference-metadata-schema"></a>推論メタデータ スキーマ

各推論オブジェクトは、次のスーパーセット スキーマに従います。

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>データ コントラクト - クラス階層

![データ コントラクト - クラス階層](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>例  

次の例には、すべてのサポートされている推論の種類を含む単一のイベントが含まれています。

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>HTTP 拡張モジュールのサンプル

いくつかの HTTP 拡張モジュールのサンプルは、[Live Video Analytics GitHub リポジトリ](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)にあります。 これらの[ビデオ分析サンプル](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)の 1 つは [YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) モデルを使用して、オブジェクト検出のための IoT Edge モジュールを構築する方法を示しています。 もう 1 つの[ビデオ分析サンプル](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny)は、YOLOv3 ONNX モデルの軽量バージョンである、Tiny YOLOv3 を使用する方法を示しています。 同じ方法で、任意の AI モデルを使用して独自のモジュールを構築できます。

## <a name="next-steps"></a>次のステップ

[[トラブルシューティング]](troubleshoot-how-to.md)
