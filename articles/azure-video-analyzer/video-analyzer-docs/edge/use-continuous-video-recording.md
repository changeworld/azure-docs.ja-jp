---
title: 連続録画と再生に関するチュートリアル - Azure Video Analyzer
description: このチュートリアルでは、Azure Video Analyzer を使用してクラウドに連続録画し、その録画を再生する方法について説明します。
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4b01a1a02e6e9c7901f6b366ff79b1e84891e44d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493307"
---
# <a name="tutorial-continuous-video-recording-and-playback"></a>チュートリアル: 連続録画と再生

[!INCLUDE [header](includes/edge-env.md)]

このチュートリアルでは、Azure Video Analyzer を使用してクラウドへの[連続録画](../continuous-video-recording.md) (CVR) を実行し、その録画を再生する方法について説明します。 この機能は、安全性やコンプライアンスなどのシナリオで、カメラから撮影した映像のアーカイブを数日、数週間、または数年間保持する必要がある場合に便利です。また、録画したビデオの保持期間を指定することもできます。 アイテム保持ポリシーは、ビデオを保存する日数を定義します (たとえば、過去 7 日間)。詳細については、「[アイテム保持ポリシーを管理する](../manage-retention-policy.md)」の記事を参照してください。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * 関連リソースを設定する。
> * CVR を実行するコードを確認する。
> * サンプル コードを実行する。
> * 結果を確認し、ビデオを表示する。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

始める前に、以下の記事をお読みください。

