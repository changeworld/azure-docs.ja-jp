---
title: クラウドへのイベントベースのビデオ記録とクラウドからの再生に関するチュートリアル - Azure
description: このチュートリアルでは、Azure Video Analyzer を使用して、イベントベースのビデオ録画をクラウドに記録し、それをクラウドから再生する方法について説明します。
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1edec79cd7baa20819e0599b039fbdfe20f35d92
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484815"
---
# <a name="tutorial-event-based-video-recording-and-playback"></a>チュートリアル: イベントベースのビデオ記録と再生

[!INCLUDE [header](includes/edge-env.md)]

このチュートリアルでは、Azure Video Analyzer を使用して、クラウド内の Video Analyzer にライブ ビデオ ソースの一部を選択的に記録する方法について説明します。 このチュートリアルでは、このユース ケースを[イベントベースのビデオ記録](../event-based-video-recording-concept.md) (EVR) と呼んでいます。 ライブ ビデオの一部を記録するには、オブジェクト検出 AI モデルを使用してビデオ内のオブジェクトを検索し、特定の種類のオブジェクトが検出された場合にのみビデオ クリップを記録します。 また、記録したビデオ クリップを Video Analyzer を使用して再生する方法についても説明します。 この機能は、関心のあるビデオ クリップのアーカイブを保持する必要があるさまざまなシナリオで役立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

始める前に、以下の記事をお読みください。

