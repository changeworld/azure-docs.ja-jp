---
title: クラウドへのイベントベースのビデオ記録とクラウドからの再生に関するチュートリアル - Azure
description: このチュートリアルでは、Live Video Analytics on IoT Edge を使用して、クラウドへのイベントベースのビデオ記録を実行し、クラウドから再生する方法について説明します。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 92367634a2f5785ecbb102db1e03f3d5f12d744e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300826"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>チュートリアル:クラウドへのイベントベースのビデオ記録とクラウドからの再生

このチュートリアルでは、Live Video Analytics on IoT Edge を使用して、クラウド内の Media Services にライブ ビデオ ソースの一部を選択的に記録する方法について説明します。 このチュートリアルでは、このユース ケースを[イベントベースのビデオ記録](event-based-video-recording-concept.md) (EVR) と呼んでいます。 これを実現するには、物体検出 AI モデルを使用してビデオ内の物体を検索し、特定の種類の物体が検出された場合にのみビデオ クリップを記録します。 また、記録したビデオ クリップを Media Services を使用して再生する方法についても説明します。 これは、関心のあるビデオ クリップのアーカイブを保持する必要があるさまざまなシナリオで役立ちます。

> [!div class="checklist"]
> * 関連リソースを設定する
> * EVR を実行するコードを確認する
> * サンプル コードを実行します。
> * 結果を確認し、ビデオを表示する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

次のドキュメントのページに目を通すことをお勧めします

