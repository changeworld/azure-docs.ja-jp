---
title: Live Video Analytics on IoT Edge の概要 - Azure
description: このクイックスタートでは、Live Video Analytics on IoT Edge の概要とライブ ビデオ ストリームにおけるモーション検出の方法について説明します。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261567"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>クイック スタート:はじめに - Live Video Analytics on IoT Edge

このクイックスタートでは、Live Video Analytics on IoT Edge の基本的な操作手順について説明します。 IoT Edge デバイスとして Azure VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。 セットアップ手順の完了後、メディア グラフを通じてライブ ビデオ ストリームのシミュレーションを実行することができます。そのストリーム内のあらゆるモーションがメディア グラフによって検出、レポートされます。 そのメディア グラフをグラフィカルに表したものが次の図です。

![モーション検出に基づく Live Video Analytics](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Visual Studio Code](https://code.visualstudio.com/)。[Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)と共に自分の開発マシンにインストールされている必要があります。
* 開発マシンの接続先ネットワーク。Azure IoT Tools が Azure IoT Hub と通信できるよう、ポート 5671 で AMQP プロトコルが許可されている必要があります。

> [!TIP]
> Azure IoT Tools 拡張機能のインストール中に Docker のインストールを求められる場合があります。 これは無視してかまいません。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

このチュートリアルには、次の Azure リソースが必要です。

* IoT Hub
* ストレージ アカウント
* Azure Media Services アカウント
* Azure 上の Linux VM ([IoT Edge ランタイム](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)がインストール済み)

このクイックスタートでは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を使用して、ご利用の Azure サブスクリプションに上記の Azure リソースをデプロイするようお勧めします。 そのためには、次の手順に従います。

1. https://shell.azure.com を参照します。
1. Cloud Shell を初めて使用している場合は、ストレージ アカウントと Microsoft Azure Files 共有を作成するためのサブスクリプションを選択するよう求められます。 [ストレージの作成] を選択して、Cloud Shell のセッション情報を保存するためのストレージ アカウントを作成してください。 Azure Media Services アカウントで使用するためのストレージ アカウントもスクリプトによって作成されますが、このストレージ アカウントはそれとは別のものです。
1. シェル ウィンドウの左側にあるドロップダウンから "Bash" 環境をご利用の環境として選択します。

    ![環境セレクター](./media/quickstarts/env-selector.png)

1. 次のコマンドを実行します。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
スクリプトが正常に完了すれば、前述したすべてのリソースがご利用のサブスクリプションに表示されます。 スクリプトの出力の一部として、IoT ハブの名前を列挙したリソース一覧が生成されます。 **"Microsoft.Devices/IotHubs"** というリソースの種類を探して、その名前を書き留めてください。 次の手順で必要になります。 さらにこのスクリプトによって、~/clouddrive/lva-sample/ ディレクトリには、いくつかの構成ファイルが生成されます。後でこのクイックスタートの中で、これらのファイルが必要になります。

## <a name="deploy-modules-on-your-edge-device"></a>エッジ デバイスにモジュールをデプロイする

Cloud Shell から次のコマンドを実行します。

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

エッジ デバイス (Linux VM) には、上記のコマンドによって次のモジュールがデプロイされます。

* Live Video Analytics on IoT Edge (モジュール名: "lvaEdge")
* RTSP シミュレーター (モジュール名: "rtspsim")

RTSP シミュレーター モジュールは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)の実行時にエッジ デバイスにコピーされた保存済みのビデオ ファイルを使用してライブ ビデオ ストリームをシミュレートします。 この段階で、モジュールのデプロイは完了していますが、メディア グラフはアクティブになっていません。

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Visual Studio Code で Azure IoT Tools 拡張機能を構成する

Visual Studio Code を起動します。以下の手順に従い、Azure IoT Tools 拡張機能を使用して Azure IoT Hub に接続します。

