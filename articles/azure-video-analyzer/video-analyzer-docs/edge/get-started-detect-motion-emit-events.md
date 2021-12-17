---
title: Azure Video Analyzer で作業を開始する - Azure
description: このクイックスタートでは、Azure Video Analyzer の基本的な操作手順について説明します。 IoT Edge デバイスとして Azure VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2dcb4ef9717cbd754bf963015e0054b0c307738d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030009"
---
# <a name="quickstart-get-started-with-azure-video-analyzer"></a>クイックスタート: Azure Video Analyzer で作業を開始する

[!INCLUDE [header](includes/edge-env.md)]

このクイックスタートでは、Azure Video Analyzer の基本的な操作手順について説明します。 IoT Edge デバイスとして Azure VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。

セットアップ手順の完了後、パイプラインを通じて、シミュレートされたライブ ビデオ ストリームを実行できます。そのストリーム内のあらゆるモーションがメディア グラフによって検出およびレポートされます。 次の図は、そのパイプラインをグラフィカルに表したものです。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="モーションの検出":::

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。

    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

デプロイには約 **20 分** かかります。 完了すると、次のような特定の Azure リソースが Azure サブスクリプションにデプロイされます。
1. **Video Analyzer アカウント** - この [クラウド サービス](../overview.md)は、Video Analyzer エッジ モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます。
1. **ストレージ アカウント** - 録画されたビデオとビデオ分析の格納用です。
1. **マネージド ID** - これは、上記のストレージ アカウントへのアクセスを管理するために使用されるユーザー割り当て [マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) です。
1. **仮想マシン** - これは、シミュレートされたエッジ デバイスとして機能する仮想マシンです。
1. **IoT Hub** - IoT アプリケーションと IoT Edge モジュール、さらにそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

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

## <a name="use-direct-method-calls"></a>ダイレクト メソッドの呼び出しを使用する

これで、Video Analyzer エッジ モジュールによって公開されるダイレクト メソッドを呼び出して、ライブ ビデオ ストリームを分析できます。 [Video Analyzer のダイレクト メソッド](direct-methods.md)に関する記事を読み、このモジュールによって提供されるすべてのダイレクト メソッドを確認してください。 ダイレクト メソッドのスキーマについては、[こちら](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json)を参照してください。

### <a name="enumerate-pipeline-topologies"></a>パイプライン トポロジを列挙する

この手順により、モジュール内のすべての[パイプライン トポロジ](../pipeline.md)が列挙されます。

1. "avaedge" モジュールを右クリックし、コンテキスト メニューから **[Invoke Module Direct Method]\(モジュールのダイレクト メソッドを呼び出す\)** を選択します。
1. Visual Studio Code ウィンドウの上部中央にエディット ボックスがポップアップ表示されます。 編集ボックスに「`pipelineTopologyList`」と入力して、Enter キーを押します。
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

上記と同じ手順で、`pipelineTopologySet` を呼び出して、次の JSON をペイロードとして使用してパイプライン トポロジを設定できます。 "MotionDetection" という名前のパイプライン トポロジを作成します。