* [Live Video Analytics on IoT Edge の概要](overview.md)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md) 
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)
* [チュートリアル: IoT Edge モジュールを開発する](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [deployment.*.template.json を編集する方法](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* [IoT Edge 配置マニフェスト内でルートを宣言する方法](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)に関するセクション

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです

* [Visual Studio Code](https://code.visualstudio.com/)。[Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)および [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 拡張機能と共に開発マシンにインストールされている必要があります。

    > [!TIP]
    > Docker のインストールを求められる場合があります。 このメッセージは無視してかまいません。
* 開発マシンにインストールされた [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* [Live Video Analytics リソース設定スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)と[環境の設定](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)の完了

上記の手順の最後に、次のような特定の Azure リソースが Azure サブスクリプション内にデプロイされます。

* IoT Hub
* ストレージ アカウント
* Azure Media Services アカウント
* Azure 上の Linux VM ([IoT Edge ランタイム](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)がインストール済み)

## <a name="concepts"></a>概念

イベントベースのビデオ記録 (EVR) とは、イベントによってトリガーされるビデオ記録のプロセスのことです。 そのイベントは、ビデオ信号自体の処理 (ビデオ内の動いている物体の検出時など) からや、独立したソース (ドアを開くなど) から生成されたものでもかまいません。 また、特定のイベントが発生したことを推論サービスが検出した場合にのみ記録をトリガーすることもできます。  このチュートリアルでは、高速道路を移動する車両のビデオを使用し、トラックが検出されるたびにビデオ クリップを記録します。

![メディア グラフ](./media/event-based-video-recording-tutorial/overview.png)

上の図は、目的のシナリオを実現する[メディア グラフ](media-graph-concept.md)などのモジュールを視覚的に表現したものです。 次の 4 つの IoT Edge モジュールが関係しています。

* Live Video Analytics on IoT Edge モジュール。
* HTTP エンドポイントの背後で AI モデルを実行する Edge モジュール。 この AI モジュールでは、さまざまな種類の物体を検出できる [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) モデルを使用します。
* このチュートリアル内で作成してデプロイする、物体をカウントしてフィルター処理するカスタム モジュール (上の図ではオブジェクト カウンターと呼ばれています)。
* RTSP カメラをシミュレートする[RTSP シミュレーター モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)。
    
    図に示すように、メディア グラフ内の [RTSP ソース](media-graph-concept.md#rtsp-source) ノードを使用して、シミュレートされた (高速道路のトラフィックの) ライブ ビデオをキャプチャし、そのビデオを 2 つのパスに送信します。

* 最初のパスは、指定された (低減された) フレーム レートでビデオ フレームを出力する[フレーム レート フィルター プロセッサ](media-graph-concept.md#frame-rate-filter-processor) ノードです。 これらのビデオ フレームは、HTTP 拡張ノードに送信されます。このノードは、フレームを (画像として) AI モジュール (オブジェクト検出器である YOLO v3) に中継し、結果を受け取ります。結果は、モデルによって検出された物体 (トラフィック内の車両) です。 次に、HTTP 拡張ノードは IoT Hub メッセージ シンク ノードを介して IoT Edge Hub に結果を発行します。
* オブジェクト カウンター モジュールは、IoT Edge ハブからメッセージを受信するように設定されています。メッセージには、物体検出の結果 (トラフィック内の車両) が含まれています。 これらのメッセージを確認して、(設定を介して構成された) 特定の種類の物体を探します。 そのような物体が見つかると、このモジュールは IoT Edge Hub にメッセージを送信します。 "物体が見つかった" というこれらのメッセージは、メディア グラフの IoT Hub ソース ノードにルーティングされます。 このようなメッセージを受信すると、メディア グラフの IoT Hub ソース ノードによって[シグナル ゲート プロセッサ](media-graph-concept.md#signal-gate-processor) ノードがトリガーされ、構成された時間にわたって後者が開きます。 その間、ビデオがゲートを通じて資産シンク ノードに流れます。 ライブ ストリームのその部分は、[資産シンク](media-graph-concept.md#asset-sink) ノードを介して、お使いの Azure Media Services アカウント内の[資産](terminology.md#asset)に記録されます。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

開始する前に、[前提条件](#prerequisites)の 3 番目の条件が完了していることを確認してください。 リソース設定スクリプトが完了したら、中かっこをクリックしてフォルダー構造を展開します。 ~/clouddrive/lva-sample ディレクトリに、いくつかのファイルが作成されていることがわかります。

![アプリケーション設定](./media/quickstarts/clouddrive.png)

このチュートリアルで注目するのは、次のファイルです。

* ~/clouddrive/lva-sample/edge-deployment/.env  - Visual Studio Code がエッジ デバイスにモジュールをデプロイする際に使用するプロパティが格納されます。
* ~/clouddrive/lva-sample/appsetting.json - Visual Studio Code がサンプル コードを実行するときに使用されます。

これらのファイルは、以下の手順で必要になります。

1. こちら (https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp ) からリポジトリをクローンします。
1. Visual Studio Code を起動して、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、"src/cloud-to-device-console-app" フォルダーに移動し、"appsettings.json" という名前のファイルを作成します。 このファイルには、プログラムを実行するために必要な設定が格納されます。
1. ~/clouddrive/lva-sample/appsettings.json ファイルの内容をコピーします。 このテキストは次のようになっています。

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    IoT Hub 接続文字列を使用すると、Visual Studio Code を使用して Azure IoT Hub 経由で Edge モジュールにコマンドを送信できます。
    
1. 次に、"src/edge" フォルダーに移動して、".env" という名前のファイルを作成します。
1. ~/clouddrive/lva-sample/.env ファイルの内容をコピーします。 このテキストは次のようになっています。

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>テンプレート ファイルを詳しく調べる 

前の手順では、Visual Studio Code を起動し、サンプル コードを含むフォルダーを開きました。

Visual Studio Code で "src/edge" に移動します。 作成した .env ファイルが、いくつかの展開テンプレート ファイルと共に確認できます。 このテンプレートでは、エッジ デバイス (Azure Linux VM) にデプロイするエッジ モジュールを定義します。 .env ファイルには、Media Service の資格情報など、これらのテンプレート内で使用される変数の値が含まれています。

"src/edge/deployment.objectCounter.template.json" を開きます。 "modules" セクションには、上 (「概念」セクション内) に示した項目に対応する 4 つのエントリがあります。

* lvaEdge – これは Live Video Analytics on IoT Edge モジュールです
* yolov3 – これは、YOLO v3 モデルを使用して構築された AI モジュールです
* rtspsim – これは RTSP シミュレーターです
* objectCounter – これは、yolov3 の結果から特定の物体を検索するモジュールです

objectCounter モジュールについては、"image" 値に使用されている文字列 (${MODULES.objectCounter}) を確認してください。これは、IoT Edge モジュールの開発に関する[チュートリアル](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)に基づいています。 Visual Studio Code では、オブジェクト カウンター モジュールのコードが "src/edge/modules/objectCounter" にあることを自動的に認識します。 

IoT Edge 配置マニフェスト内でルートを宣言した後、テンプレート JSON ファイル内でルートを確認する方法については、[この](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)セクションを参照してください。 次の方法に注目してください。

* LVAToObjectCounter は、objectCounter モジュール内の特定のエンドポイントに特定のイベントを送信するために使用されます。
* ObjectCounterToLVA は、lvaEdge モジュール内の特定のエンドポイント (IoT Hub ソース ノード) にトリガー イベントを送信するために使用されます。
* objectCounterToIoTHub は、デバッグ ツールとして使用され、このチュートリアルを実行するときに objectCounter からの出力を確認するのに役立ちます。

> [!NOTE]
> objectCounter モジュールの必要なプロパティを確認してください。このモジュールは、信頼レベルが 50% 以上で "トラック" としてタグ付けされた物体を検索するように設定されています。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする 

エッジ デバイスにデプロイされるモジュールとそれらのモジュールの構成設定は、配置マニフェストによって定義されます。 そのようなマニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、次の手順に従います。

Visual Studio Code を使用して、[これら](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution)の手順に従って、Docker にログインし、"IoT Edge ソリューションをビルドしてプッシュ" しますが、この手順では src/edge/deployment.objectCounter.template.json を使用します。

![IoT Edge ソリューションをビルドしてプッシュする](./media/event-based-video-recording-tutorial/build-push.png)

これにより、オブジェクト カウント用の objectCounter モジュールがビルドされ、お使いの Azure Container Registry (ACR) にイメージがプッシュされます。

* 環境変数 CONTAINER_REGISTRY_USERNAME_myacr と CONTAINER_REGISTRY_PASSWORD_myacr が .env ファイル内で定義されていることを確認してください。

上記の手順により、src/edge/config/deployment.objectCounter.amd64.json に IoT Edge 配置マニフェストが作成されます。 そのファイルを右クリックし、[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\) をクリックします。

![単一デバイスのデプロイを作成する](./media/quickstarts/create-deployment-single-device.png)

これが Live Video Analytics on IoT Edge を使用する最初のチュートリアルである場合は、IoTHub 接続文字列を入力するよう Visual Studio Code に求められます。 これは、appsettings.json ファイルからコピーできます。

次に、IoT Hub デバイスを選択するよう Visual Studio Code に求められます。 IoT Edge デバイス ("lva-sample-device") を選択します。

この段階で、お使いの IoT Edge デバイスへのエッジ モジュールのデプロイが開始されました。
30 秒ほど経過したら、Visual Studio Code 内の左下のセクションで Azure IoT Hub を最新の情報に更新します。そうすると、4 つのモジュールがデプロイされていることがわかります (再度、lvaEdge、rtspsim、yolov3、objectCounter という名前に注目してください)。

![デプロイされた 4 つのモジュール](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>イベントを監視するための準備をする

オブジェクト カウンター モジュールからと Live Video Analytics on IoT Edge モジュールからのイベントを表示するには、これらの手順を実行します。

1. Visual Studio Code の [エクスプローラー] ペインを開いて、左下隅の Azure IoT Hub を探します。
1. [Devices] ノードを展開します。
1. [lva-sample-device] を右クリックし、 **[Start Monitoring Built-in Event Monitoring]\(組み込みイベント モニタリングの監視を開始する\)** オプションを選択します。

![組み込みイベント エンドポイントの監視を開始する](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>プログラムの実行

1. Visual Studio Code で、"src/cloud-to-device-console-app/operations.json" に移動します

1. GraphTopologySet ノードで、次を編集します。

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. 次に、GraphInstanceSet と GraphTopologyDelete ノードで編集します。

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. デバッグ セッションを開始します (F5 キーを押します)。 ターミナル ウィンドウへのメッセージ出力が開始されます。

1. operations.json は、GraphTopologyList と GraphInstanceList の呼び出しで開始されます。 前回のクイックスタートまたはチュートリアルの後に、リソースをクリーンアップした場合は、空のリストが返された後、一時停止して、以下のように Enter キーの入力待ち状態になります。

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
     * 前出の topologyUrl を使用した GraphTopologySet の呼び出し。
     * 次の body を使用した GraphInstanceSet の呼び出し。
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
    
     * グラフ インスタンスを起動し、ビデオのフローを開始する GraphInstanceActivate の呼び出し
     * 実際にグラフ インスタンスが実行状態として表示される GraphInstanceList の 2 回目の呼び出し
     
1. ターミナル ウィンドウの出力が、今度は "Press Enter to continue (続行するには Enter キーを押してください)" というプロンプトで一時停止状態となります。 この時点では Enter キーは押さないでください。 上へスクロールすると、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認できます。

1. ここで Visual Studio Code の出力ウィンドウに切り替えると、Live Video Analytics on IoT Edge モジュールから IoT Hub に送信されているメッセージが表示されます。

     * これらのメッセージについては、次のセクションで説明します。
     
1. 引き続きグラフ インスタンスが実行され、ビデオが記録されます。つまり、RTSP シミュレーターによってソース ビデオがループ処理されます。 以下のセクションで説明されているようにメッセージを確認したら、インスタンスを停止するために、ターミナル ウィンドウに戻り、Enter キーを押します。 次の一連の呼び出しによって、リソースがクリーンアップされます。

     * グラフ インスタンスを非アクティブ化する GraphInstanceDeactivate の呼び出し
     * インスタンスを削除する GraphInstanceDelete の呼び出し
     * トポロジを削除する GraphTopologyDelete の呼び出し
     * リストが空になったことを示す GraphTopologyList の最後の呼び出し

## <a name="interpret-the-results"></a>結果を解釈する 

メディア グラフを実行すると、Live Video Analytics on IoT Edge モジュールによって、特定の診断イベントと操作イベントが IoT Edge ハブに送信されます。 これらのイベントは Visual Studio Code の出力ウィンドウに表示されるメッセージで、"body" セクションと "applicationProperties" セクションが含まれています。 これらのセクションが表す内容については、「[IoT Hub メッセージを作成し、読み取る](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)」を参照してください。

以下のメッセージ内のアプリケーションのプロパティと body の内容は Live Video Analytics モジュールによって定義されています。

## <a name="diagnostic-events"></a>診断イベント

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント 

メディア グラフがインスタンス化されると、RTSP ソース ノードは、RTSP シミュレーター コンテナーで実行されている RTSP サーバーへの接続を試みます。 成功した場合、このイベントが出力されます。 イベントの種類は、Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished です。

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* このメッセージは Diagnostics イベントです。MediaSessionEstablished は、RTSP ソース ノード (subject) が RTSP シミュレーターとの接続を確立し、(シミュレートされた) ライブ フィードの受信を開始できたことを示します。

* applicationProperties の "subject" が、メッセージの生成元となったグラフ トポロジ内のノードを参照しています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。

* applicationProperties の "eventType" には Diagnostics イベントであることが示されています。

* "eventTime" にはイベントの発生時刻が示されています。これは、トラフィック ビデオ (MKV ファイル) がライブ ストリームとしてモジュールに到達し始めた時刻です。

* 診断イベントに関するデータ (このケースでは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細) は "body" に含まれています。


## <a name="operational-events"></a>操作イベント

しばらくの間メディア グラフが実行されると、最終的にオブジェクト カウンター モジュールからイベントが返されます。 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties には eventTime が含まれています。これは、YOLO v3 モジュールからの結果に対象の物体 (トラック) が含まれていることをオブジェクト カウンター モジュールが確認した時刻です。

ビデオ内で他のトラックが検出されると、これらのイベントがもっと表示されることがあります。

### <a name="recordingstarted-event"></a>RecordingStarted イベント

オブジェクト カウンターからイベントが送信されると、そのほぼ直後に種類が Microsoft.Media.Graph.Operational.RecordingStarted のイベントが表示されます

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

applicationProperties の "subject" が、このメッセージの生成元となったグラフ内の資産シンク ノードを参照しています。 body には、出力場所に関する情報が含まれています。このケースでは、ビデオが記録される Azure Media Service 資産の名前です。 この値を書き留めておきます。

### <a name="recordingavailable-event"></a>RecordingAvailable イベント

資産シンク ノードがビデオを資産にアップロードすると、種類が Microsoft.Media.Graph.Operational.RecordingAvailable のこのイベントが出力されます

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

このイベントは、プレーヤーまたはクライアントがビデオの再生を開始するのに十分なデータが資産に書き込まれたことを示します。 applicationProperties の "subject" が、このメッセージの生成元となったグラフ内の資産シンク ノードを参照しています。 body には、出力場所に関する情報が含まれています。このケースでは、ビデオが記録される Azure Media Service 資産の名前です。

### <a name="recordingstopped-event"></a>RecordingStopped イベント

[トポロジ](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)内のシグナル ゲート プロセッサ ノードのアクティブ化設定 (maximumActivationTime) を確認すると、ビデオが 30 秒間送信された後にゲートが閉じるように設定されていることがわかります。 RecordingStarted イベントの約 30 秒後に、種類が Microsoft.Media.Graph.Operational.RecordingStopped のイベントが表示されます。これは、資産シンク ノードが資産へのビデオ記録を停止したことを示します。

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

このイベントは、記録が停止したことを示します。 applicationProperties の "subject" が、このメッセージの生成元となったグラフ内の資産シンク ノードを参照しています。 body には、出力場所に関する情報が含まれています。このケースでは、ビデオが記録される Azure Media Service 資産の名前です。

## <a name="media-services-asset"></a>Media Services 資産  

グラフによって作成された Media Services 資産を確認するには、Azure portal にログインし、ビデオを表示します。

1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. ご自分のサブスクリプション内のリソースの中からお使いの Media Services アカウントを見つけ、アカウント ブレードを開きます
1. Media Services リストで、[資産] をクリックします。

    ![アセット](./media/continuous-video-recording-tutorial/assets.png)
1. sampleAssetFromEVR-LVAEdge-{DateTime} という名前の資産がリストに表示されます。これは、RecordingStarted イベントの outputLocation プロパティに指定されている名前です。 この名前の生成方法は、トポロジ内の assetNamePattern によって決定されます。
1. [資産] をクリックします。
1. 資産の詳細ページで、[ストリーミング URL] ボックスの下にある **[新規作成]** をクリックします。

    ![新しい資産](./media/continuous-video-recording-tutorial/new-asset.png)

1. 開いたウィザードで、既定のオプションをそのまま使用し、[追加] をクリックします。 詳細については、「[ビデオの再生](video-playback-concept.md)」を参照してください。

    > [!TIP]
    > [ストリーミング エンドポイントが実行されている](../latest/streaming-endpoint-concept.md)ことを確認します。
1. プレーヤーにビデオが読み込まれ、 **[再生]** をクリックして表示することができます。

> [!NOTE]
> このビデオのソースはカメラ フィードをシミュレートしたコンテナーであるため、ビデオのタイムスタンプは、グラフ インスタンスをアクティブ化した時点と、非アクティブ化した時点に関連付けられています。 [数日間の記録の再生](playback-multi-day-recordings-tutorial.md)チュートリアルに組み込まれている再生コントロールを使用している場合は、画面に表示されているビデオのタイムスタンプを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のチュートリアルに取り組む場合は、作成したリソースをそのまま残しておく必要があります。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルで使用したリソース グループを選択して、それらのリソース グループを削除してください。

## <a name="next-steps"></a>次のステップ

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラを検索するには、[ONVIF 準拠製品のページ](https://www.onvif.org/conformant-products/)でプロファイル G、S、または T に準拠しているデバイスを検索します。
* AMD64 または X64 Linux デバイスを使用します (Azure Linux VM を使用するのではなく)。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)に関するページの手順に従い、次に「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)」クイックスタートの手順に従って、Azure IoT Hub にデバイスを登録することができます。