1. Visual Studio Code で、 **[表示]**  >  **[エクスプローラー]** の順に移動するか、単に Ctrl + Shift + E キーを押して、[エクスプローラー] タブに移動します。
1. [エクスプローラー] タブで、左下隅にある [Azure IoT Hub] をクリックします。
1. [その他のオプション] アイコンをクリックしてコンテキスト メニューを表示し、[Set IoT Hub Connection String]\(IoT Hub の接続文字列の設定\) オプションを選択します。
1. 入力ボックスがポップアップ表示されたら、該当する IoT Hub の接続文字列を入力します。 IoT Hub の接続文字列は、Cloud Shell で ~/clouddrive/lva-sample/appsettings.json から取得できます。
1. 接続に成功した場合、エッジ デバイスの一覧が表示されます。 "lva-sample-device" という名前のデバイスが少なくとも 1 つ存在すると思います。
1. これでコンテキスト メニューから IoT Edge デバイスを管理し、Azure IoT Hub を操作できるようになりました。
1. "lva-sample-device" の Modules ノードを展開すると、エッジ デバイスにデプロイされたモジュールを表示できます。

    ![lva-sample-device ノード](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>ダイレクト メソッドの使用

ライブ ビデオ ストリームの分析は、モジュールを使用し、ダイレクト メソッドを呼び出すことによって行うことができます。 モジュールに用意されているすべてのダイレクト メソッドについては、[Live Video Analytics on IoT Edge のダイレクト メソッド](direct-methods.md)に関するページをご覧ください。 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList を呼び出す
これにより、モジュール内のすべての[グラフ トポロジ](media-graph-concept.md#media-graph-topologies-and-instances)が列挙されます。

1. "lvaEdge" モジュールを右クリックし、コンテキスト メニューから [Invoke Module Direct Method]\(モジュールのダイレクト メソッドを呼び出す\) を選択します。
1. Visual Studio Code ウィンドウの上部中央にエディット ボックスがポップアップ表示されます。 エディット ボックスに「GraphTopologyList」と入力して、Enter キーを押します。
1. さらに、次の JSON ペイロードをコピーしてエディット ボックスに貼り付け、Enter キーを押します。

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    数秒すると、次の応答と共に Visual Studio Code の出力ウィンドウがポップアップ表示されます

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    上記は想定内の応答です。まだグラフ トポロジが作成されていないためです。
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet を呼び出す

GraphTopologyList を呼び出したときと同じ手順で、次の JSON をペイロードに使用して GraphTopologySet を呼び出せば、[グラフ トポロジ](media-graph-concept.md#media-graph-topologies-and-instances)を設定できます。

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


上記の JSON ペイロードを使用した場合、3 つのパラメーター (うち 2 つは既定値) を定義するグラフ トポロジが作成されます。 このトポロジには、ソース (RTSP ソース) ノード、プロセッサ (モーション検出プロセッサ) ノード、シンク (IoT Hub シンク) ノードがそれぞれ 1 つずつ存在します。

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
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
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

返された状態は 201 で、新しいトポロジが作成されたことを意味します。 次の手順を試してみましょう。

* 再度 GraphTopologySet を呼び出します。返される状態コードが 200 であることに注目してください。 状態コード 200 は、既存のトポロジが正常に更新されたことを意味します。
* 説明文字列を変えて再度 GraphTopologySet を呼び出します。 応答の状態コードは 200 で、説明が新しい値に更新されたことに注目してください。
* 前セクションで取り上げた GraphTopologyList を呼び出します。今度は、返されたペイロードに "MotionDetection" トポロジが表示されていることが確認できます。

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet を呼び出す

今度は、次のペイロードで GraphTopologyGet を呼び出します。

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
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
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

応答のペイロードで、次の点に注目してください。

* 状態コードが 200 です。これは成功を意味します。
* ペイロードに "created" と "lastModified" のタイムスタンプが存在します。

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet を呼び出す

次に、前出のグラフ トポロジを参照するグラフ インスタンスを作成します。 [こちら](media-graph-concept.md#media-graph-topologies-and-instances)の説明にあるように、グラフ インスタンスを使用すると、同じグラフ トポロジを持つさまざまなカメラからのライブ ビデオ ストリームを分析することができます。

次のペイロードでダイレクト メソッド GraphInstanceSet を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

次のことを考慮してください。

* 上記のペイロードには、作成する必要のあるインスタンスのトポロジ名 (MotionDetection) が指定されています。
* このペイロードには、"rtspUrl" パラメーターの値が含まれています。これには、グラフ トポロジのペイロードで既定値が設定されていませんでした。

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

応答のペイロードで、次の点に注目してください。

* 状態コードが 201 です。これは新しいインスタンスが作成されたことを意味します。
* 状態が "Inactive" になっています。グラフ インスタンスは作成されたものの、アクティブ化されていないことがわかります。 詳細については、[メディア グラフの状態](media-graph-concept.md)に関するセクションを参照してください。

次の手順を試してみましょう。

* 同じペイロードで再度 GraphInstanceSet を呼び出します。返される状態コードが、今度は 200 になっていることに注目してください。
* 異なる説明で再度 Invoke GraphInstanceSet を呼び出します。応答のペイロードを見ると、説明が更新されていることがわかります。これはグラフ インスタンスが正常に更新されたことを意味します。
* 名前を "Sample-Graph-2" に変えて GraphInstanceSet を呼び出し、応答のペイロードを観察します。 新しいグラフ インスタンスが作成されている (状態コードが 201 である) ことに注目してください。

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate を呼び出す

今度は、グラフ インスタンスをアクティブにします。グラフ インスタンスをアクティブにすることで、モジュールを使用したライブ ビデオのフローが開始されます。 次のペイロードでダイレクト メソッド GraphInstanceActivate を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

応答のペイロードにある状態コード 200 は、グラフ インスタンスが正常にアクティブ化されたことを意味します。

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet を呼び出す

次のペイロードでダイレクト メソッド GraphInstanceGet を呼び出します。

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

応答のペイロードで、次の点に注目してください。

* 状態コードが 200 です。これは成功を意味します。
* 状態が "Active" になり、グラフ インスタンスが "Active" 状態になったことがわかります。

## <a name="observe-results"></a>結果を観察する

先ほど作成してアクティブにしたグラフ インスタンスは、モーション検出プロセッサ ノードを使用して、受信したライブ ビデオ ストリーム内から動きを検出し、IoT Hub のシンク ノードにイベントを送信します。 それらのイベントは IoT Edge ハブに中継されて、観察できる状態になります。 これを行うには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開いて、左下隅の Azure IoT Hub を探します。
2. [Devices] ノードを展開します。
3. [lva-sample-device] を右クリックし、[Start Monitoring Built-in Event Monitoring]\(組み込みイベント モニタリングの監視を開始する\) オプションを選択します。

![IoT Hub イベントの監視を開始する](./media/quickstarts/start-monitoring-iothub-events.png)

出力ウィンドウに次のメッセージが表示されます。

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

上のメッセージに関して、次の点に注意してください

* メッセージに "body" セクションと "applicationProperties" セクションが存在します。 これらのセクションが表す内容については、「[IoT Hub メッセージを作成し、読み取る](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)」の記事を参照してください。
* applicationProperties の "subject" が、メッセージの生成元となった MediaGraph 内のノードを参照しています。 このケースでは、モーション検出プロセッサからメッセージが生成されています。
* applicationProperties の "eventType" を見ると、これは Analytics イベントであることがわかります。
* "eventTime" にはイベントの発生時刻が示されています。
* "body" に、分析イベントに関するデータが含まれています。 このケースでは、Inference イベントであるため、body には "timestamp" データと "inferences" データが存在します。
* "inferences" セクションを見ると、"type" が "motion" であること、また、"motion" イベントについての詳しいデータが存在することがわかります。

MediaGraph をしばらく実行していると、出力ウィンドウに次のメッセージも表示されます。

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

上のメッセージに関して、次の点に注意してください

* applicationProperties の "subject" を見ると、メッセージの生成元がメディア グラフの RTSP ソース ノードであることがわかります。
* applicationProperties の "eventType" を見ると、これは Diagnostic イベントであることがわかります。
* 診断イベントに関するデータは "body" に含まれています。 このケースでは、イベントが MediaSessionEstablished であるため、その本体となっています。

## <a name="invoke-additional-direct-methods-to-clean-up"></a>その他のダイレクト メソッドを呼び出してクリーンアップする

ダイレクト メソッドを呼び出して、グラフ インスタンスをまず非アクティブ化し、その後削除します。

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate を呼び出す

次のペイロードでダイレクト メソッド GraphInstanceDeactivate を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、グラフ インスタンスが正常に非アクティブ化されたことを意味します。

次の手順を試してみましょう。

* 前のセクションで触れた GraphInstanceGet を呼び出して、"state" の値を観察します。

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete を呼び出す

次のペイロードでダイレクト メソッド GraphInstanceDelete を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

応答の状態コード 200 は、グラフ インスタンスが正常に削除されたことを意味します。

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete を呼び出す

次のペイロードでダイレクト メソッド GraphTopologyDelete を呼び出します。

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

数秒すると、次の応答が出力ウィンドウに表示されます。

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

状態コード 200 は、グラフ トポロジが正常に削除されたことを意味します。

次の手順を試してみましょう。

* GraphTopologyList を呼び出し、モジュールにグラフ トポロジが存在しないことを確認します。
* GraphTopologyList と同じペイロードで GraphInstanceList を呼び出し、グラフ インスタンスが一切列挙されないことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使う予定がなければ、このクイックスタートで作成したリソースを削除してください。

## <a name="next-steps"></a>次のステップ

* Live Video Analytics on IoT Edge を使用してビデオを記録する方法を学習します。
* 診断メッセージについて詳しく学習します。
