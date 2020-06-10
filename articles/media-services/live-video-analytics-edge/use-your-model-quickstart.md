---
title: 独自のモデルを使用してライブ ビデオを分析する - Azure
description: このクイックスタートでは、コンピューター ビジョンを適用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析します。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261490"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>クイック スタート:独自のモデルを使用してライブ ビデオを分析する

このクイックスタートでは、Live Video Analytics on IoT Edge を使用して、オブジェクトを検出するためのコンピューター ビジョン モデルを適用することによって、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 ライブ ビデオ フィード内のフレームのサブセットが推論サービスに送信され、その結果が IoT Edge ハブに送信されます。 IoT Edge デバイスとして Azure VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。 この記事は、C# で作成されたサンプル コードを基にしています。

この記事は、[こちら](detect-motion-emit-events-quickstart.md)のクイックスタートを基礎にしています。 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能がマシンにインストールされていること。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) がシステムにインストールされていること。
* [こちら](detect-motion-emit-events-quickstart.md)のクイックスタートをまだ完了していない場合は、次の手順を完了してください。
     * [Azure リソースの設定](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Azure IoT Tools をインストールするときに、Docker のインストールを求められる場合があります。 これは無視してかまいません。

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する
上記の手順の一部として Azure リソースを設定するために、高速道路の交通状況の (短い) ビデオが、IoT Edge デバイスとして使用されている Azure の Linux VM にコピーされます。 このビデオ ファイルは、このチュートリアルのライブ ストリームをシミュレートするために使用されます。

[VLC Player](https://www.videolan.org/vlc/) のようなアプリケーションを使用してそれを起動し、Ctrl キーを押しながら N キーを押し、[この](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)リンクをビデオに貼り付けると再生を開始できます。 そのビデオが、多くの車両が通行している高速道路の交通状況を示すビデオであることがわかります。

以下の手順を完了すると、Live Video Analytics on IoT Edge を使用して、車両や人などのオブジェクトを検出し、関連する推論イベントを IoT Edge ハブに発行することができます。

## <a name="overview"></a>概要

![概要](./media/quickstarts/overview-qs5.png)

上の図は、このクイックスタートでのシグナルの流れを示しています。 エッジ モジュール (詳細については[こちら](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)を参照) では、RTSP サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](media-graph-concept.md#rtsp-source) ノードでは、このサーバーからビデオ フィードを取得し、[フレーム レート フィルター プロセッサ](media-graph-concept.md#frame-rate-filter-processor) ノードにビデオ フレームを送信します。 このプロセッサでは、[HTTP 拡張プロセッサ](media-graph-concept.md#http-extension-processor) ノードに到達するビデオ ストリームのフレーム レートを制限します。 

HTTP 拡張ノードは、ビデオ フレームを指定された画像の種類に変換し、HTTP エンドポイントの背後で AI モデルを実行している別の Edge モジュールに REST を介して画像を中継することで、プロキシの役割を果たします。 この例では、その Edge モジュールは、さまざまな種類のオブジェクトを検出できる [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) モデルを使用して構築されています。 HTTP 拡張プロセッサ ノードでは、検出結果を収集し、イベントを [IoT Hub シンク](media-graph-concept.md#iot-hub-message-sink ) ノードに発行します。これにより、それらのイベントが [IoT Edge ハブ](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)に送信されます。

このクイックスタートでは次の作業を行います。

1. メディア グラフを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。



## <a name="create-and-deploy-the-media-graph"></a>メディア グラフを作成してデプロイする
    
### <a name="examine-and-edit-the-sample-files"></a>サンプル アプリを確認して編集する

前提条件の一部として、サンプル コードをフォルダーにダウンロードしました。 Visual Studio Code を起動し、そのフォルダーを開きます。

1. Visual Studio Code で "src/edge" に移動します。 作成した .env ファイルが、いくつかの展開テンプレート ファイルと共に確認できます。

    * 展開テンプレートでは、いくつかのプレースホルダー値で、エッジ デバイスの配置マニフェストが参照されています。 それらの変数の値が .env ファイルに格納されています。
1. 次に、"src/cloud-to-device-console-app" フォルダーに移動します。 ここには、作成した appsettings.json ファイルのほか、いくつかのファイルが確認できます。

    * c2d-console-app.csproj - これは Visual Studio Code のプロジェクト ファイルです。
    * operations.json - このファイルには、プログラムで実行するさまざまな操作がリストされます。
    * Program.cs - これはサンプル プログラムのコードです。次の処理を実行します。

        * アプリ設定を読み込みます。
        *  Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 ライブ ビデオ ストリームの分析は、モジュールを使用し、その[ダイレクト メソッド](direct-methods.md)を呼び出すことによって行うことができます。 
        * プログラムからの出力をターミナル ウィンドウで、また、モジュールによって生成されたイベントを出力ウィンドウで詳しく調べることができるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。   


1. operations.json ファイルに次の編集を行います。
    * グラフ トポロジへのリンクを変更します: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * GraphInstanceSet で、上のリンクの値と一致するようにグラフ トポロジの名前を編集します: `"topologyName" : "InferencingWithHttpExtension"`
    * GraphTopologyDelete で、名前を編集します: `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

1. "src/edge/ deployment.yolov3.template.json" ファイルを右クリックし、[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\) をクリックします。

    ![IoT Edge 配置マニフェストの生成](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. これで、"deployment.yolov3.amd64.json" という名前のマニフェスト ファイルが src/edge/config フォルダーに作成されます。
1. [クイックスタート](detect-motion-emit-events-quickstart.md)を既に完了している場合は、この手順をスキップします。 そうでない場合は、左下隅の [Azure IoT Hub] ペインの横にある [その他のアクション] アイコンをクリックして、IoTHub 接続文字列を設定します。 この文字列は、appsettings.json ファイルからコピーできます (Visual Studio Code で適切な IoT Hub 構成を確実に行うためのもう 1 つの推奨される方法として、[IoT Hub の選択コマンド](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)を使用する方法があります)。
    
    ![IoTHub 接続文字列](./media/quickstarts/set-iotconnection-string.png)
1. 次に、"src/edge/config/deployment.yolov3.amd64.json" を右クリックし、[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\) をクリックします。 

    ![単一デバイスのデプロイを作成する](./media/quickstarts/create-deployment-single-device.png)
1. その後、IoT Hub デバイスを選択するように求められます。 ドロップダウンから [lva-sample-device] を選択します。
1. 30 秒ほど経過したら、左下のセクションで Azure IoT Hub を最新の情報に更新すると、エッジ デバイスに次のモジュールがデプロイされています。

    1. "lvaEdge" という名前の Live Video Analytics モジュール。
    1. "rtspsim" という名前のモジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。
    1. "yolov3" という名前のモジュール。これは、その名前が示すように、コンピューター ビジョンを画像に適用し、複数のオブジェクトの種類のクラスを返す YOLOv3 オブジェクト検出モデルです。
 
        ![YOLOv3 オブジェクト検出モデル](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>イベントを監視するための準備をする

Live Video Analytics デバイスを右クリックし、[組み込みイベント エンドポイントの監視を開始する] をクリックします。 この手順は、IoT Hub イベントを監視し、Visual Studio Code の出力ウィンドウで確認するために必要です。 

![監視の開始](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. デバッグ セッションを開始します (F5 キーを押します)。 ターミナル ウィンドウへのメッセージ出力が開始されます。
1. operations.json は、ダイレクト メソッド GraphTopologyList と GraphInstanceList の呼び出しで開始されます。 前回のクイックスタート後、リソースをクリーンアップした場合は、空のリストが返された後、一時停止して、Enter キーの入力待ち状態になります。
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. ターミナル ウィンドウで Enter キーを押すと、次に示す一連のダイレクト メソッドの呼び出しが実行されます。
     * 前出の topologyUrl を使用した GraphTopologySet の呼び出し
     * 次の body を使用した GraphInstanceSet の呼び出し
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
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
     * GraphInstanceActivate を呼び出すことでグラフ インスタンスが起動し、ビデオのフローが開始されます。
     * GraphInstanceList の 2 回目の呼び出しで、実際にグラフ インスタンスが実行状態として表示されます。
1. ターミナル ウィンドウの出力が、今度は "Press Enter to continue (続行するには Enter キーを押してください)" というプロンプトで一時停止状態となります。 この時点では Enter キーは押さないでください。 上にスクロールすると、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認できます。
1. ここで Visual Studio Code の出力ウィンドウに切り替えると、Live Video Analytics on IoT Edge モジュールから IoT Hub に送信されているメッセージが表示されます。
     * これらのメッセージについては、以降のセクションで取り上げます。
1. 引き続きメディア グラフが実行され、結果が出力されます。つまり、RTSP シミュレーターによってソース ビデオがループ処理されます。 メディア グラフを停止するには、ターミナル ウィンドウに戻って Enter キーを押します。 次の一連の呼び出しによって、リソースがクリーンアップされます。
     * GraphInstanceDeactivate を呼び出すことでグラフ インスタンスが非アクティブ化されます。
     * GraphInstanceDelete を呼び出すことでインスタンスが削除されます。
     * GraphTopologyDelete を呼び出すことでトポロジが削除されます。
     * 最後に、GraphTopologyList を呼び出すことで、リストが空になったことが示されます。

## <a name="interpret-results"></a>結果を解釈する

メディア グラフを実行すると、HTTP 拡張プロセッサ ノードから IoT Hub シンク ノードを介して IoT Hub に結果が送信されます。 Visual Studio Code の出力ウィンドウに表示されるメッセージには、"body" セクションと "applicationProperties" セクションが含まれています。 これらのセクションが表す内容については、[こちら](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)の記事を参照してください。

以下のメッセージでは、アプリケーションのプロパティと body の内容が Live Video Analytics モジュールによって定義されています。 



### <a name="mediasession-established-event"></a>MediaSession Established イベント

メディア グラフがインスタンス化されると、RTSP ソース ノードは、rtspsim-live55 コンテナーで実行されている RTSP サーバーへの接続を試みます。 成功した場合、このイベントが出力されます。 イベントの種類は、Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished です。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

上のメッセージに関して、次の点に注意してください。
* このメッセージは Diagnostics イベントです。MediaSessionEstablished は、RTSP ソース ノード (subject) が RTSP シミュレーターとの接続を確立し、(シミュレートされた) ライブ フィードの受信を開始できたことを示します。
* applicationProperties の "subject" を見ると、メッセージの生成元がメディア グラフの RTSP ソース ノードであることがわかります。
* applicationProperties の "eventType" を見ると、これは Diagnostic イベントであることがわかります。
* イベントの発生時刻が "eventTime" に示されています。
* 診断イベントに関するデータ (このケースでは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細) は "body" に含まれています。

### <a name="inference-event"></a>推論イベント

HTTP 拡張プロセッサ ノードでは、yolov3 モジュールから推論結果を受け取り、それらを推論イベントとして IoT Hub シンク ノードを介して出力します。 これらのイベントでは、種類が "entity" に設定されおり、それが車やトラックなどのエンティティであることを示しています。また、eventTime はオブジェクトが検出された時刻 (UTC) を示します。 次に示すのは、同じビデオ フレーム内で、信頼度のレベルが異なる 2 台の車が検出された場合の例です。

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

上のメッセージに関して、次の点に注意してください。

* applicationProperties の "subject" が、メッセージの生成元となったグラフ トポロジ内のノードを参照しています。 
* applicationProperties の "eventType" を見ると、これは Analytics イベントであることがわかります。
* イベントの発生時刻が "eventTime" に示されています。
* "body" に、分析イベントに関するデータが含まれています。 このケースでは、Inference イベントであるため、body には "inferences" データが存在します。
* "inferences" セクションを見ると、"type" が "entity" であること、また、"entity" についての詳しいデータが存在することがわかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これから他のクイックスタートに取り組む場合は、作成したリソースをそのまま残しておく必要があります。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このクイックスタートで使用したリソース グループを選択して、それらのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP のサポートを含む [IP カメラ](https://en.wikipedia.org/wiki/IP_camera) を使用します。 RTSP をサポートする IP カメラを検索するには、[ONVIF 準拠](https://www.onvif.org/conformant-products/)製品のページでプロファイル G、S、または T に準拠しているデバイスを検索します。
* AMD64 または X64 Linux デバイスを使用します (Azure Linux VM を使用するのではなく)。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux に Azure IoT Edge ランタイムをインストールする](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)手順に従い、次にこの「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)」クイックスタートの手順に従って、Azure IoT Hub にデバイスを登録することができます。

