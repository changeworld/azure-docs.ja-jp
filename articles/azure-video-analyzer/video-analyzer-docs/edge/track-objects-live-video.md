---
title: Azure Video Analyzer を使用してライブ ビデオでオブジェクトを追跡する
description: このクイックスタートでは、Azure Video Analyzer エッジ モジュールを使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードでオブジェクトを追跡する方法を示します。 コンピューター ビジョン モデルを適用して、ライブ ビデオ フィードのフレームのサブセット内でオブジェクトを検出する方法を示します。 その後、オブジェクト トラッカー ノードを使用して、他のフレーム内でそれらのオブジェクトを追跡できます。
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b9286dc6dba80de841969af285ab57e587f560a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319265"
---
# <a name="quickstart-track-objects-in-a-live-video"></a>クイックスタート: ライブ ビデオでオブジェクトを追跡する

[!INCLUDE [header](includes/edge-env.md)]

このクイックスタートでは、Azure Video Analyzer エッジ モジュールを使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードでオブジェクトを追跡する方法を示します。 コンピューター ビジョン モデルを適用して、ライブ ビデオ フィードのフレームのサブセット内でオブジェクトを検出する方法を示します。 その後、オブジェクト トラッカー ノードを使用して、他のフレーム内でそれらのオブジェクトを追跡できます。

