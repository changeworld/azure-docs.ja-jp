---
title: モーションの検出とイベントの生成 - Azure
description: このクイックスタートでは、Live Video Analytics on IoT Edge を使用して、プログラムからダイレクト メソッドを呼び出すことで動きを検出し、イベントを生成する方法について説明します。
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261589"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>クイック スタート:モーションの検出とイベントの生成

このクイックスタートでは、Live Video Analytics on IoT Edge の基本的な操作手順について説明します。 IoT Edge デバイスとして Azure VM を使用すると共に、シミュレートしたライブ ビデオ ストリームを使用します。 セットアップ手順の完了後、メディア グラフを通じてライブ ビデオ ストリームのシミュレーションを実行することができます。そのストリーム内のあらゆるモーションがメディア グラフによって検出、レポートされます。 そのメディア グラフをグラフィカルに表したものが次の図です。

![モーション検出に基づく Live Video Analytics](./media/analyze-live-video/motion-detection.png) 

この記事は、C# で作成された[サンプル コード](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp)を基にしています。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能がマシンにインストールされていること。
    1. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) がシステムにインストールされていること

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

    スクリプトが正常に完了すれば、前述したすべてのリソースがご利用のサブスクリプションに表示されます。

1. スクリプトが完了したら、中かっこをクリックしてフォルダー構造を展開します。 ~/clouddrive/lva-sample ディレクトリに、いくつかのファイルが作成されていることがわかります。 このクイックスタートで注目するのは、次のものです。

     * ~/clouddrive/lva-sample/edge-deployment/.env  - Visual Studio Code がエッジ デバイスにモジュールをデプロイする際に使用するプロパティが格納されます
     * ~/clouddrive/lva-sample/appsetting.json - Visual Studio Code がサンプル コードを実行するときに使用されます
     
