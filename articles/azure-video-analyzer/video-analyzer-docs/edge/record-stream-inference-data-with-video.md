---
title: ビデオを使用して推論メタデータを記録およびストリーム配信する - Azure Video Analyzer
description: このチュートリアルでは、Azure Video Analyzer を使用してビデオと推論メタデータをクラウドに記録し、ビジュアル推論メタデータを使用して記録を再生する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 67331e8b39a7c059da31215dd971988009a0ee36
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491983"
---
# <a name="tutorial-record-and-stream-inference-metadata-with-video"></a>チュートリアル: ビデオを使用して推論メタデータを記録およびストリーム配信する

[!INCLUDE [header](includes/edge-env.md)]

このチュートリアルでは、Azure Video Analyzer を使用してライブ ビデオと推論メタデータをクラウドに記録し、ビジュアル推論メタデータを使用してその記録を再生する方法について説明します。 このケースでは、カスタム モデルを使用してオブジェクト **(yoloV3)** を検出し、Video Analyzer プロセッサ **(オブジェクト トラッカー)** を使用してオブジェクトを追跡しながら、ビデオを継続的に記録します。 ビデオが継続的に記録されているため、検出および追跡されているオブジェクトからの推論メタデータも記録されます。 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

始める前に、以下の記事をお読みください。

* [Azure Video Analyzer on IoT Edge の概要](../overview.md)
* [Azure Video Analyzer on IoT Edge の用語](../terminology.md)
* [Video Analyzer パイプラインの概念](../pipeline.md) 
* [継続的なビデオ記録](../continuous-video-recording.md)
* [クイック スタート:独自のモデルを使用してライブ ビデオを分析する - HTTP](analyze-live-video-use-your-model-http.md)
* [クイックスタート: ライブ ビデオでオブジェクトを追跡する](track-objects-live-video.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure にデプロイする](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/record-stream-inference-data-with-video/overview.svg" alt-text="パイプライン":::

図は、目的のシナリオを実現する[パイプライン](../pipeline.md)と追加のモジュールを視覚的に表現したものです。 次の 3 つの IoT Edge モジュールが関与します。
* Video Analyzer モジュール。
* HTTP エンドポイントの背後で AI モデルを実行する Edge モジュール。 この AI モジュールでは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) モデルを使用します。
* RTSP カメラをシミュレートする[RTSP シミュレーター モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)。

