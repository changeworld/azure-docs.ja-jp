---
title: Azure Video Analyzer を使用してライブ ビデオでオブジェクトが仮想線を越えたことを検出する
description: このクイックスタートでは、Azure Video Analyzer 使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードでオブジェクトが線を越えたことを検出する方法を示します。
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 163dcc488de80a81b8ab9defdbdb7b84dfdbd302
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494598"
---
# <a name="tutorial-detect-when-objects-cross-a-virtual-line-in-a-live-video"></a>チュートリアル: ライブ ビデオでオブジェクトが仮想線を越えたことを検出する

[!INCLUDE [header](includes/edge-env.md)]

このチュートリアルでは、Azure Video Analyzer を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードでオブジェクトが仮想線を越えたことを検出する方法について説明します。 コンピューター ビジョン モデルを適用して、ライブ ビデオ フィードでフレームのサブセット内のオブジェクトを検出する方法を示します。 その後に、オブジェクト トラッカー ノードを使用して他のフレーム内でそれらのオブジェクトを追跡し、結果をライン クロッシング ノードに送信できます。

ライン クロッシング ノードを使用すると、オブジェクトが仮想線を越えたことを検出できます。 イベントには、方向 (時計回り、反時計回り) と方向ごとの合計カウンターが含まれます。  

このチュートリアルでは、IoT Edge デバイスとして Azure VM を使用し、シミュレートされたライブ ビデオ ストリームも使用します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-tracker-topology.png" alt-text="ライブ ビデオでオブジェクトが仮想線を越えたことを検出する。":::

