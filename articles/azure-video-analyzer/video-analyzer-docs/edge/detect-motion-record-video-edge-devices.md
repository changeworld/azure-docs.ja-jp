---
title: モーションの検出とエッジ デバイス上でのビデオの記録 - Azure
description: Azure Video Analyzer を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析します。 ここでは、なんらかの動きがあるかどうかを検出し、ある場合には、MP4 ビデオ クリップをエッジ デバイス上のローカル ファイル システムに記録する方法を示します。 このクイックスタートでは、IoT Edge デバイスとして Azure VM を使用するほか、シミュレートされたライブ ビデオ ストリームも使用します。
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 84dbe38e69b9dea259c570ef229927b75aefbe23
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493422"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>クイック スタート:モーションの検出とエッジ デバイス上でのビデオの記録

[!INCLUDE [header](includes/edge-env.md)]

このクイックスタートでは、Azure Video Analyzer を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 ここでは、なんらかの動きがあるかどうかを検出し、ある場合には、MP4 ビデオ クリップをエッジ デバイス上のローカル ファイル システムに記録する方法を示します。 このクイックスタートでは、IoT Edge デバイスとして Azure VM を使用するほか、シミュレートされたライブ ビデオ ストリームも使用します。

## <a name="prerequisites"></a>前提条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

このクイックスタートの Azure リソースを設定する際に、駐車場の短いビデオが、IoT Edge デバイスとして使用される Azure の Linux VM にコピーされます。 このビデオ ファイルは、このチュートリアルのライブ ストリームをシミュレートするために使用されます。