```json
{
    "@apiVersion": "1.1",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUrl",
                "type": "string",
                "description&quot;: &quot;rtspUrl"
            },
            {
                "name": "rtspUserName",
                "type": "string",
                "description": "rtspUserName",
                "default&quot;: &quot;dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "string",
                "description": "rtspPassword",
                "default&quot;: &quot;dummypw"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password&quot;: &quot;${rtspPassword}"
                    },
                    "url&quot;: &quot;${rtspUrl}"
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
                "sensitivity": "medium",
                "name": "motionDetection",
                "inputs": [
                    {
                        "nodeName": "rtspSource",
                        "outputSelectors": []
                    }
                ]
            }
        ],
        "sinks": [
            {
                "hubOutputName": "inferenceOutput",
                "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
                "name": "iotHubSink",
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

この JSON ペイロードにより、3 つのパラメーターを定義するトポロジが作成されます。そのうちの 2 つのパラメーターは既定値です。 このトポロジには、1 つのソース ノード ([RTSP ソース](../pipeline.md#rtsp-source))、1 つのプロセッサ ノード ([モーション検出プロセッサ](../pipeline.md#motion-detection-processor))、1 つのシンク ノード ([IoT Hub メッセージ シンク](../pipeline.md#iot-hub-message-sink)) があります。 トポロジの視覚的な表現を上に示します。

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
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
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
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

返される状態は 201 です。 この状態は、新しいトポロジが作成されたことを示しています。

次の手順を試してみましょう。

1. `pipelineTopologySet` を再度呼び出します。 返された状態コードは 200 です。 このコードは、既存のトポロジが正常に更新されたこと示しています。
1. `pipelineTopologySet` を再度呼び出しますが、説明文字列を変更します。 返された状態コードは 200 であり、説明は新しい値に更新されています。
1. 前のセクションで説明したように `pipelineTopologyList` を呼び出します。 返されたペイロードに "MotionDetection" トポロジが表示されるようになりました。

### <a name="read-the-pipeline-topology"></a>パイプライン トポロジを読み取る

次のペイロードを使用して `pipelineTopologyGet` を呼び出します。

```json
{
    "@apiVersion" : "1.1",
    "name" : "MotionDetection"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
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
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
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

応答ペイロードで、次の詳細に注目します。

* 状態コードは 200 であり、成功を示しています。
* ペイロードには、`createdAt` タイム スタンプと `lastModifiedAt` タイム スタンプが含まれています。

### <a name="create-a-live-pipeline-using-the-topology"></a>トポロジを使用してライブ パイプラインを作成する

次に、上記のパイプライン トポロジを参照するライブ パイプラインを作成します。 次のペイロードを使用して、`livePipelineSet` ダイレクト メソッドを呼び出します。

```json
{
    "@apiVersion" : "1.1",
    "name": "mdpipeline1",
    "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample pipeline description",
        "parameters": [
            {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
            },
            {
                "name": "rtspUserName",
                "value": "testuser"
            },
            {
                "name": "rtspPassword",
                "value": "testpassword"
            }
        ]
    }
}
```

このペイロードについて次の点に注目します。

* 上記のペイロードは、ライブ パイプラインによって使用されるトポロジ ("MotionDetection") を指定します。
* このペイロードには、`rtspUrl` のパラメーター値が含まれています。これには、トポロジのペイロードで既定値が設定されていませんでした。 この値は、次のサンプル ビデオへのリンクです。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]


数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

応答ペイロードで、次の点に注目します。