図に示すように、パイプライン内の [RTSP ソース](../pipeline.md#rtsp-source) ノードを使用して、シミュレートされた (高速道路のトラフィックの) ライブ ビデオをキャプチャし、そのビデオを次の 2 つのパスに送信します。

* 1 つ目は、HTTP 拡張ノードへのパスです。 HTTP 拡張ノードは、プロキシの役割を果たします。 これによって、10 番目ごとのビデオ フレームが指定された画像の種類に変換されます。 次に、その画像は、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに HTTP 経由で転送されます。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる YOLOv3 モデルを使用して構築されています。 HTTP 拡張プロセッサ ノードによって、検出結果が収集され、それらの結果および (10 番目のフレームだけではなく) すべてのビデオ フレームがオブジェクト トラッカー ノードに送信されます。 オブジェクト トラッカー ノードでは、オプティカル フロー技術を使用して、AI モデルが適用されていない 9 個のフレーム内のオブジェクトが追跡されます。 トラッカー ノードによって、その結果がビデオ シンク ノードと IoT Hub シンク ノードに発行されます。 [ビデオ シンク](../pipeline.md#video-sink) ノードでは、オブジェクト トラッカー ノードからの推論メタデータを使用して、記録されたビデオが再生されます。 次に、[IoT Hub メッセージ シンク](../pipeline.md#iot-hub-message-sink) ノードによって、それらのイベントが [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub) に送信されます。

* 2 番目のパスは、RTSP ソースからビデオ シンク ノードへの直接のパスで、継続的なビデオ記録を実現します。 このチュートリアルで使用するビデオは、[幹線道路の交差点のサンプル ビデオ](https://avamedia.blob.core.windows.net/public/camera-300s.mkv)です。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

このチュートリアルでは、次のことについて説明します。

1. 使用する開発環境を設定する。
1. 必要なエッジ モジュールをデプロイする。
1. ライブ パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="set-up-your-development-environment"></a>開発環境を設定する
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="examine-the-sample-files"></a>サンプル ファイルを詳しく調べる

Visual Studio Code で src/edge に移動します。 作成した .env ファイルと、いくつかの展開テンプレート ファイルを確認できます。 このテンプレートでは、エッジ デバイス (Azure Linux VM) にデプロイするエッジ モジュールを定義します。 .env ファイルには、Video Analyzer の資格情報など、これらのテンプレート内で使用される変数の値が含まれています。

src/edge/deployment.yolov3.template.json を開きます。 **modules** セクションには、前の「概要」セクションに示した項目に対応する 3 つのエントリがあります。

* **avaedge**: これは Video Analyzer on IoT Edge モジュールです。
* **yolov3**: これは、YOLO v3 モデルを使用して構築された AI モジュールです。
* **rtspsim**:これは RTSP シミュレーターです。


次に、src/cloud-to-device-console-app フォルダーに移動します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

* **c2d-console-app.csproj**:Visual Studio Code 用のプロジェクト ファイルです。
* **operations.json**:このファイルには、実行できるさまざまな操作がリストされています。
* **Program.cs** サンプル プログラム コードです。これは次のことを実行します。
    * アプリ設定を読み込みます。

    * Video Analyzer on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用して、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。

    * プログラムからの出力を **[ターミナル]** ウィンドウで、またモジュールによって生成されたイベントを **[出力]** ウィンドウで詳しく調べることができるように一時停止します。
    * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする 

1. _src/edge/deployment.yolov3.template.json_ ファイルを右クリックし、 **[IoT Edge 配置マニフェストの生成]** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\) を示すスクリーンショット":::

    * _deployment.yolov3.amd64.json_ マニフェスト ファイルが _src/edge/config_ フォルダーに作成されます。
1. _src/edge/config/deployment.yolov3.amd64.json_ を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイを作成する\)** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、 **[avasample-iot-edge-device]** を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

   - **avaedge** モジュール。これは Video Analyzer モジュールです。
   - **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。
   - **yolov3** モジュール。これは、コンピューター ビジョンを画像に適用し、オブジェクトの種類のクラスを複数返す YoloV3 オブジェクト検出モデルです

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::
     
## <a name="create-and-deploy-the-live-pipeline"></a>ライブ パイプラインを作成してデプロイする

### <a name="edit-the-sample-files"></a>サンプル ファイルを編集する

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="拡張機能の設定":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="詳細メッセージの表示":::
1. src/cloud-to-device-console-app/operations.js に移動します。
1. **pipelineTopologySet** ノードで、次を編集します。
 
    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json" `
    
1. 次に、**livePipelineSet** と **pipelineTopologyDelete** の各ノードで、**topologyName** の値が、上記パイプライン トポロジ内の **name** プロパティの値と一致していることを確認します。

    `"pipelineTopologyName" : "CVRHttpExtensionObjectTracking"`
1. ブラウザーで[パイプライン トポロジ](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json)を開き、videoName を確認します。これは `sample-cvr-with-inference-metadata` にハードコーディングされています。 これはチュートリアルでは問題ありません。 運用環境では、それぞれの一意の RTSP カメラが必ず一意の名前を持つビデオ リソースに録画されるように注意してください。  

1. HTTP 拡張ノードの設定を確認します。

  ```=
     "samplingOptions":{
         "skipSamplesWithoutAnnotation":"false",
         "maximumSamplesPerSecond":"2"
    }
  ```

ここで、`skipSamplesWithoutAnnotation` は `false` に設定されています。これは、推論結果があるかどうかにかかわらず、拡張ノードではすべてのフレームをダウンストリームのオブジェクト トラッカー ノードに渡す必要があるためです。 オブジェクト トラッカーを使用すると、約 15 フレームにわたってオブジェクトを追跡できます。 AI モデルには、処理用の最大 FPS があります。これは、`maximumSamplesPerSecond` に設定する必要がある最も高い値です。


## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. F5 キーを押して、デバッグ セッションを開始します。 **[ターミナル]** ウィンドウにいくつかのメッセージが出力されます。
1. operations.json ファイルは、pipelineTopologyList および livePipelineList の呼び出しで開始されます。 前回のクイックスタートまたはチュートリアルの後に、リソースをクリーンアップした場合は、このアクションによって空のリストが返された後、一時停止して、以下のように **Enter** キーの入力待ち状態になります。
    ```
    --------------------------------------------------------------------------
    Executing operation pipelineTopologyList
    -----------------------  Request: pipelineTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.1"
    }
    ---------------  Response: pipelineTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
1. **[ターミナル]** ウィンドウで **Enter** キーを押すと、次に示す一連のダイレクト メソッド呼び出しが実行されます。
   * 前の pipelinetopologyUrl を使用した pipelineTopologySet の呼び出し
   * 次の body を使用した livePipelineSet の呼び出し
     
        ```
        {
          "@apiVersion": "1.1",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "CVRHttpExtensionObjectTracking",
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
   * ライブ パイプラインとビデオのフローを開始する livePipelineActivate の呼び出し。
   * ライブ パイプラインが実行状態であることを示す livePipelineList の 2 回目の呼び出し。
1. [ターミナル] ウィンドウの出力が、[続行するには Enter キーを押してください] というプロンプトで一時停止されます。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の [出力] ウィンドウに切り替えます。 Video Analyzer on IoT Edge モジュールから IoT ハブに送信されているメッセージが表示されます。
1. パイプラインは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 ライブ パイプラインを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。
1. 次の一連の呼び出しによって、リソースがクリーンアップされます。

    * livePipelineDeactivate を呼び出すと、ライブ パイプラインが非アクティブ化されます。
    * livePipelineDelete を呼び出すと、ライブ パイプラインが削除されます。
    * pipelineTopologyDelete を呼び出すと、パイプライン トポロジが削除されます。
    * 最後に pipelineTopologyList を呼び出すと、リストが空であることが示されます。       

## <a name="interpret-results"></a>結果を解釈する

ライブ パイプラインを実行すると、HTTP 拡張プロセッサ ノードからの結果が、オブジェクト トラッカー ノードを経由して、IoT Hub シンク ノード、IoT ハブに渡されます。 **出力** ウィンドウに表示されるメッセージには、body セクションと applicationProperties セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Video Analyzer モジュールによって、アプリケーションのプロパティと本文の内容が定義されます。

## <a name="diagnostics-events"></a>診断イベント
### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

ライブ パイプラインがアクティブ化されると、RTSP ソース ノードによって、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続が試行されます。 接続に成功すると、次のイベントが出力されます。 イベントの種類は、Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished です。

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

* このメッセージは、診断イベントです。 MediaSessionEstablished は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* applicationProperties の subject を見ると、メッセージの生成元がライブ パイプラインの RTSP ソース ノードであることがわかります。
* applicationProperties の eventType には、このイベントが診断イベントであることが示されています。
* eventTime にはイベントの発生時刻が示されています。
* body には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

## <a name="operational-events"></a>操作イベント

### <a name="object-tracking-events"></a>オブジェクト追跡イベント

HTTP 拡張プロセッサ ノードは、0 番目、15 番目、30 番目、... というようにフレームを yolov3 モジュールに送信し、推論結果を受信します。 その後、これらの結果とすべてのビデオ フレームをオブジェクト トラッカー ノードに送信します。 フレーム 0 でオブジェクトが検出されたとします。すると、オブジェクト トラッカーによってそのオブジェクトに一意の sequenceId が割り当てられます。 次に、フレーム 1、2、...、14 で、そのオブジェクトを追跡できる場合は、同じ sequenceId で結果が出力されます。 次の結果のスニペットで、`sequenceId` が繰り返されていますが、オブジェクトが移動するにしたがって、境界ボックスの場所が変化していることに注意してください。

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

### <a name="recordingstarted-event"></a>RecordingStarted イベント
ビデオ シンク ノードでメディアの録画が開始されると、種類が **、Microsoft.VideoAnalyzers.Pipeline.OperationalRecordingStarted** のイベントが出力されます。

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties の subject セクションでは、このメッセージの生成元となったライブ パイプライン内のビデオ シンク ノードが参照されています。

body セクションには、出力場所に関する情報が含まれています。 この場合は、ビデオが録画される Video Analyzer リソースの名前です。

### <a name="recordingavailable-event"></a>RecordingAvailable イベント

名前が示すように、RecordingStarted イベントは録画が開始されると送信されますが、メディア データがまだビデオ リソースにアップロードされていない可能性があります。 ビデオ シンク ノードは、メディアをアップロードすると、種類が **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** のイベントを出力します。

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

このイベントは、プレーヤーまたはクライアントでビデオの再生を開始するのに十分なデータがビデオ リソースに書き込まれたことを示します。

applicationProperties の subject セクションでは、このメッセージの生成元となったライブ パイプライン内のビデオ シンク ノードが参照されています。

body セクションには、出力場所に関する情報が含まれています。 この場合は、ビデオが録画される Video Analyzer リソースの名前です。

### <a name="recordingstopped-event"></a>RecordingStopped イベント

ライブ パイプラインを非アクティブ化すると、ビデオ シンク ノードではメディアの録画が停止されます。 **Microsoft.Media.Graph.Operational.RecordingStopped** の種類のイベントが出力されます。

```
[IoTHubMonitor] [11:33:31 PM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

このイベントは、記録が停止したことを示します。

applicationProperties の subject セクションでは、このメッセージの生成元となったライブ パイプライン内のビデオ シンク ノードが参照されています。

body セクションには、出力場所に関する情報が含まれています。この場合は、ビデオが録画される Video Analyzer リソースの名前です。

## <a name="streaming-the-recording-with-visual-inference-metadata"></a>ビジュアル推論メタデータを使用して記録をストリーミングする

ライブ パイプラインによって作成された Video Analyzer ビデオ リソースを調べるには、Azure portal にログインしてビデオを視聴します。

1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. サブスクリプション内のリソースの中から Video Analyzers アカウントを見つけ、アカウント ペインを開きます。
1. **[Video Analyzer]** のリストで **[ビデオ]** を選択します。
1. `sample-cvr-with-inference-metadata` という名前で一覧に含まれているビデオが見つかります。 これは、パイプライン トポロジ ファイルで選択された名前です。
1. ビデオを選択します。
1. ビデオの詳細ページで、 **[再生]** アイコンをクリックします。
1. 推論メタデータを境界ボックスとしてビデオに表示するには、(赤い円で囲まれた) **境界ボックス** アイコンをクリックします。
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/video-playback.png" alt-text="ビデオ再生のスクリーンショット":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラを検索するには、[ONVIF 準拠製品のページ](https://www.onvif.org/conformant-products/)でプロファイル G、S、または T に準拠しているデバイスを検索します。
* AMD64 または X64 Linux デバイスを使用します (Azure Linux VM を使用するのではなく)。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md)に関するページの手順に従います。 「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md)」クイックスタートの手順に従って、Azure IoT Hub にデバイスを登録します。
 
