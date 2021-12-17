---
title: Azure Video Analyzer を使用して動きを検出し、ビデオを録画する
description: このクイック スタートでは、Azure Video Analyzer エッジ モジュールを使用して、ライブ ビデオ ストリーム内の動きを検出し、Video Analyzer アカウントにビデオを録画する方法を示します。
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48c41b812a4cdbd416d0dd146aba938cce419864
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484207"
---
# <a name="quickstart-detect-motion-record-video-to-video-analyzer"></a>クイック スタート: 動きを検出し、Video Analyzer にビデオを録画する

[!INCLUDE [header](includes/edge-env.md)]

この記事では、[イベントベースの録画](../event-based-video-recording-concept.md)に Azure Video Analyzer エッジ モジュールを使用する手順について説明します。 IoT Edge デバイスとして Azure 上の Linux VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。 このビデオ ストリームで、動くオブジェクトが存在するかどうかが分析されます。 動きが検出されると、イベントが Azure IoT Hub に送信され、ビデオ ストリームの関連する部分が Video Analyzer アカウントの[ビデオ リソース](../terminology.md#video)として記録されます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、無料の[アカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

デプロイには約 **20 分** かかります。 完了すると、次のような特定の Azure リソースが Azure サブスクリプションにデプロイされます。
1. **Video Analyzer アカウント** - この [クラウド サービス](../overview.md)は、Video Analyzer エッジ モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます。
1. **ストレージ アカウント** - 録画されたビデオとビデオ分析の格納用です。
1. **マネージド ID** - これは、上記のストレージ アカウントへのアクセスを管理するために使用されるユーザー割り当て[マネージド ID]../../../active-directory/managed-identities-azure-resources/overview.md) です。
1. **仮想マシン** - これは、シミュレートされたエッジ デバイスとして機能する仮想マシンです。
1. **IoT Hub** - IoT アプリケーションと IoT Edge モジュール、さらにそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