* [Azure Video Analyzer の概要](../overview.md)
* [Azure Video Analyzer の用語](../terminology.md)
* [Video Analyzer パイプラインの概念](../pipeline.md) 
* [イベントベースのビデオ記録](../event-based-video-recording-concept.md)
* [チュートリアル:IoT Edge モジュールを開発する](../../../iot-edge/tutorial-develop-for-linux.md)
* [deployment.*.template.json を編集する方法](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* [IoT Edge 配置マニフェスト内でルートを宣言する方法](../../../iot-edge/module-composition.md#declare-routes)に関するセクション

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、無料の[アカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* マシンに [Docker がインストール](https://docs.docker.com/desktop/#download-and-install)されている。
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure にデプロイする](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

イベントベースのビデオ記録とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 このイベントは、以下から生成されます。
- ビデオ信号自体の処理 (移動するオブジェクトがビデオ内で検出された場合など)。
- 独立したソース (ドアが開いた場合など)。 

また、特定のイベントが発生したことを推論サービスが検出した場合にのみ記録をトリガーすることもできます。 このチュートリアルでは、高速道路を移動する車両のビデオを使用し、トラックが検出されるたびにビデオ クリップを記録します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/overview.svg" alt-text="パイプライン":::

図は、目的のシナリオを実現する[パイプライン](../pipeline.md)と追加のモジュールを視覚的に表現したものです。 次の 4 つの IoT Edge モジュールが関与します。

* IoT Edge モジュール上の Video Analyzer。
* HTTP エンドポイントの背後で AI モデルを実行する Edge モジュール。 この AI モジュールでは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) モデルを使用します。
* オブジェクトをカウントおよびフィルター処理するためのカスタム モジュール (図では、Object Counter (オブジェクト カウンター) となっています)。 このチュートリアルでは、オブジェクト カウンターを作成してデプロイします。
* RTSP カメラをシミュレートする[RTSP シミュレーター モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)。
    
図に示すように、パイプライン内の [RTSP ソース](../pipeline.md#rtsp-source) ノードを使用して、シミュレートされた (高速道路のトラフィックの) ライブ ビデオをキャプチャし、そのビデオを次の 2 つのパスに送信します。

* 1 つ目は、HTTP 拡張ノードへのパスです。 このノードは、`samplingOptions` フィールドを使用して設定された値にビデオ フレームをサンプリングした後、フレームを画像として AI モジュール YOLO v3 (オブジェクト検出器) にリレーします。 ノードは結果 (モデルによって検出されたオブジェクト、つまりトラフィック内の車両) を受信します。 次に、HTTP 拡張ノードは、IoT Hub メッセージ シンク ノードを介して IoT Edge ハブに結果を発行します。

* objectCounter モジュールは、IoT Edge ハブからメッセージを受信するように設定されています。このメッセージには、オブジェクト検出の結果 (トラフィック内の車両) が含まれています。 モジュールではこれらのメッセージを確認して、(設定を介して構成された) 特定の種類のオブジェクトを探します。 そのようなオブジェクトが見つかると、このモジュールから IoT Edge ハブにメッセージが送信されます。 "オブジェクトが見つかった" というこれらのメッセージは、パイプラインの IoT Hub ソース ノードにルーティングされます。 このようなメッセージを受信すると、パイプラインの IoT Hub ソース ノードによって[シグナル ゲート プロセッサ](../pipeline.md#signal-gate-processor) ノードがトリガーされます。 すると、構成された時間だけシグナル ゲート プロセッサ ノードが開きます。 その間、ビデオはゲートを通ってビデオ シンク ノードに流れます。 ライブ ストリームのその部分は、[ビデオ シンク](../pipeline.md#video-sink) ノードを介して、お使いの Video Analyzer アカウント内の[ビデオ](../terminology.md#video)に記録されます。 このチュートリアルで使用するビデオは、[幹線道路の交差点のサンプル ビデオ](https://avamedia.blob.core.windows.net/public/camera-300s.mkv)です。

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

src/edge/deployment.objectCounter.template.json を開きます。 **modules** セクションには、前の「概要」セクションに示した項目に対応する 4 つのエントリがあります。

* **avaedge**: これは Video Analyzer モジュールです。
* **yolov3**: これは、YOLO v3 モデルを使用して構築された AI モジュールです。
* **rtspsim**: これは RTSP シミュレーターです。
* **objectCounter**: これは、yolov3 の結果から特定のオブジェクトを検索するモジュールです。

ObjectCounter モジュールについては、"image" 値に使用される (${MODULES.objectCounter}) 文字列を参照してください。 これは、IoT Edge モジュールの開発に関する[チュートリアル](../../../iot-edge/tutorial-develop-for-linux.md)に基づいています。 Visual Studio Code は、objectCounter モジュールのコードが src/edge/modules/objectCounter にあることを自動的に認識します。 

IoT Edge 配置マニフェスト内でルートを宣言する方法に関する[このセクション](../../../iot-edge/module-composition.md#declare-routes)を参照してください。 次に、テンプレート JSON ファイル内でルートを確認します。 次の方法に注目してください。

> [!NOTE]
> objectCounter モジュールの必要なプロパティを確認してください。このモジュールは、信頼レベルが 50% 以上で "トラック" とタグ付けされたオブジェクトを検索するように設定されています。

次に、src/cloud-to-device-console-app フォルダーに移動します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

* **c2d-console-app.csproj**:Visual Studio Code 用のプロジェクト ファイルです。
* **operations.json**:このファイルには、実行できるさまざまな操作がリストされています。
* **Program.cs** サンプル プログラム コードです。これは次のことを実行します。
    * アプリ設定を読み込みます。

    * Azure Video Analyzer モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用して、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。

    * プログラムからの出力を **[ターミナル]** ウィンドウで、またモジュールによって生成されたイベントを **[出力]** ウィンドウで詳しく調べることができるように一時停止します。
    * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする 

エッジ デバイスにデプロイされるモジュールとそれらのモジュールの構成設定は、配置マニフェストによって定義されます。 マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、これらの手順に従います。

Visual Studio Code を使用し、[こちらの手順](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)に従って Docker にサインインします。 次に、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションをビルドしてプッシュする\)** を選択します。 この手順では、src/edge/deployment.objectCounter.template.json を使用します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/build-push.png" alt-text="IoT Edge ソリューションをビルドしてプッシュする":::

このアクションにより、オブジェクト カウント用の objectCounter モジュールがビルドされ、お使いの Azure Container Registry に画像がプッシュされます。

* 環境変数 CONTAINER_REGISTRY_USERNAME_myacr と CONTAINER_REGISTRY_PASSWORD_myacr が .env ファイル内で定義されていることを確認してください。

この手順により、src/edge/config/deployment.objectCounter.amd64.json に IoT Edge 配置マニフェストが作成されます。 そのファイルを右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/create-deployment-single-device.png" alt-text="単一デバイスのデプロイを作成する":::

これが Video Analyzer を使用する最初のチュートリアルである場合は、IoT Hub 接続文字列を入力するように Visual Studio Code から求められます。 これは、appsettings.json ファイルからコピーできます。

[!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

次に、IoT Hub デバイスを選択するように Visual Studio Code から求められます。 IoT Edge デバイス (avasample-iot-edge-device) を選択します。

この段階で、お使いの IoT Edge デバイスへのエッジ モジュールのデプロイが開始されました。
30 秒ほど経過したら、Visual Studio Code の左下のセクションで Azure IoT Hub を最新の情報に更新してください。 そうすると、$edgeAgent、$edgeHub avaedge、lvaEdge、rtspsim、yolov3、および objectCounter という名前の 6 つのモジュールがデプロイされていることがわかります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/modules.png" alt-text="デプロイされた 4 つのモジュール":::

## <a name="run-the-program"></a>プログラムの実行

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="拡張機能の設定":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="詳細メッセージの表示":::
1. src/cloud-to-device-console-app/operations.js に移動します。
1. **pipelineTopologySet** ノードで、次を編集します。


    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json" `
    
1. 次に、**livePipelineSet** と **pipelineTopologyDelete** の各ノードで、**topologyName** の値が、上記パイプライン トポロジ内の **name** プロパティの値と一致していることを確認します。

    `"pipelineTopologyName" : "EVRtoVideoSinkOnObjDetect"`
1. ブラウザーで[パイプライン トポロジ](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json)を開き、videoName を確認します。これは `sample-evr-video` にハードコーディングされています。 これはチュートリアルでは問題ありません。 運用環境では、それぞれの一意の RTSP カメラが必ず一意の名前を持つビデオ リソースに録画されるように注意してください。
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
            "topologyName": "EVRtoVideoSinkOnObjDetect",
            "description": "Sample topology description",
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
    
   * ライブ パイプラインを開始し、ビデオのフローを開始するための livePipelineActivate の呼び出し
   * ライブ パイプラインが実行状態であることを示すための livePipelineList の 2 回目の呼び出し 
     
1. **[ターミナル]** ウィンドウの出力が、今度は **[続行するには Enter キーを押してください]** というプロンプトで一時停止状態となります。 この時点では **Enter** キーは押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. ここで Visual Studio Code の **[出力]** ウィンドウに切り替えると、Video Analyzer モジュールによって IoT Hub にメッセージが送信されているのを確認できます。

   これらのメッセージについては、次のセクションで説明します。
1. ライブ パイプラインは引き続き実行されてビデオを録画します。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 記録を停止するには、 **[ターミナル]** ウィンドウに戻って **Enter** キーを押します。 次の一連の呼び出しが行われて、リソースがクリーンアップされます。

   * ライブ パイプラインを非アクティブ化するための livePipelineDeactivate の呼び出し。
   * ライブ パイプラインを削除するための livePipelineDelete の呼び出し。
   * トポロジを削除するための pipelineTopologyDelete の呼び出し。
   * リストが空になったことを示すための pipelineTopologyList の最後の呼び出し。

## <a name="interpret-the-results"></a>結果を解釈する 

ライブ パイプラインを実行すると、Video Analyzer モジュールによって、特定の診断と操作のイベントが IoT Edge ハブに送信されます。 これらのイベントは Visual Studio Code の **出力** ウィンドウに表示されるメッセージです。 それらには、body セクションと applicationProperties セクションが存在します。 これらのセクションが表す内容については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Video Analyzer モジュールによってアプリケーション プロパティと本文の内容が定義されています。

## <a name="diagnostics-events"></a>診断イベント

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント 

ライブ パイプラインがアクティブになると、RTSP ソース ノードは、RTSP シミュレーター コンテナーで実行されている RTSP サーバーへの接続を試みます。 成功した場合、このイベントが出力されます。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```


* メッセージは診断イベント (MediaSessionEstablished) です。 これは、RTSP ソース ノード (subject) によって RTSP シミュレーターとの接続が確立され、(シミュレートされた) ライブ フィードの受信が開始されたことを示します。
* applicationProperties の subject セクションでは、メッセージの生成元となったパイプライン トポロジ内のノードが参照されています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。
* applicationProperties の eventType セクションには、これが Diagnostics イベントであることが示されています。
* eventTime セクションにはイベントの発生時刻が示されています。
* body セクションには、診断イベントに関するデータ (このケースでは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細) が含まれています。

## <a name="operational-events"></a>操作イベント

パイプライン実行がしばらく行われた後、最終的に objectCounter モジュールからイベントが返されます。 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [avasample-iot-edge-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties セクションには、イベントの時刻が含まれています。 これは、objectCounter モジュールが、yolov3 モジュールの結果に対象オブジェクト (トラック) が含まれていることを確認した時刻です。

ビデオ内で他のトラックが検出された場合は、このイベントが他にも表示されているのを確認できます。

### <a name="recordingstarted-event"></a>RecordingStarted イベント

オブジェクト カウンターからイベントが送信された直後に、種類が **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted** のイベントが表示されます。

```
[IoTHubMonitor] [5:53:46 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties の subject セクションでは、このメッセージの生成元となったライブ パイプライン内のビデオ シンク ノードが参照されています。 body セクションには、出力場所に関する情報が含まれています。 この場合は、ビデオが録画される Video Analyzer ビデオ リソースの名前です。

### <a name="recordingavailable-event"></a>RecordingAvailable イベント

名前が示すように、RecordingStarted イベントは録画が開始されると送信されますが、メディア データがまだビデオ リソースにアップロードされていない可能性があります。 ビデオ シンク ノードは、メディアをアップロードすると、種類が **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** のイベントを出力します。

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

このイベントは、プレーヤーまたはクライアントでビデオの再生を開始するのに十分なデータがビデオ リソースに書き込まれたことを示します。 applicationProperties の subject セクションでは、このメッセージの生成元となったライブ パイプライン内のビデオ シンク ノードが参照されています。 body セクションには、出力場所に関する情報が含まれています。 この場合は、ビデオが録画される Video Analyzer リソースの名前です。

### <a name="recordingstopped-event"></a>RecordingStopped イベント

ライブ パイプラインを非アクティブ化すると、ビデオ シンク ノードではメディアの録画が停止されます。 **Microsoft.Media.Graph.Operational.RecordingStopped** の種類のイベントが出力されます。

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

このイベントは、記録が停止したことを示します。 applicationProperties の subject セクションでは、このメッセージの生成元となったライブ パイプライン内のビデオ シンク ノードが参照されています。 body セクションには、出力場所に関する情報が含まれています。 この場合は、ビデオが録画される Video Analyzer リソースの名前です。

## <a name="playing-back-the-recording"></a>録画を再生する

ライブ パイプラインによって作成された Video Analyzer ビデオ リソースを調べるには、Azure portal にログインしてビデオを視聴します。
1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. サブスクリプション内のリソースの中から Video Analyzers アカウントを見つけ、アカウント ペインを開きます。
1. **[Video Analyzer]** のリストで **[ビデオ]** を選択します。
1. `sample-evr-video` という名前で一覧に含まれているビデオが見つかります。 これは、パイプライン トポロジ ファイルで選択された名前です。
1. ビデオを選択します。
1. ビデオの詳細ページが開き、自動的に再生が開始されます。

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [clean-up-resources](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラを検索するには、[ONVIF 準拠製品のページ](https://www.onvif.org/conformant-products/)でプロファイル G、S、または T に準拠しているデバイスを検索します。
* AMD64 または X64 Linux デバイスを使用します (Azure Linux VM を使用するのではなく)。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md)に関するページの手順に従います。 「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md)」クイックスタートの手順に従って、Azure IoT Hub にデバイスを登録します。