* 状態コードは 201 であり、新しいライブ パイプラインが作成されたことを示しています。
* 状態は "Inactive" であり、ライブ パイプラインは作成されたが、アクティブ化されていないことを示しています。 詳細については、「[パイプラインの状態](../pipeline.md#pipeline-states)」を参照してください。

次の手順として、次のダイレクト メソッドを試してみてください。

* 同じペイロードで再度 `livePipelineSet` を呼び出します。返される状態コードが、今度は 200 になっていることに注目してください。
* 異なる説明で再度 `livePipelineSet` を呼び出します。応答ペイロードを見ると、説明が更新されていることがわかります。これはライブ パイプラインが正常に更新されたことを示しています。
* `livePipelineSet` を呼び出しますが、名前を "mdpipeline2" に変更し、`rtspUrl` を "rtsp://rtspsim:554/media/lots_015.mkv" に変更します。 応答ペイロードで、新しく作成されたライブ パイプライン (つまり、状態コード 201) に注目してください。
    > [!NOTE]
    > 「[パイプライン トポロジ](../pipeline.md#pipeline-topologies)」で説明されているように、複数のライブ パイプラインを作成して、同じパイプライン トポロジを使用して複数のカメラからのライブ ビデオ ストリームを分析できます。 追加のライブ パイプラインを作成する場合は、クリーンアップ手順でそれらを削除するように注意してください。

### <a name="activate-the-live-pipeline"></a>ライブ パイプラインをアクティブ化する

次に、ライブ パイプラインをアクティブ化します。これにより、パイプラインを介して (シミュレートされた) ライブ ビデオのフローが開始されます。 次のペイロードを使用して、ダイレクト メソッド `livePipelineActivate` を呼び出します。

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

数秒以内に、次の応答が [出力] ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、ライブ パイプラインが正常にアクティブ化されたことを示しています。

### <a name="check-the-state-of-the-live-pipeline"></a>ライブ パイプラインの状態を確認する

ここでは、次のペイロードを使用して、`livePipelineGet` ダイレクト メソッドを呼び出します。

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

数秒以内に、次の応答が [出力] ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

応答ペイロードで、次の詳細に注目します。

* 状態コードは 200 であり、成功を示しています。
* 状態が "Active" になり、ライブ パイプラインが現在アクティブであることを示しています。

## <a name="observe-results"></a>結果を観察する

上記で作成してアクティブ化したライブ パイプラインは、モーション検出プロセッサ ノードを使用して、受信ライブ ビデオ ストリーム内から動きを検出し、IoT Hub シンクにイベントを送信します。 これらのイベントは IoT ハブにメッセージとして中継されて、観察できる状態になります。 出力ウィンドウに、次のような "本文" のメッセージが表示されます。


```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

次の詳細を確認してください。

*   推論セクションで、種類がモーションであることが示されています。 これにより、モーション イベントに関する追加データが提供され、モーションが検出された (特定のタイムスタンプの) ビデオ フレームの領域の境界ボックスが提供されます。

    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>その他のダイレクト メソッド呼び出しを呼び出してクリーンアップする

次に、ダイレクト メソッドを呼び出して、ライブ パイプラインを非アクティブ化および削除できます (この順序で)。

### <a name="deactivate-the-live-pipeline"></a>ライブ パイプラインを非アクティブ化する

次のペイロードを使用して、`livePipelineDeactivate` ダイレクト メソッドを呼び出します。

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、ライブ パイプラインが正常に非アクティブ化されたことを示しています。 

次に、この記事で前述したように `livePipelineGet` の呼び出しを試してみます。 状態の値を観察します。

### <a name="delete-the-live-pipeline"></a>ライブ パイプラインを削除する

次のペイロードを使用して、ダイレクト メソッド `livePipelineDelete` を呼び出します

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": null
}
```
状態コード 200 は、ライブ パイプラインが正常に削除されたことを示しています。

"mdpipeline2" という名前のパイプラインも作成した場合は、この追加のパイプラインも削除しないと、パイプライン トポロジを削除できません。 次のペイロードを使用して、ダイレクト メソッド `livePipelineDelete` を再度呼び出します。

```
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline2"
}
```

数秒以内に、次の応答が [出力] ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、ライブ パイプラインが正常に削除されたことを示しています。

`pipelineTopologyList` と同じペイロードを使用して `livePipelineList` を呼び出すことができます。 アクティブなパイプラインが列挙されないことを確認します。

### <a name="delete-the-pipeline-topology"></a>パイプライン トポロジを削除する

すべてのライブ パイプラインが削除された後、次のペイロードを使用して `pipelineTopologyDelete` ダイレクト メソッドを呼び出すことができます。

```json
{
    "@apiVersion" : "1.1",
    "name" : "MotionDetection"
}
```

数秒以内に、次の応答が **[出力]** ウィンドウに表示されます。

```json
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、トポロジが正常に削除されたことを示しています。

`pipelineTopologyList` を呼び出し、モジュールにトポロジが含まれていないことを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

* [モーションが検出されたらクラウドにビデオを記録するクイックスタート](detect-motion-record-video-clips-cloud.md)を試す
* [ライブ ビデオを分析するためのクイック スタート](analyze-live-video-use-your-model-http.md)を試す
* [診断メッセージ](monitor-log-edge.md)について詳しく学習する 
