---
title: 独自の gRPC モデルを使用してライブ ビデオを分析する
description: このクイック スタートでは、Video Analyzer を使用して独自の gRPC モデルでライブ ビデオを分析する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f71efb14d70a63c8f7884ac010dd248aa72223b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494617"
---
# <a name="quickstart-analyze-live-video-with-your-own-model---grpc"></a>クイック スタート: 独自のモデルを使用してライブ ビデオを分析する - gRPC

[!INCLUDE [header](includes/edge-env.md)]

このクイックスタートでは、Azure Video Analyzer を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 コンピューター ビジョン モデルを適用して、オブジェクトを検出する方法を確認します。 ライブ ビデオ フィード内のフレームのサブセットが、推論サービスに送信されます。 その結果が IoT Edge Hub に送信されます。

このクイックスタートでは、IoT Edge デバイスとして Azure VM を使用し、シミュレートされたライブ ビデオ ストリームも使用します。 これは、C# で記述されたサンプル コードに基づいており、クイックスタート「[モーションの検出とイベントの生成](detect-motion-emit-events-quickstart.md)」を基に構築されています。

## <a name="prerequisites"></a>前提条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>サンプル ビデオを確認する

Azure リソースを設定する際に、高速道路のトラフィックの短いビデオが、IoT Edge デバイスとして使用している、Azure の Linux VM にコピーされます。 このクイックスタートでは、このビデオ ファイルを使用してライブ ストリームをシミュレートします。