オブジェクト トラッカーは、すべてのフレーム内のオブジェクトを検出する必要があるが、すべてのフレームにビジョン モデルを適用するのに必要なコンピューティング能力がエッジ デバイスにない場合に役立ちます。 たとえば、ライブ ビデオ フィードが 30 フレーム/秒で、毎回 15 番目のフレームでしかコンピューター ビジョン モデルを実行できない場合、オブジェクト トラッカーではそのようなフレームの 1 つから結果を取得し、[光学フロー](https://en.wikipedia.org/wiki/Optical_flow)手法を使用して、モデルが次のフレームで再度適用されるまで、2 番目、3 番目、...、14 番目のフレームの結果を生成します。

このクイックスタートでは、IoT Edge デバイスとして Azure VM を使用し、シミュレートされたライブ ビデオ ストリームも使用します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/object-tracker-topology.svg" alt-text="ライブ ビデオでオブジェクトを追跡する":::

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](../pipeline.md#http-extension-processor)ノードにビデオ フレームを送信します。

HTTP 拡張ノードは、プロキシの役割を果たします。 毎回 15 番目のビデオ フレームが、指定した画像の種類に変換されます。 次に、その画像は、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに HTTP 経由で転送されます。 この例の場合、そのエッジ モジュールでは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) モデルが使用されています。 HTTP 拡張プロセッサ ノードでは検出結果を受け取って、これらの結果と、(15 番目のフレームだけではなく) すべてのビデオ フレームを[オブジェクト トラッカー](../pipeline.md#object-tracker-processor) ノードに送信します。 オブジェクト トラッカー ノードでは、光学フロー技術を使用して、AI モデルが適用されていない 14 個のフレーム内のオブジェクトを追跡します。 トラッカー ノードでは、その結果を IoT Hub メッセージ シンク ノードに発行します。 次に、この [IoT Hub シンク ノード](../pipeline.md#iot-hub-message-sink)では、それらのイベントを [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub) に送信します。

> [!NOTE]
> [オブジェクト トラッカー](../pipeline.md#object-tracker-processor) ノードでの精度と処理能力のトレードオフに関する説明を確認してください。

このクイックスタートでは次の作業を行います。

1. 使用する開発環境を設定する。
1. 必要なエッジ モジュールをデプロイする。
1. ライブ パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="set-up-your-development-environment"></a>開発環境を設定する
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>必要なモジュールをデプロイする

1. Visual Studio Code で、*src/edge/deployment.yolov3.template.json* ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="IoT Edge 配置マニフェストの生成":::
1. *deployment.yolov3.amd64.json* マニフェスト ファイルが *src/edge/config* フォルダーに作成されます。
1. *src/edge/config/deployment.yolov3.amd64.json* を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイを作成する\)** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::
1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、 **[avasample-iot-edge-device]** を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

    * **avaedge** という名前の Video Analyzer エッジ モジュール。
    * **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。 
    * YOLOV3 モデルを使用してさまざまなオブジェクトを検出する **yolov3** モジュール

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/track-objects-live-video/object-tracker-modules.png" alt-text= "List of deployed IoT Edge modules":::


## <a name="create-and-deploy-the-live-pipeline"></a>ライブ パイプラインを作成してデプロイする

### <a name="examine-and-edit-the-sample-files"></a>サンプル ファイルを調べて編集する

Visual Studio Code で、src/cloud-to-device-console-app フォルダーを参照します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

* **c2d-console-app.csproj**:Visual Studio Code 用のプロジェクト ファイルです。
* **operations.json**:このファイルには、実行できるさまざまな操作がリストされています。
* **Program.cs** サンプル プログラム コードです。これは次のことを実行します。
    * アプリ設定を読み込みます。
    * Azure Video Analyzer モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用して、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。
    * プログラムからの出力を **[ターミナル]** ウィンドウで、またモジュールによって生成されたイベントを **[出力]** ウィンドウで詳しく調べることができるように一時停止します。
    * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

1. operations.json ファイルを編集します。
    
    * パイプライン トポロジへのリンクを変更します。
    * "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/object-tracking/topology.json"
    * livePipelineSet で、前のリンクの値と一致するようにパイプライン トポロジの名前を編集します。
    * "topologyName" : "ObjectTrackingWithHttpExtension"
    * pipelineTopologyDelete で、名前を編集します。
    * "name" : "ObjectTrackingWithHttpExtension"
    
ブラウザーでパイプライン トポロジの URL を開き、HTTP 拡張ノードの設定を確認します。

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

ここで、`skipSamplesWithoutAnnotation` は `false` に設定されています。これは、推論結果があるかどうかにかかわらず、拡張ノードではすべてのフレームをダウンストリームのオブジェクト トラッカー ノードに渡す必要があるためです。 オブジェクト トラッカーを使用すると、約 15 フレームにわたってオブジェクトを追跡できます。 AI モデルには、処理用の最大 FPS があります。これは、`maximumSamplesPerSecond` に設定する必要がある最も高い値です。
    
## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. デバッグ セッションを開始するには、F5 キーを押します。 **[ターミナル]** ウィンドウにメッセージが出力されるのを確認できます。
1. operations.json コードは、ダイレクト メソッド `pipelineTopologyList` および `livePipelineList` の呼び出しから始まります。 前回のクイックスタートを完了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 続行するには、Enter キーを押します。
    
    ```
    -------------------------------Executing operation pipelineTopologyList-----------------------  
    Request: pipelineTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.1"
    }
    ---------------  
    Response: pipelineTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
    
    **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。
    
    * `pipelineTopologyUrl` の内容を使用する `pipelineTopologySet` への呼び出し
    * 次の本文を使用する `livePipelineSet` の呼び出し。
        
    ```json
    {
      "@apiVersion": "1.1",
      "name": "Sample-Pipeline-1",
      "properties": {
        "topologyName": "ObjectTrackingWithHttpExtension",
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
    * ライブ パイプラインとビデオのフローをアクティブにする `livePipelineActivate` への呼び出し。
    * ライブ パイプラインが実行状態であることを示す `livePipelineList` への 2 回目の呼び出し。
1. [ターミナル] ウィンドウの出力が、[続行するには Enter キーを押してください] というプロンプトで一時停止されます。 まだ Enter キーを押しないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の [出力] ウィンドウに切り替えます。 Video Analyzer エッジ モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. ライブ パイプラインは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 ライブ パイプラインを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。
1. 次の一連の呼び出しによって、リソースがクリーンアップされます。

    * `livePipelineDeactivate` の呼び出しによって、ライブ パイプラインが非アクティブ化されます。
    * `livePipelineDelete` の呼び出しによって、ライブ パイプラインが削除されます。
    * `pipelineTopologyDelete` の呼び出しによって、パイプライン トポロジが削除されます。
    * `pipelineTopologyList` の最後の呼び出しによって、リストが空であることが示されます。
    
## <a name="interpret-results"></a>結果を解釈する

ライブ パイプラインを実行すると、HTTP 拡張プロセッサ ノードから IoT Hub メッセージ シンク ノードを通過して IoT ハブに結果が渡されます。 **[出力]** ウィンドウに表示されるメッセージには、`body` セクションと `applicationProperties` セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Video Analyzer モジュールによって、アプリケーションのプロパティと本文の内容が定義されます。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

ライブ パイプラインがアクティブ化されると、RTSP ソース ノードによって、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続が試行されます。 接続に成功すると、次のイベントが出力されます。 イベントの種類は **MediaSessionEstablished** です。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

このメッセージでは、以下の詳細に注目します。

* このメッセージは、診断イベントです。 **MediaSessionEstablished** は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* applicationProperties の subject を見ると、メッセージの生成元がライブ パイプラインの RTSP ソース ノードであることがわかります。
* applicationProperties の eventType には、このイベントが診断イベントであることが示されています。
* eventTime にはイベントの発生時刻が示されています。
* body には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

## <a name="object-tracking-events"></a>オブジェクト追跡イベント

HTTP 拡張プロセッサ ノードは、0 番目、15 番目、30 番目、... というようにフレームを yolov3 モジュールに送信し、推論結果を受信します。 その後、これらの結果とすべてのビデオ フレームがオブジェクト トラッカー ノードに送信されます。 フレーム 0 でオブジェクトが検出されたとします。すると、オブジェクト トラッカーではそのオブジェクトに一意の `sequenceId` を割り当てます。 次に、フレーム 1、2、...、14 で、そのオブジェクトを追跡できる場合は、同じ `sequenceId` で結果が出力されます。 次の結果のスニペットで、`sequenceId` が繰り返されていますが、オブジェクトが移動するにしたがって、境界ボックスの場所が変化していることに注意してください。

フレーム M から:

```json
  {
    "type": "entity",
    "subtype": "objectDetection",
    "inferenceId": "4d325fc4dc7a43b2a781bf7d6bdb3ff0",
    "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
    "entity": {
      "tag": {
        "value": "car",
        "confidence": 0.95237225
      },
      "box": {
        "l": 0.0025893003,
        "t": 0.550063,
        "w": 0.1086607,
        "h": 0.12116724
      }
    }
  },
```

フレーム N から:

```json
{
  "type": "entity",
  "subtype": "objectDetection",
  "inferenceId": "317aafdab7e940388be1e4c4cc58c366",
  "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
  "entity": {
    "tag": {
      "value": "car",
      "confidence": 0.95237225
    },
    "box": {
      "l": 0.0027777778,
      "t": 0.54901963,
      "w": 0.108333334,
      "h": 0.12009804
    }
  }
},
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

* [ライブ ビデオでオブジェクトが仮想線を越えたことの検出](use-line-crossing.md)に関する記事をお読みください。