* [Video Analyzer の概要](../overview.md)
* [Video Analyzer の用語](../terminology.md)
* [Video Analyzer パイプラインの概念](../pipeline.md) 
* [継続的なビデオ記録のシナリオ](../continuous-video-recording.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルの前提条件は次のとおりです。[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure にデプロイする](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="concepts"></a>概念

[こちら](../pipeline.md)の記事で説明されているように、Video Analyzer パイプラインでは次のものを定義できます。

- メディアのキャプチャ元。
- それが処理される方法。
- 結果の配信先。 
 
 CVR を実現するには、RTSP 対応カメラからのビデオをキャプチャし、それを[ビデオ リソース](../terminology.md#video)に連続録画する必要があります。 この図は、そのパイプラインをグラフィカルに表したものです。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="../media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="CVR のための Video Analyzer パイプライン":::
<!-- ./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg -->

このチュートリアルでは、[Live555 Media Server](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) を使用して構築されたエッジ モジュールを使用して、RTSP カメラをシミュレートします。 パイプライン内では、[RTSP ソース](../pipeline.md#rtsp-source) ノードを使用してライブ フィードを取得し、そのビデオを[ビデオ シンク ノード](../pipeline.md#video-sink)に送信します。これにより、ビデオが Video Analyzer アカウントに録画されます。 このチュートリアルで使用するビデオは、[幹線道路の交差点のサンプル ビデオ](https://avamedia.blob.core.windows.net/public/camera-300s.mkv)です。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

このチュートリアルでは、次のことについて説明します。

1. 使用する開発環境を設定する。
1. 必要なエッジ モジュールをデプロイする。
1. ライブ パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="set-up-your-development-environment"></a>開発環境を設定する


### <a name="get-the-sample-code"></a>サンプル コードを入手する

1. [AVA C# サンプル リポジトリ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)を複製します。
1. Visual Studio Code を開始して、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、src/cloud-to-device-console-app フォルダーに移動し、**appsettings.json** という名前のファイルを作成します。 このファイルには、プログラムを実行するために必要な設定が格納されています。
1. 上記のセットアップ手順で作成したストレージ アカウントのファイル共有を参照し、"deployment-output" ファイル共有の下にある **appsettings.json** を見つけます。 そのファイルをクリックし、[ダウンロード] ボタンをクリックします。 コンテンツは新しいブラウザー タブで開きます。これは次のようになっています。

   ```
   {
       "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
       "deviceId" : "avasample-iot-edge-device",
       "moduleId" : "avaedge"
   }
   ```

   IoT Hub 接続文字列を使用すると、Visual Studio Code を使用して Azure IoT Hub 経由でエッジ モジュールにコマンドを送信できます。 上記の JSON を **src/cloud-to-device-console-app/appsettings.json** ファイルにコピーします。

### <a name="connect-to-the-iot-hub"></a>IoT ハブに接続します

1. Visual Studio Code で、左下隅の **[Azure IoT Hub]** ペインの横にある **[その他のアクション]** アイコンを選択して、IoTHub 接続文字列を設定します。 src/cloud-to-device-console-app/appsettings.json ファイルからその文字列をコピーします。

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->
    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]
1. 30 秒ほど経過したら、左下のセクションで Azure IoT Hub を更新します。 次のモジュールがデプロイされているエッジ デバイス `avasample-iot-edge-device` が表示されます。
    - Edge ハブ (モジュール名: **edgeHub**)
    - Edge エージェント (モジュール名: **edgeAgent**)
    - Video Analyzer (モジュール名: **avaedge**)
    - RTSP シミュレーター (モジュール名: **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>モジュールの監視の準備をする

このクイックスタートまたはチュートリアルを実行すると、イベントが IoT Hub に送信されます。 それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある **[Azure IoT Hub]** を探します。
1. **[デバイス]** ノードを展開します。
1. `avasample-iot-edge-device` を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="examine-the-sample-files"></a>サンプル ファイルを詳しく調べる

Visual Studio Code で、src/cloud-to-device-console-app フォルダーを参照します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

* **c2d-console-app.csproj**:Visual Studio Code 用のプロジェクト ファイルです。
* **operations.json**:このファイルには、実行できるさまざまな操作がリストされています。
* **Program.cs** サンプル プログラム コードです。これは次のことを実行します。
    * アプリ設定を読み込みます。
    * Video Analyzer エッジ モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用して、その[ダイレクト メソッド](direct-methods.md)を呼び出すことで、ライブ ビデオ ストリームを分析できます。
    * プログラムからの出力を **[ターミナル]** ウィンドウで、またモジュールによって生成されたイベントを **[出力]** ウィンドウで詳しく調べることができるように一時停止します。
    * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。

## <a name="run-the-program"></a>プログラムの実行 

1. Visual Studio Code で、src/cloud-to-device-console-app/operations.json に移動します。
1. **pipelineTopologySet** ノードで、次を編集します。

    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json" `
1. 次に、**livePipelineSet** と **pipelineTopologyDelete** の各ノードで、**topologyName** の値が、上記パイプライン トポロジ内の **name** プロパティの値と一致していることを確認します。

    `"topologyName" : "CVRToVideoSink"`  
1. ブラウザーで[パイプライン トポロジ](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)を開き、videoName を確認します。これは `sample-cvr-video` にハードコーディングされています。 これはチュートリアルでは問題ありません。 運用環境では、それぞれの一意の RTSP カメラが必ず一意の名前を持つビデオ リソースに録画されるように注意してください。
1. F5 キーを押して、デバッグ セッションを開始します。 **[ターミナル]** ウィンドウにいくつかのメッセージが出力されます。
1. operations.json ファイルは、`pipelineTopologyList` および `livePipelineList` の呼び出しから始まります。 以前のクイックスタートまたはチュートリアルの後にリソースをクリーンアップした場合は、このアクションによって次のような空のリストが返されます。

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

    ```

1. その後、次の一連のダイレクト メソッド呼び出しが実行されます。
   * 前の `topologyUrl` を使用した `pipelineTopologySet` への呼び出し
   * 次の本文を使用した `livePipelineSet` への呼び出し
     
     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "CVRToVideoSink",
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
   * ライブ パイプラインを開始し、ビデオのフローを開始した後、ユーザーが **[ターミナル]** ウィンドウで **Enter** キーを選択するまで一時停止する `livePipelineActivate` への呼び出し
1. **[ターミナル]** ウィンドウの出力が、今度は **[続行するには Enter キーを押してください]** というプロンプトで一時停止状態となります。 この時点では **Enter** キーを選択しないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. ここで Visual Studio Code の **[出力]** ウィンドウに切り替えると、Video Analyzer エッジ モジュールから IoT Hub にメッセージが送信されているのを確認できます。 これらのメッセージについては、次のセクションで説明します。
1. ライブ パイプラインは引き続き実行されてビデオを録画します。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 記録を停止するには、 **[ターミナル]** ウィンドウに戻って **Enter** キーを押します。 次の一連の呼び出しが行われて、リソースがクリーンアップされます。

   * ライブ パイプラインを非アクティブ化する `livePipelineDeactivate` への呼び出し。
   * ライブ パイプラインを削除する `livePipelineDelete` への呼び出し。
   * ライブ パイプラインが実行状態であることを示す `livePipelineList` への 2 回目の呼び出し。
   * トポロジを削除する `pipelineTopologyDelete` への呼び出し。
   * リストが空になったことを示す `pipelineTopologyList` への最後の呼び出し。

## <a name="interpret-the-results"></a>結果を解釈する

ライブ パイプラインを実行すると、Video Analyzer エッジ モジュールによって、特定の診断と操作のイベントが IoT Edge ハブに送信されます。 これらのイベントは Visual Studio Code の **出力** ウィンドウに表示されるメッセージです。 それには、`body` セクションと `applicationProperties` セクションが含まれています。 これらのセクションが表す内容については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Video Analyzer エッジ モジュールによってアプリケーション プロパティと本文の内容が定義されています。


## <a name="diagnostics-events"></a>診断イベント 

### <a name="mediasession-established-event"></a>MediaSession Established イベント

ライブ パイプラインがアクティブになると、RTSP ソース ノードでは、rtspsim モジュールで実行されている RTSP サーバーへの接続が試行されます。 成功した場合、このイベントが出力されます。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```

* メッセージは診断イベント (**Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished**) です。 これは、RTSP ソース ノード (subject) によって RTSP シミュレーターとの接続が確立され、(シミュレートされた) ライブ フィードの受信が開始されたことを示します。
* applicationProperties の subject セクションでは、メッセージの生成元となったパイプライン トポロジ内のノードが参照されています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。
* applicationProperties の eventType セクションには、これが Diagnostics イベントであることが示されています。
* eventTime セクションにはイベントの発生時刻が示されています。
* body セクションには、診断イベントに関するデータ (このケースでは [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細) が含まれています。

## <a name="operational-events"></a>操作イベント 

### <a name="recordingstarted-event"></a>RecordingStarted イベント

ビデオ シンク ノードでメディアの録画が開始されると、種類が **Microsoft.VideoAnalyzers.Pipeline.OperationalRecordingStarted** のイベントが出力されます。

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
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

名前が示すように、**RecordingStarted** イベントは録画が開始されると送信されますが、メディア データはまだビデオ リソースにアップロードされていない可能性があります。 ビデオ シンク ノードは、メディアをアップロードすると、種類が **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** のイベントを出力します。

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
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
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
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

## <a name="playing-back-the-recording"></a>録画を再生する

ライブ パイプラインによって作成された Video Analyzer ビデオ リソースを調べるには、Azure portal にログインしてビデオを視聴します。

1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. サブスクリプション内のリソースから Video Analyzers アカウントを見つけ、アカウント ペインを開きます。
1. **[Video Analyzer]** セクションで **[ビデオ]** を選択します。
1. `sample-cvr-video` という名前で一覧に含まれているビデオが見つかります。 これは、パイプライン トポロジ ファイルで選択された名前です。
1. ビデオを選択します。
1. ビデオの詳細ページが開き、自動的に再生が開始されます。

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラを検索するには、[ONVIF 準拠製品のページ](https://www.onvif.org/conformant-products/)でプロファイル G、S、または T に準拠しているデバイスを検索します。
* AMD64 または X64 Linux デバイスを使用します (Azure Linux VM を使用するのではなく)。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md)に関するページの手順に従います。 「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md)」クイックスタートの手順に従って、Azure IoT Hub にデバイスを登録します。