詳細については、[こちら](https://github.com/Azure/video-analyzer/tree/main/setup)を参照してください。

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

上記のデプロイによって作成された仮想マシンには、複数の MKV ファイルがあります。  これらのファイルの 1 つは `lots_015.mkv` と呼ばれます。 次の手順では、このビデオ ファイルを使用して、このチュートリアル向けにライブ ストリームをシミュレートします。

[VLC プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを使用してこれを起動し、`Ctrl+N` を押して[駐車場のビデオ サンプル](https://avamedia.blob.core.windows.net/public/lots_015.mkv) リンクを貼り付けると再生を開始できます。 だいたい 5 秒の地点で、白の車が駐車場を通って移動します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

下の手順を完了すると、Video Analyzer エッジ モジュールを使用してその車の動きを検出し、その 5 秒の地点前後で始まるビデオ クリップが録画されます。 

次の図は、フロー全体を視覚的に表したものです。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/detect-motion-record-video-clips-cloud/topology.svg" alt-text="モーション イベントに基づいたビデオ リソースへのイベントベースのビデオ記録":::

## <a name="set-up-your-development-environment"></a>開発環境を設定する

### <a name="obtain-your-iot-hub-connection-string"></a>IoT ハブの接続文字列を取得する

1. Azure portal で、上記の設定手順の一環として作成した IoT ハブに移動します
1. 左側のナビゲーションで **[共有アクセス ポリシー]** オプションを探し、それをクリックします。
1. **iothubowner** という名前のポリシーをクリックします
1. **プライマリ接続文字列** をコピーします。これは `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX` のように表示されます

### <a name="connect-to-the-iot-hub"></a>IoT ハブに接続します

1. Visual Studio Code を開き、 **[表示]**  >  **[エクスプローラー]** を選択します。 または、Ctrl + Shift + E キーを押します。
1. **[エクスプローラー]** タブの左下隅で、 **[Azure IoT Hub]** を選択します。
1. **[その他のオプション]** アイコンを選択して、コンテキスト メニューを表示します。 次に、 **[Set IoT Hub Connection String]\(IoT Hub 接続文字列を設定する\)** を選択します。
1. 入力ボックスが表示されたら、IoT Hub 接続文字列を入力します。
1. 30 秒ほど経過したら、左下のセクションで Azure IoT Hub を更新します。 次のモジュールがデプロイされているエッジ デバイス `avasample-iot-edge-device` が表示されます。
    * Video Analyzer エッジ モジュール (モジュール名 **avaedge**)
    * RTSP シミュレーター (モジュール名: **rtspsim**)


> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="[モジュール] ノードを展開する":::

> [!TIP]
> エッジ デバイス (ARM64 デバイスなど) に自分で [Video Analyzer を手動でデプロイした](deploy-iot-edge-device.md)場合、Azure IoT Hub でそのデバイスの下に、そのモジュールが表示されます。 そのモジュールを選択し、以下の残りの手順に従います。

### <a name="prepare-to-monitor-the-modules"></a>モジュールの監視の準備をする 

このクイックスタートを実行すると、イベントが IoT ハブに送信されます。 それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、**Azure IoT Hub** を検索します。
1. 右クリックして、 **[拡張機能の設定]** を選択します。
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="[拡張機能の設定] を選択する":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="詳細メッセージの表示":::
1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある **[Azure IoT Hub]** を探します。
1. **[デバイス]** ノードを展開します。
1. `avasample-iot-edge-device` を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="use-direct-method-calls-to-analyze-live-video"></a>ダイレクト メソッド呼び出しを使用してライブ ビデオを分析する

この時点で、Video Analyzer エッジ モジュールによって公開されるダイレクト メソッドを呼び出して、ライブ ビデオ ストリームを分析できます。 [Video Analyzer のダイレクト メソッド](direct-methods.md)に関する記事を読み、このモジュールによって提供されるすべてのダイレクト メソッドを確認してください。

### <a name="enumerate-pipeline-topologies"></a>パイプライン トポロジを列挙する

この手順により、モジュール内のすべての[パイプライン トポロジ](../pipeline.md)が列挙されます。

1. "avaedge" モジュールを右クリックし、コンテキスト メニューから **[Invoke Module Direct Method]\(モジュールのダイレクト メソッドを呼び出す\)** を選択します。
1. Visual Studio Code ウィンドウの上部中央にエディット ボックスがポップアップ表示されます。 エディット ボックスに「pipelineTopologyList」と入力して、Enter キーを押します。
1. さらに、次の JSON ペイロードをコピーしてエディット ボックスに貼り付け、Enter キーを押します。
   
```json
{
    "@apiVersion" : "1.1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。
    
```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

パイプライン トポロジが作成されていないため、上記は想定された応答です。

### <a name="set-a-pipeline-topology"></a>パイプライン トポロジを設定する

上記と同じ手順で、`pipelineTopologySet` を呼び出して、次の JSON をペイロードとして使用してパイプライン トポロジを設定できます。 "EVRtoVideoSinkOnMotionDetection" という名前のパイプライン トポロジを作成します。

> [!NOTE]
> 下のペイロードで `videoName` プロパティは "sample-motion-video-camera001" に設定されています。これは、Video Analyzer アカウントに作成されるビデオ リソースの名前になります。 このリソース名は、録画するライブ ビデオ ソースごとに一意である必要があります。 一意性を確保するために、必要に応じて下の `videoName` プロパティを編集する必要があります。

```
{
  "@apiVersion": "1.1",
  "name": "EVRtoVideoSinkOnMotionDetection",
  "properties": {
    "description": "Event-based video recording to Video Sink based on motion events",
    "parameters": [
      {
        "name": "rtspUserName",
        "type": "String",
        "description": "rtsp source user name.",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "String",
        "description": "rtsp source password.",
        "default&quot;: &quot;dummyPassword"
      },
      {
        "name": "rtspUrl",
        "type": "String",
        "description&quot;: &quot;rtsp Url"
      },
      {
        "name": "motionSensitivity",
        "type": "String",
        "description": "motion detection sensitivity",
        "default&quot;: &quot;medium"
      },
      {
        "name": "hubSinkOutputName",
        "type": "String",
        "description": "hub sink output name",
        "default&quot;: &quot;inferenceOutput"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "url": "${rtspUrl}",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          }
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "name": "motionDetection",
        "sensitivity": "${motionSensitivity}",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ]
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
        "name": "signalGateProcessor",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          },
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "activationEvaluationWindow": "PT1S",
        "activationSignalOffset": "PT0S",
        "minimumActivationTime": "PT30S",
        "maximumActivationTime&quot;: &quot;PT30S"
      }
    ],
    "sinks": [
      {
        "@type": "#Microsoft.VideoAnalyzer.VideoSink",
        "name": "videoSink",
        "videoName": "sample-motion-video-camera001",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "videoCreationProperties": {
          "title": "sample-motion-video-camera001",
          "description": "Motion-detection based recording of clips to a video resource",
          "segmentLength&quot;: &quot;PT30S"
        },
        "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
        "localMediaCacheMaximumSizeMiB&quot;: &quot;2048"
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.IoTHubMessageSink",
        "name": "hubSink",
        "hubOutputName": "${hubSinkOutputName}",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          }
        ]
      }
    ]
  }
}
```

上記の JSON ペイロードによって、5 つのパラメーター (うち 4 つは既定値) を定義するパイプライン トポロジが作成されます。 トポロジには、1 つのソース ノード ([RTSP ソース](../pipeline.md#rtsp-source))、2 つのプロセッサ ノード ([モーション検出プロセッサ](../pipeline.md#motion-detection-processor)と[シグナル ゲート プロセッサ](../pipeline.md#signal-gate-processor)、2 つのシンク ノード (IoT Hub シンクと[ビデオ シンク](../pipeline.md#video-sink)) があります。 トポロジの視覚的な表現を上に示します。

数秒すると、**出力** ウィンドウに次の応答が表示されます。

```
[DirectMethod] Invoking Direct Method [pipelineTopologySet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```



返された状態は 201 であり、新しいパイプライン トポロジが作成されたことを示しています。 次の手順として、次のダイレクト メソッドを試してみてください。

* 再度 `pipelineTopologySet` を呼び出し、返された状態コードが 200 であることを確認します。 状態コード 200 は、既存のパイプライン トポロジが正常に更新されたことを示しています。
* `pipelineTopologySet` を再度呼び出しますが、説明文字列を変更します。 応答の状態コードは 200 で、説明が新しい値に更新されたことを確認してください。
* 前の手順で説明した `pipelineTopologyList` を呼び出し、返されたペイロードに今度は "EVRtoVideoSinkOnMotionDetection" トポロジが表示されていることを確認します。

### <a name="read-the-pipeline-topology"></a>パイプライン トポロジを読み取る

ここでは、次のペイロードを使用して `pipelineTopologyGet` を呼び出します。
```

{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [pipelineTopologyGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

応答のペイロードで、次のプロパティに注目してください。

* 状態コードが 200 です。これは成功を意味します。
* ペイロードには、`createdAt` タイム スタンプと `lastModifiedAt` タイム スタンプが含まれています。

### <a name="create-a-live-pipeline-using-the-topology"></a>トポロジを使用してライブ パイプラインを作成する

次に、上記のパイプライン トポロジを参照するライブ パイプラインを作成します。 次のペイロードを使用して、`livePipelineSet` ダイレクト メソッドを呼び出します。

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1",
    "properties" : {
        "topologyName" : "EVRtoVideoSinkOnMotionDetection",
        "description" : "Sample pipeline description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

次のことを考慮してください。

* 上記のペイロードは、ライブ パイプラインによって使用されるトポロジ ("EVRtoVideoSinkOnMotionDetection") を指定します。
* このペイロードには、`rtspUrl` のパラメーター値が含まれています。これには、トポロジのペイロードで既定値が設定されていませんでした。

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [livePipelineSet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:20:29.023Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:20:29.023Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

応答のペイロードで、次のプロパティに注目してください。

* 状態コードは 201 であり、新しいライブ パイプラインが作成されたことを示しています。
* 状態は "Inactive" であり、ライブ パイプラインは作成されたが、アクティブ化されていないことを示しています。 詳細については、「[パイプラインの状態](../pipeline.md#pipeline-states)」を参照してください。

次の手順として、次のダイレクト メソッドを試してみてください。

* 同じペイロードで再度 `livePipelineSet` を呼び出します。返される状態コードが、今度は 200 になっていることに注目してください。
* 異なる説明で再度 `livePipelineSet` を呼び出し、応答ペイロード内の更新されている説明を確認します。これはライブ パイプラインが正常に更新されたことを示しています。

    > [!NOTE]
    > [パイプライン トポロジ](../pipeline.md#pipeline-topologies)のセクションで説明されているように、複数のライブ パイプラインを作成して、同じパイプライン トポロジを使用して複数のカメラからのライブ ビデオ ストリームを分析できます。 ただし、この特定のトポロジでは、`videoName` の値がハードコーディングされます。 Video Analyzer ビデオ リソースに記録されるライブ ビデオ ソースは 1 つだけであるため、この特定のトポロジで追加のライブ パイプラインを作成することはできません。

### <a name="activate-the-live-pipeline"></a>ライブ パイプラインをアクティブ化する

次に、ライブ パイプラインをアクティブ化します。これにより、パイプラインを介して (シミュレートされた) ライブ ビデオのフローが開始されます。 次のペイロードを使用して、ダイレクト メソッド `livePipelineActivate` を呼び出します。

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [livePipelineActivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

応答ペイロードの状態コード 200 は、ライブ パイプラインが正常にアクティブ化されたことを示しています。

### <a name="check-the-state-of-the-live-pipeline"></a>ライブ パイプラインの状態を確認する

ここでは、次のペイロードを使用して、`livePipelineGet` ダイレクト メソッドを呼び出します。

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [livePipelineGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T14:21:21.750Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T14:21:21.750Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

応答のペイロードで、次のプロパティに注目してください。

* 状態コードが 200 です。これは成功を意味します。
* 状態が "Active" になり、ライブ パイプラインは現在 "Active" 状態であることを示しています。

## <a name="observe-results"></a>結果を観察する

上記で作成してアクティブ化したライブ パイプラインは、モーション検出プロセッサ ノードを使用して、受信ライブ ビデオ ストリーム内から動きを検出し、IoT Hub シンクにイベントを送信します。 その後、これらのイベントはメッセージとして IoT ハブに中継されて、観察できる状態になります。 出力ウィンドウに次のメッセージが表示されます。

```
[IoTHubMonitor] [1:22:53 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620066173760872 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCJoQAAAMABAAAAwDwPGDGWA==,aOvhEsiw\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-03T18:22:53.761Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066173816,
    "iothub-message-source": "Telemetry",
    "messageId": "c2de6a40-1e0a-45ef-9449-599fc5680d05",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
[IoTHubMonitor] [1:22:59 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "timestamp": 145805956115743,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/motionDetection",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-03T18:22:59.063Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066179359,
    "iothub-message-source": "Telemetry",
    "messageId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}

```

上記のメッセージに関して、次のプロパティに注意してください。

* 各メッセージには、`body` セクションと `properties` セクションが含まれています。 これらのセクションが表す内容については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」の記事を参照してください。
* 最初のメッセージは **MediaSessionEstablished** であり、RTSP ソース ノード (subject) が RTSP シミュレーターとの接続を確立し、(シミュレートされた) ライブ フィードの受信を開始できたことを示します。
* `subject` は、メッセージの生成元のライブ パイプライン内のノードを参照しています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。
* `eventType` は、これが診断イベントであることを示しています。
* `eventTime` はイベントの発生時刻を示しています。
* `body` にはイベントに関するデータが含まれています。これは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) メッセージです。
* 2 番目のメッセージは **Inference** イベントです。 これは **MediaSessionEstablished** メッセージの約 5 秒後に送信されていることを確認できます。これはビデオの開始から、車が駐車場を通過するまでの遅延に相当します。
* `subject` は、このメッセージを生成した、パイプライン内のモーション検出プロセッサ ノードを参照しています
* これは分析イベントであるため、`body` には `timestamp` および `inferences` データが含まれています。
* `inferences` セクションを見ると、`type` が "motion" であり、"motion" イベントに関する追加データが含まれていることがわかります。

本文の `messageId` セクションが "9ccfab80-2993-42c7-9452-92e21df96413" であることに注意してください。 これは、次の操作イベントに表示されます。

```
[IoTHubMonitor] [1:23:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion-video-camera001"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-03T18:23:31.319Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066211373,
    "iothub-message-source": "Telemetry",
    "messageId": "c7cbb363-7cc7-4169-936f-55de5fae111c",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
この **RecordingStarted** メッセージは、ビデオ録画が開始されたことを示します。 `correlationId` の値の "9ccfab80-2993-42c7-9452-92e21df96413" は、**Inference** メッセージの `messageId` と一致することに注目してください。これにより、録画をトリガーしたイベントを追跡できます。 次の操作イベントは以下です。

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingAvailable",
    "eventTime": "2021-05-03T18:24:00.686Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240741,
    "iothub-message-source": "Telemetry",
    "messageId": "5b26aa88-e037-4834-af34-a6a4df3c42c2",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
```

この **RecordingAvailable** イベントは、メディア データが現在ビデオ リソースに録画されていることを示します。 `correlationId` が同じ "9ccfab80-2993-42c7-9452-92e21df96413" である点に注意してください。 このメッセージ チェーン (同じ `correlationId` を含む) の最後の操作イベントは次のとおりです。

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStopped",
    "eventTime": "2021-05-03T18:24:00.710Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240766,
    "iothub-message-source": "Telemetry",
    "messageId": "f3dbd5d5-3176-4d5b-80d8-c67de85bc619",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
この **RecordingStopped** イベントは、シグナル ゲートが閉じ、受信ライブ ビデオの関連部分が録画されたことを示します。 `correlationId` が同じ "9ccfab80-2993-42c7-9452-92e21df96413" である点に注意してください。

トポロジでは、シグナル ゲート プロセッサ ノードのアクティブ化の時間が 30 秒に構成されています。つまり、このパイプライン トポロジにより約 30 秒分のビデオが録画されます。  ビデオが録画されている間、**Inference** イベントがモーション検出プロセッサ ノードで継続的に生成され、**RecordingAvailable** と **RecordingStopped** のイベントの間に出力ウィンドウに表示されます。

ライブ パイプラインの実行を継続すると、RTSP シミュレーターがビデオ ファイルの最後に到達し、停止または切断されます。 その後、RTSP ソース ノードがシミュレーターに再接続され、プロセスが繰り返されます。
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>その他のダイレクト メソッド呼び出しを呼び出してクリーンアップする

次に、ダイレクト メソッドを呼び出して、ライブ パイプラインを非アクティブ化および削除できます (この順序で)。

### <a name="deactivate-the-live-pipeline"></a>ライブ パイプラインを非アクティブ化する

次のペイロードを使用して、`livePipelineDeactivate` ダイレクト メソッドを呼び出します。

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [livePipelineDeactivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、ライブ パイプラインが正常に非アクティブ化されたことを示しています。


### <a name="delete-the-live-pipeline"></a>ライブ パイプラインを削除する

次のペイロードを使用して、ダイレクト メソッド `livePipelineDelete` を呼び出します

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [livePipelineDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

応答の状態コード 200 は、ライブ パイプラインが正常に削除されたことを示しています。


### <a name="delete-the-pipeline-topology"></a>パイプライン トポロジを削除する

次のペイロードを使用して、`pipelineTopologyDelete` ダイレクト メソッドを呼び出します。

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [pipelineTopologyDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、パイプライン トポロジが正常に削除されたことを示しています。

## <a name="playing-back-the-recording"></a>録画を再生する

ライブ パイプラインによって作成された Video Analyzer ビデオ リソースを調べるには、Azure portal にログインしてビデオを視聴します。
1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. サブスクリプション内のリソースの中から Video Analyzers アカウントを見つけ、アカウント ペインを開きます。
1. **[Video Analyzer]** のリストで **[ビデオ]** を選択します。
1. `sample-motion-video-camera001` という名前で一覧に含まれているビデオが見つかります。 これは、パイプライン トポロジ ファイルで選択された名前です。
1. ビデオを選択します。
1. ビデオの詳細ページが開き、自動的に再生が開始されます。

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->


[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]    

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

* [ビデオのレコーディングを再生する](../playback-recordings-how-to.md)方法を確認する
* [ライブ ビデオを分析するためのクイック スタート](analyze-live-video-use-your-model-http.md)を試す