[VLC メディア プレーヤー](https://www.videolan.org/vlc/)などのアプリケーションを開きます。 Ctrl キーを押しながら N キーを押し、[ハイウェイの交差点のサンプル ビデオ](https://avamedia.blob.core.windows.net/public/camera-300s.mkv)へのリンクを貼り付けて、再生を開始します。 高速道路のトラフィック内を移動する多くの車両の映像が表示されます。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

このクイックスタートでは、Video Analyzer を使用して、車両や人物などのオブジェクトを検出します。 関連付けられている推論イベントを IoT Edge Hub に発行します。

## <a name="create-and-deploy-the-pipeline"></a>パイプラインを作成してデプロイする

### <a name="examine-and-edit-the-sample-files"></a>サンプル ファイルを調べて編集する

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

1. _src/edge/_ deployment.grpcyolov3icpu.template.json ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-grpc/generate-deployment-manifest.png" alt-text="IoT Edge 配置マニフェストの生成":::

1. _deployment.grpcyolov3icpu.amd64.json_ マニフェスト ファイルが src/edge/config フォルダーに作成されます。

1. src/edge/config/**deployment.grpcyolov3icpu.amd64.json** を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-grpc/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::

1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、[avasample-iot-edge-device] を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

   - **avaedge** という名前の Video Analyzer モジュール。
   - **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。
   - **avaextension** モジュール。これは、通信方法として gRPC を使用し、コンピューター ビジョンを画像に適用して、オブジェクトの種類のクラスを複数返す YOLOv3 オブジェクト検出モデルです。

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::

## <a name="run-the-sample-program"></a>サンプル プログラムを実行する

1. ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. **operations.json** コードは、ダイレクト メソッド pipelineTopologyList と livePipelineList の呼び出しで開始されます。 前回のクイックスタートを完了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 続行するには、Enter キーを押します。

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

1. [ターミナル] ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。

   - 前の pipelineTopologyUrl を使用する pipelineTopologySet の呼び出し
   - 次の body を使用する livePipelineSet の呼び出し:

   ```
   {
     "@apiVersion": "1.1",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "InferencingWithGrpcExtension",
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
         },
         {
           "name": "grpcExtensionAddress",
           "value": "tcp://avaextension:44000"
         }
       ]
     }
   }
   ```

   - ライブ パイプラインとビデオのフローを開始する livePipelineActivate の呼び出し。
   - ライブ パイプラインが実行状態であることを示す livePipelineList の 2 回目の呼び出し。

1. [ターミナル] ウィンドウの出力が、[続行するには Enter キーを押してください] というプロンプトで一時停止されます。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の [出力] ウィンドウに切り替えます。 Video Analyzer モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. パイプラインは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 パイプラインを停止するには、[ターミナル] ウィンドウに戻り、Enter キーを押します。

   次の一連の呼び出しによって、リソースがクリーンアップされます。

   - `livePipelineDeactivate` の呼び出しによって、ライブ パイプラインが非アクティブ化されます 
   - `livePipelineDelete` の呼び出しによって、ライブ パイプラインが削除されます。
   - `pipelineTopologyDelete` の呼び出しによって、トポロジが削除されます。
   - `pipelineTopologyList` の最後の呼び出しによって、リストが空であることが示されます。

## <a name="interpret-results"></a>結果を解釈する

パイプライン トポロジを実行すると、gRPC 拡張プロセッサ ノードから IoT Hub メッセージ シンク ノードを介して IoT ハブに結果が渡されます。 出力ウィンドウに表示されるメッセージには、body セクションと applicationProperties セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Video Analyzer モジュールによって、アプリケーションのプロパティと本文の内容が定義されます。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

パイプラインのインスタンスが作成されると、RTSP ソース ノードでは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続が試みられます。 接続に成功すると、次のイベントが出力されます。 イベントの種類は、Microsoft.VideoAnalyzer..Diagnostics.MediaSessionEstablished です。

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

このメッセージでは、以下の詳細に注目します。

- このメッセージは、診断イベントです。 MediaSessionEstablished は、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
- applicationProperties の subject を見ると、メッセージの生成元がパイプラインの RTSP ソース ノードであることがわかります。
- applicationProperties の eventType には、このイベントが診断イベントであることが示されています。
- eventTime にはイベントの発生時刻が示されています。
- body には、診断イベントに関するデータが含まれています。 このケースでは、データはセッション記述プロトコル (SDP) の詳細で構成されています。

### <a name="inference-event"></a>推論イベント

gRPC 拡張プロセッサ ノードは、avaextension モジュールから推論の結果を受け取ります。 次に、IoT Hub メッセージ シンク ノードを介して、推論イベントとして結果が出力されます。 これらのイベントでは、type が entity に設定されており、それが車やトラックなどのエンティティであることを示しています。 eventTime の値は、オブジェクトが検出された UTC 時刻です。 次の例では、同じビデオ フレーム内で、信頼度のレベルが異なる 3 台の車が検出されています。

```json
[IoTHubMonitor] [1:48:04 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145589011404622,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.97052866
        },
        "box": {
          "l": 0.40896654,
          "t": 0.60390747,
          "w": 0.045092657,
          "h": 0.029998193
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.9547283
        },
        "box": {
          "l": 0.20050547,
          "t": 0.6094412,
          "w": 0.043425046,
          "h": 0.037724357
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.94567955
        },
        "box": {
          "l": 0.55363107,
          "t": 0.5320657,
          "w": 0.037418623,
          "h": 0.027014252
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8916893
        },
        "box": {
          "l": 0.6642384,
          "t": 0.581689,
          "w": 0.034349587,
          "h": 0.027812533
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8547814
        },
        "box": {
          "l": 0.584758,
          "t": 0.60079926,
          "w": 0.07082855,
          "h": 0.034121
        }
      }
    }
  ]
}
```

メッセージでは、次の詳細に注目します。

- body セクションには、分析イベントに関するデータが含まれています。 この例では、イベントは推論イベントであるため、body には inferences データが含まれています。
- 推論セクションで、種類がエンティティであることが示されています。 このセクションには、エンティティに関する追加のデータが含まれています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

- gRPC プロトコルを使用して、さまざまなパイプライン トロポジを実行してみます。

- 上級ユーザー向けのその他の課題を確認します。

  - RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
  - Azure Linux VM ではなく、AMD64 または x64 Linux デバイスを使用してください。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2018-06&preserve-view=true)に関するページの手順を参照できます。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md?view=iotedge-2018-06&preserve-view=true)」の手順に従って、デバイスを Azure IoT Hub に登録します。