[VLC メディア プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを起動し、Ctrl キーを押しながら N キーを押して、[このリンク](https://avamedia.blob.core.windows.net/public/lots_015.mkv)を駐車場ビデオに貼り付けて再生を開始します。 だいたい 5 秒の地点で、白の車が駐車場を通って移動します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

次の手順を完了すると、Video Analyzer を使用してその車の動きが検出され、5 秒のマークあたりから始まるビデオ クリップが録画されます。

## <a name="examine-and-edit-the-sample-files"></a>サンプル ファイルを調べて編集する

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>配置マニフェストを生成してデプロイする

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. Visual Studio Code で [拡張機能] タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. 右クリックして、 [拡張機能の設定] を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text= "Extension settings":::

1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text= "Show Verbose Message":::

1.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. _operations.json_ コードにより、ダイレクト メソッド `pipelineTopologyList` および `livePipelineList` が呼び出されます。 前回のクイックスタート後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 Enter キーを押します。

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

   [ターミナル] ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。

   - pipelineTopologyUrl を使用する `pipelineTopologySet` の呼び出し
   - 次の本文を使用する `livePipelineSet` の呼び出し。

     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
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

   - ライブ パイプラインとビデオのフローを開始する `livePipelineActivate` の呼び出し。
   - ライブ パイプラインが実行状態であることを示す `livePipelineList` への 2 回目の呼び出し。

1. [ターミナル] ウィンドウの出力が、[続行するには Enter キーを押してください] で一時停止されます。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の [出力] ウィンドウに切り替えます。 Video Analyzer エッジ モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. パイプライン トポロジは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 パイプライン トポロジを停止するには、[ターミナル] ウィンドウに戻り、Enter キーを選択します。

次の一連の呼び出しによって、リソースがクリーンアップされます。

- `livePipelineDeactivate` への呼び出しによって、ライブ パイプラインが非アクティブ化されます。
- `livePipelineDelete` の呼び出しによって、ライブ パイプラインが削除されます。
- `pipelineTopologyDelete` の呼び出しによって、トポロジが削除されます。
- `pipelineTopologyList` の最後の呼び出しによって、リストが空になっていることが示されます。

## <a name="interpret-results"></a>結果を解釈する

パイプライン トポロジを実行すると、モーション検出プロセッサ ノードの結果が IoT Hub シンク ノードを介して IoT バブに渡されます。 Visual Studio Code の出力ウィンドウに表示されるメッセージには、body セクションと applicationProperties セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージ内のアプリケーションのプロパティと body の内容は、Video Analyzer エッジ モジュールによって定義されています。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

パイプライン トポロジがインスタンス化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 接続に成功すると、次のイベントが出力されます。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
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

前の出力は、次のような内容を表します。

- メッセージは診断イベント MediaSessionEstablished です。 これは、RTSP ソース ノード (subject) が RTSP シミュレーターとの接続を確立し、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
- applicationProperties の subject は、メッセージの生成元となったパイプライン トポロジ内のノードを参照しています。 このケースでは、RTSP ソース ノードからメッセージが生成されています。
- applicationProperties の eventType には、このイベントが診断イベントであることが示されています。
- eventTime 値は、イベントが発生した時刻です。
- body セクションには、診断イベントに関するデータが含まれています。 このケースでは、データはセッション記述プロトコル (SDP) の詳細で構成されています。

### <a name="recordingstarted-event"></a>RecordingStarted イベント

モーションが検出されると、シグナル ゲート プロセッサ ノードがアクティブになり、パイプライン トポロジのファイル シンク ノードが MP4 ファイルの書き込みを開始します。 ファイル シンク ノードは、操作イベントを送信します。 type は、これがモーション検出プロセッサからの結果であることを示す motion に設定されています。 eventTime 値は、モーションが発生した UTC 時刻です。 このプロセスの詳細については、このクイックスタートの「[概要](detect-motion-record-video-edge-devices.md#overview)」セクションを参照してください。

このメッセージの例を次に示します。

```
[IoTHubMonitor] [05:37:27 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation&quot;: &quot;/var/media/sampleFilesFromEVR-filesinkOutput-20210511T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-11T05:37:27.713Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

前のメッセージは、次のような内容を表します。

- applicationProperties の subject は、メッセージの生成元となったパイプライン内のノードを参照しています。 このケースでは、メッセージはファイル シンク ノードから生成されています。
- applicationProperties の eventType には、このイベントが動作中であることが示されています。
- eventTime 値は、イベントが発生した時刻です。 この時刻は、MediaSessionEstablished の後、ビデオのフローが開始されて 5 から 6 秒後です。 この時刻は、駐車場へと[車が移動を開始した](#review-the-sample-video)時点 (5 から 6 秒あたりのマーク) に対応しています。
- body セクションには、操作イベントに関するデータが含まれています。 この場合、データは outputType および outputLocation で構成されます。
- outputType 変数は、この情報がファイル パスに関するものであることを示しています。
- outputLocation 値は、エッジ モジュール内の MP4 ファイルの場所です。

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped イベントと RecordingAvailable イベント

[パイプライン トポロジ](../pipeline.md)でシグナル ゲート プロセッサ ノードのプロパティを確認すると、アクティブ化の時間が 5 秒に設定されていることがわかります。 したがって、**RecordingStarted** イベントを受信してから約 5 秒後に、次を受け取ります。

- 記録が停止したことを示す **RecordingStopped** イベント。
- MP4 ファイルを表示に使用できるようになったことを示す **RecordingAvailable** イベント。

この 2 つのイベントは、通常、数秒以上間隔を開けずに出力されます。

## <a name="play-the-mp4-clip"></a>MP4 クリップを再生する

MP4 ファイルは、VIDEO_OUTPUT_FOLDER_ON_DEVICE キーを使用して .env ファイルで構成したエッジ デバイス上のディレクトリに書き込まれます。 既定値を使用している場合、結果は /var/media/ フォルダーに配置されます。

MP4 クリップを再生するには、次のようにします。

1. リソース グループに移動して VM を見つけ、VM に接続します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/detect-motion-record-video-edge-devices/sample-iot-edge-device.png" alt-text= "Edge device":::

1. Azure リソースを設定したときに生成された資格情報を使用して、サインインします。
1. コマンド プロンプトで、関連するディレクトリに移動します。 既定の場所は /var/media です。 MP4 ファイルがこのディレクトリで見つかります。

1. [Secure Copy (SCP)](../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) を使用して、このファイルをローカル コンピューターにコピーします。
1. [VLC メディア プレーヤー](https://www.videolan.org/vlc/)またはその他の MP4 プレーヤーを使用して、ファイルを再生します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal で、ご利用のリソース グループに移動し、このクイックスタートを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

- [自分のモデルを使用した Azure Video Analyzer の実行](analyze-live-video-use-your-model-http.md)に関するクイックスタートに従って、AI をライブ ビデオ フィードに適用します。
- 上級ユーザー向けのその他の課題を確認します。

  - RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。
  - Azure では、Linux VM を使用するのではなく、AMD64 または x64 Linux デバイスを使用します。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)に関するページの手順に従います。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md?view=iotedge-2020-11&preserve-view=true)」の手順に従って、デバイスを Azure IoT Hub に登録します。