この図は、このチュートリアルでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](../pipeline.md#http-extension-processor)ノードにビデオ フレームを送信します。

HTTP 拡張ノードは、プロキシの役割を果たします。 これによって、10 番目ごとのビデオ フレームが指定された画像の種類に変換されます。 次に、その画像は、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに HTTP 経由で転送されます。 この例では、そのエッジ モジュールは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) モデルを使用して構築されています。 HTTP 拡張プロセッサ ノードでは検出結果を収集し、それらの結果および (10 番目のフレームだけではなく) すべてのビデオ フレームをオブジェクト トラッカー ノードに送信します。 オブジェクト トラッカー ノードでは、オプティカル フロー技術を使用して、AI モデルが適用されていない 9 個のフレーム内のオブジェクトが追跡されます。 トラッカー ノードでは、その結果が IoT Hub メッセージ シンク ノードに発行されます。 次に、この [IoT Hub メッセージ シンク](../pipeline.md#iot-hub-message-sink) ノードによって、それらのイベントが [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub) に送信されます。

ライン クロッシング ノードでは、アップストリームのオブジェクト トラッカー ノードから結果が受信されます。 オブジェクト トラッカー ノードの出力には、検出されたオブジェクトの座標が含まれます。 これらの座標は、ライン クロッシング ノードによって線座標に照らして評価されます。 オブジェクトが線を越えると、ライン クロッシング ノードによってイベントが生成されます。 イベントは、IoT Edge ハブ メッセージ シンクに送信されます。 

このチュートリアルでは、次のことについて説明します。

1. 使用する開発環境を設定する。
1. 必要なエッジ モジュールをデプロイする。
1. ライブ パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. 座標を計算する方法を理解する。
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
    * **avaextension** モジュール。これは、コンピューター ビジョンを画像に適用し、オブジェクトの種類のクラスを複数返す YOLOv3 オブジェクト検出モデルです

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::


## <a name="create-and-deploy-the-live-pipeline"></a>ライブ パイプラインを作成してデプロイする

### <a name="review-sample-video"></a>サンプル ビデオを確認する

Azure リソースを設定する際に、高速道路のトラフィックの短いビデオが、IoT Edge デバイスとして使用している、Azure の Linux VM にコピーされます。 このチュートリアルでは、このビデオ ファイルを使用してライブ ストリームをシミュレートします。

[VLC メディア プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを開きます。 Ctrl キーを押しながら N キーを押し、[ハイウェイの交差点のサンプル ビデオ](https://avamedia.blob.core.windows.net/public/camera-300s.mkv)へのリンクを貼り付けて、再生を開始します。 高速道路のトラフィック内を移動する多くの車両の映像が表示されます。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

### <a name="examine-and-edit-the-sample-files"></a>サンプル ファイルを調べて編集する

Visual Studio Code で、src/cloud-to-device-console-app フォルダーを参照します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

* **c2d-console-app.csproj**:Visual Studio Code 用のプロジェクト ファイルです。
* **operations.json**:このファイルには、実行できるさまざまな操作がリストされています。
* **Program.cs** サンプル プログラム コードです。これは次のことを実行します。
    * アプリ設定を読み込みます。
    * Video Analyzer エッジ モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用して、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。
    * プログラムからの出力を **[ターミナル]** ウィンドウで、またモジュールによって生成されたイベントを **[出力]** ウィンドウで詳しく調べることができるように一時停止します。
    * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

1. operations.json ファイルを編集します。
    
    * パイプライン トポロジへのリンクを変更します。
    * `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/line-crossing/topology.json"`
    * `livePipelineSet` で、前のリンクの値と一致するようにトポロジの名前を編集します。
    * `"topologyName" : "LineCrossingWithHttpExtension"`
    * `pipelineTopologyDelete` で、名前を編集します。
    * `"name" : "LineCrossingWithHttpExtension"`
    
ブラウザーでパイプライン トポロジの URL を開き、HTTP 拡張ノードの設定を確認します。

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

ここで、`skipSamplesWithoutAnnotation` は `false` に設定されています。これは、推論結果があるかどうかにかかわらず、拡張ノードではすべてのフレームをダウンストリームのオブジェクト トラッカー ノードに渡す必要があるためです。 オブジェクト トラッカーを使用すると、約 15 フレームにわたってオブジェクトを追跡できます。 AI モデルには、処理用の最大 FPS があります。これは、`maximumSamplesPerSecond` に設定する必要がある最も高い値です。

また、ライン クロッシング ノードのパラメーター プレースホルダー `linecrossingName` と `lineCoordinates` も確認してください。 これらのパラメーターには既定値が用意されていますが、operations.json ファイルを使用してこれらを上書きします。 operations.json ファイルの他のパラメーターをトポロジに渡す方法 (rtsp url) を確認してください。  

 
## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. デバッグ セッションを開始するには、F5 キーを押します。 **[ターミナル]** ウィンドウにメッセージが出力されるのを確認できます。
1. operations.json コードは、ダイレクト メソッド `pipelineTopologyList` および `livePipelineList` の呼び出しから始まります。 前回のクイックスタート/チュートリアルを完了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから一時停止します。 続行するには、Enter キーを押します。
    
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
      "topologyName": "LineCrossingWithHttpExtension",
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
    * ライブ パイプラインとビデオのフローを開始する `livePipelineActivate` の呼び出し。
    * ライブ パイプラインが実行状態であることを示す `livePipelineList` の 2 回目の呼び出し。
1. [ターミナル] ウィンドウの出力が、[続行するには Enter キーを押してください] というプロンプトで一時停止されます。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の [出力] ウィンドウに切り替えます。 Video Analyzer モジュールから IoT ハブに送信されているメッセージが表示されます。 このチュートリアルの次のセクションでは、これらのメッセージについて説明します。
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
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafetaria-01.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T17:33:09.554Z",
    "dataVersion": "1.0"
  },
  }
}
```

このメッセージでは、以下の詳細に注目します。

* このメッセージは、診断イベントです。 **MediaSessionEstablished** は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* `applicationProperties` 内の "subject" は、メッセージがライブ パイプラインの RTSP ソース ノードから生成されたことを示しています。
* `applicationProperties` 内の "eventType" は、このイベントが診断イベントであることを示します。
* "eventTime" にはイベントの発生時刻が示されています。
* body には、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

## <a name="line-crossing-events"></a>ライン クロッシング イベント

HTTP 拡張プロセッサ ノードは、0 番目、15 番目、30 番目、... というようにフレームを avaextension モジュールに送信し、推論結果を受信します。 その後、これらの結果とすべてのビデオ フレームがオブジェクト トラッカー ノードに送信されます。 オブジェクト トラッカーでは、すべてのフレーム (0、1、2 など) の推論結果が生成されます。その後これらは、ライン クロッシング ノードによってトポロジで指定された線座標に照らして評価されます。 オブジェクトがこれらの座標を越えると、イベントがトリガーされます。 イベントは次のように表されます。
```
{
  "body": {
    "timestamp": 145865319410261,
    "inferences": [
      {
        "type": "event",
        "subtype": "lineCrossing",
        "inferenceId": "8f4f7b25d6654536908bcfe34374a15e",
        "relatedInferences": [
          "c9ea5decdd6a487089ded249c748cf5b"
        ],
        "event": {
          "name": "LineCrossing1",
          "properties": {
            "counterclockwiseTotal": "35",
            "direction": "counterclockwise",
            "total": "38",
            "clockwiseTotal": "3"
          }
        }
      }
    ]
  },
```
このメッセージでは、以下の詳細に注目します。
* タイプが `event` であり、サブタイプが `lineCrossing` です。
* イベントには、越えられた線のトポロジで指定された `name` が含まれています。
* いずれかの方向で線が越えられた `total` の数。
* 超えられた `clockwiseTotal` の数。
* 超えられた `counterclockwiseTotal` の数。
* `direction` には、このイベントの方向が含まれています。

> [!NOTE] 
> このチュートリアルのワンクリック デプロイを使用して Azure リソースをデプロイした場合は、Standard DS1 仮想マシンが作成されます。 ただし、YOLO のように多くのリソースを消費する AI モデルから正確な結果を得るには、VM サイズの増加が必要な場合があります。 [VM のサイズを変更](../../../virtual-machines/windows/resize-vm.md)し、要件に基づいて vCPU の数とメモリを増やします。 次に、ライブ パイプラインを再アクティブ化して推論を表示します。

## <a name="customize-for-your-own-environment"></a>独自の環境に合わせてカスタマイズする

このチュートリアルでは、提供されているサンプル ビデオ (線の正しい線座標が算出されている) を使用して作業します。 トポロジ ファイルを調べると、`lineCoordinates` パラメーターに値 `[[0.5,0.1], [0.5,0.9]]` が含まれていることがわかります。

この値は何を意味するのでしょうか。 2D 画像上に線を描画する場合、A と B の 2 つの点が必要であり、それらの点の間に想像上の線を引きます。 各点にはその独自の x および y 座標があり、最大画像解像度を想定して、それがどこにあるかが決定します。 この場合、点 A は `[0.5,0.1]`、点 B は `[0.5,0.9]` です。 その線のビジュアル表現は次のようになります。
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-visual-example.png" alt-text="画像上に引かれた線を示すビジュアルの例。":::

この画像では、点 A と点 B の間に線が示されています。この線を越えて移動するオブジェクトがあると、このチュートリアルで前に説明したような、方向などのプロパティを持つイベントが作成されます。 また、左下隅の x および y 軸にも注目してください。 これは、ライン クロッシング ノードで想定されている値に対して座標をどのように正規化するかを説明する図を示すことだけを目的としています。 

計算の例を次に示します。ビデオ解像度は 1920 x 1080 とします。 1920 と 1080 は、それぞれ x および y 軸に沿ったピクセルの数です。
使用しようとしているビデオのフレームから画像を作成します。 次に、その画像をイメージ エディター プログラム (MSPaint) で開きます。 点 A を指定する場所にカーソルを移動します。左下隅には、そのカーソル位置の x および y 座標が示されています。
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-mspaint-coordinates.png" alt-text="MSPaint を使用した、線を越えている様子を示すビジュアルの例。":::

これらの値を書き留め、点 B でも同じことを繰り返し、同じ値を書き留めます。 ここまでで、点 A の x および y 値、および点 B の x および y 値が得られました。例: 点 A: x = 1024、y = 96、点 B: x = 1024、y = 960。これらの値は、ライン クロッシング ノードに入る値のように思われません。必要な数値は 0 から 1 までの数値であるためです。 これを計算するには、次の式を適用します。

`x coordinate / image resolution along x axis`。この例では `1024/1920 = 0.5` です。 次に、y でも同じことを行います (`96/1080=0.1`)。 これらは、点 A の正規化された座標です。点 B でもこれを繰り返します。最終的に、このチュートリアルで前述したように、0 から 1 の間の値の配列 `[[0.5,0.1], [0.5,0.9]]` が得られます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

* ライブ パイプラインを使用して別のビデオを実行してみてください。