このクイックスタートの後の方で Visual Studio Code のファイルを更新する際に、これらが必要となります。 差し当たりローカル ファイルとしてコピーしておいてください。


 ![アプリケーション設定](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>開発環境を設定する

1. こちら (https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp ) からリポジトリをクローンします。
1. Visual Studio Code を起動して、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、"src/cloud-to-device-console-app" フォルダーに移動し、"appsettings.json" という名前のファイルを作成します。 このファイルには、プログラムを実行するために必要な設定が格納されます。
1. 前のセクション (手順 5. を参照) で生成した ~/clouddrive/lva-sample/appsettings.json ファイルの内容をコピーします

    このテキストは次のようになっています。

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. 次に、"src/edge" フォルダーに移動して、".env" という名前のファイルを作成します。
1. "/clouddrive/lva-sample/edge-deployment/.env ファイルの内容をコピーします。 このテキストは次のようになっています。

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>サンプル ファイルを詳しく調べる

1. Visual Studio Code で "src/edge" に移動します。 作成した .env ファイルが、いくつかの展開テンプレート ファイルと共に確認できます。

    展開テンプレートでは、いくつかのプレースホルダー値で、エッジ デバイスの配置マニフェストが参照されています。 それらの変数の値が .env ファイルに格納されています。
1. 次に、"src/cloud-to-device-console-app" フォルダーに移動します。 ここには、作成した appsettings.json ファイルのほか、いくつかのファイルが確認できます。

    * c2d-console-app.csproj - Visual Studio Code のプロジェクト ファイルです。
    * operations.json - このファイルには、プログラムで実行したいさまざまな操作がリストされています。
    * Program.cs - サンプル プログラムのコードです。次の処理を実行します。
    
        * アプリ設定を読み込みます
        * Live Video Analytics on IoT Edge モジュールによって公開されているダイレクト メソッドを呼び出します。 ライブ ビデオ ストリームの分析は、モジュールを使用し、その[ダイレクト メソッド](direct-methods.md)を呼び出すことによって行うことができます 
        * プログラムからの出力をターミナル ウィンドウで、またモジュールによって生成されたイベントを出力ウィンドウで詳しく調べることができるように一時停止します
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

エッジ デバイスにデプロイされるモジュールとそれらのモジュールの構成設定は、配置マニフェストによって定義されます。 そのようなマニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、次の手順に従います。

1. Visual Studio Code を開きます。
1. 左下隅の [Azure IoT Hub] ペインの横にある [その他のアクション] アイコンをクリックして、IoTHub 接続文字列を設定します。 この文字列は、src/cloud-to-device-console-app/appsettings.json ファイルからコピーすることができます。 

    ![IOT 接続文字列を設定する](./media/quickstarts/set-iotconnection-string.png)
1. 次に、"src/edge/deployment.template.json" ファイルを右クリックし、[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\) をクリックします。
    ![IoT Edge 配置マニフェストの生成](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    これで、"deployment.amd64.json" という名前のマニフェスト ファイルが src/edge/config フォルダーに作成されます。
1. [src/edge/config/deployment.amd64.json] を右クリックし、[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\) をクリックして、エッジ デバイスの名前を選択します。

    ![単一デバイスのデプロイを作成する](./media/quickstarts/create-deployment-single-device.png)
1. その後、IoT Hub デバイスを選択するように求められます。 ドロップダウンから [lva-sample-device] を選択します。
1. 30 秒ほど経過したら、左下のセクションで Azure IoT Hub を最新の情報に更新してください。そうすると、エッジ デバイスに次のモジュールがデプロイされていることがわかります。

    * Live Video Analytics on IoT Edge (モジュール名: "lvaEdge")
    * RTSP シミュレーター (モジュール名: "rtspsim")

RTSP シミュレーター モジュールは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)の実行時にエッジ デバイスにコピーされた保存済みのビデオ ファイルを使用してライブ ビデオ ストリームをシミュレートします。 この段階で、モジュールのデプロイは完了していますが、メディア グラフはアクティブになっていません。

## <a name="prepare-for-monitoring-events"></a>イベントを監視するための準備をする

ここでは、Live Video Analytics on IoT Edge モジュールを使用して、受信したライブ ビデオ ストリーム内で動きを検出し、IoT Hub にイベントを送信します。 それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開いて、左下隅の Azure IoT Hub を探します。
1. [Devices] ノードを展開します。
1. [lva-sample-device] を右クリックし、[Start Monitoring Built-in Event Monitoring]\(組み込みイベント モニタリングの監視を開始する\) オプションを選択します。

    ![組み込みイベント エンドポイントの監視を開始する](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

次の手順に従って、サンプル コードを実行します。
1. Visual Studio Code で、"src/cloud-to-device-console-app/operations.json" に移動します。
1. GraphTopologySet ノードで、次の項目を確認します。

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. 次に、GraphInstanceSet ノードと GraphTopologyDelete ノードで、topologyName の値が、上記グラフ トポロジの "name" プロパティの値と一致していることを確認します。

    `"topologyName" : "MotionDetection"`
    
1. デバッグ セッションを開始します (F5 キーを押します)。 ターミナル ウィンドウへのメッセージ出力が開始されます。
1. operations.json は、GraphTopologyList と GraphInstanceList の呼び出しで開始されます。 前回のクイックスタート後、リソースをクリーンアップした場合は、空のリストが返された後、一時停止して、Enter キーの入力待ち状態になります。

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
1. ターミナル ウィンドウで Enter キーを押すと、次に示す一連のダイレクト メソッドの呼び出しが実行されます
     
     * 前出の topologyUrl を使用した GraphTopologySet の呼び出し
     * 次の body を使用した GraphInstanceSet の呼び出し
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "MotionDetection",
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
1. ターミナル ウィンドウの出力が、今度は "Press Enter to continue (続行するには Enter キーを押してください)" というプロンプトで一時停止状態となります。 この時点では Enter キーは押さないでください。 上へスクロールすると、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認できます
1. ここで Visual Studio Code の出力ウィンドウに切り替えると、Live Video Analytics on IoT Edge モジュールから IoT Hub に送信されているメッセージが表示されます。
     * これらのメッセージについては、以降のセクションで取り上げます
1. 引き続きメディア グラフが実行され、結果が出力されます。つまり、RTSP シミュレーターによってソース ビデオがループ処理されます。 メディア グラフを停止するには、ターミナル ウィンドウに戻って "Enter" キーを押します。 次の一連の呼び出しによって、リソースがクリーンアップされます。
     * グラフ インスタンスを非アクティブ化する GraphInstanceDeactivate の呼び出し
     * インスタンスを削除する GraphInstanceDelete の呼び出し
     * トポロジを削除する GraphTopologyDelete の呼び出し
     * リストが空になったことを示す GraphTopologyList の最後の呼び出し

## <a name="interpret-results"></a>結果を解釈する

メディア グラフを実行すると、モーション検出プロセッサ ノードから IoT Hub シンク ノードを介して IoT Hub に結果が送信されます。 Visual Studio Code の出力ウィンドウに表示されるメッセージには、"body" セクションと "applicationProperties" セクションが含まれています。 これらのセクションが表す内容については、[こちら](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)の記事を参照してください。

以下のメッセージ内のアプリケーションのプロパティと body の内容は Live Video Analytics モジュールによって定義されています。

## <a name="mediasession-established-event"></a>MediaSession Established イベント

メディア グラフがインスタンス化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 成功した場合、このイベントが出力されます。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

* このメッセージは Diagnostics イベントです。MediaSessionEstablished は、RTSP ソース ノード (subject) が RTSP シミュレーターとの接続を確立し、(シミュレートされた) ライブ フィードの受信を開始できたことを示します。
* applicationProperties の "subject" が、メッセージの生成元となったグラフ トポロジ内のノードを参照しています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。
* applicationProperties の "eventType" には Diagnostics イベントであることが示されています。
* "eventTime" にはイベントの発生時刻が示されています。
* 診断イベントに関するデータ (このケースでは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細) は "body" に含まれています。


## <a name="motion-detection-event"></a>モーション検出イベント

モーションが検出されると、Live Video Analytics Edge モジュールから Inference イベントが送信されます。 type は、モーション検出プロセッサからの結果であることを示す "motion" に設定され、また、eventTime を見るとモーションが発生した日時 (UTC) がわかります。 次に例を示します。

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

* applicationProperties の "subject" が、メッセージの生成元となったメディア グラフ内のノードを参照しています。 このケースでは、モーション検出プロセッサ ノードからメッセージが生成されています。
* applicationProperties の "eventType" を見ると、これは Analytics イベントであることがわかります。
* "eventTime" にはイベントの発生時刻が示されています。
"body" に、分析イベントに関するデータが含まれています。 このケースでは、Inference イベントであるため、body には "timestamp" データと "inferences" データが存在します。
* "inferences" データを見ると、"type" が "motion" であること、また、その "motion" イベントについての詳しいデータが存在することがわかります。
* "box" セクションには、移動する物体を囲む境界ボックスの座標が格納されます。 これらの値は、ピクセル単位のビデオの幅と高さ (幅 1920、高さ 1080 など) で正規化されます。 width of 1920 and height of 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>リソースをクリーンアップする

これから他のクイックスタートに取り組む場合は、作成したリソースをそのまま残しておく必要があります。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このクイックスタートで使用したリソース グループを選択して、それらのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

ライブ ビデオ フィードからの物体の検出など、他のクイックスタートに取り組みます。        
