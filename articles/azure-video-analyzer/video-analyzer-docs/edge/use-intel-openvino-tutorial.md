---
title: Intel の AI 拡張機能 OpenVINO™ モデル サーバーを使用してライブ ビデオを分析する
description: このチュートリアルでは、Intel が提供する AI モデル サーバー (トレーニング済みモデルを含む) を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析します。
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
titleSuffix: Azure
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2e6e14849bfa00862b2fd87a0e42d88b315ab14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490707"
---
# <a name="tutorial-analyze-live-video-using-openvino-model-server--ai-extension-from-intel"></a>チュートリアル: Intel の AI 拡張機能 OpenVINO™ モデル サーバーを使用してライブ ビデオを分析する 

[!INCLUDE [header](includes/edge-env.md)]

このチュートリアルでは、[Intel の AI 拡張機能 OpenVINO™ モデル サーバー](https://aka.ms/ava-intel-ovms)を使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 この推論サーバーで、物体 (人物、車両、バイク) を検出するためのモデルや車両を分類するためのモデルにアクセスできるようにする方法を見ていきましょう。 ライブ ビデオ フィード内のフレームのサブセットが推論サーバーに送信され、その結果が IoT Edge ハブに送信されます。

このチュートリアルでは、IoT Edge デバイスとして Azure VM を使用し、シミュレートされたライブ ビデオ ストリームも使用します。 C# で作成されたサンプル コードをベースにしています。

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure リソースの設定

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Intel の AI 拡張機能 OpenVINO™ モデル サーバーについて

Intel® のディストリビューション [OpenVINO™ ツールキット](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (Open Visual Inference and Neural Network Optimization) は無料のソフトウェア キットで、開発者やデータ サイエンティストがコンピューター ビジョン ワークロードをスピードアップしたり、ディープ ラーニングの推論およびデプロイを効率化したり、エッジからクラウドまで Intel® プラットフォーム全体で簡単に異種実行環境を実現したりする際に役立ちます。 モデル オプティマイザーと推論エンジンを備えた Intel® ディープ ラーニング デプロイメント ツールキットのほか、40 を超える最適化された事前トレーニング済みモデルを含んだ [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) リポジトリが同梱されています。

複雑なハイパフォーマンスのビデオ分析ソリューションを構築するには、Video Analyzer モジュールに、エッジのスケールを活かせる強力な推論エンジンを組み合わせる必要があります。 このチュートリアルでは、Video Analyzer と連動するように設計された Edge モジュールである [Intel の AI 拡張機能 OpenVINO™ モデル サーバー](https://aka.ms/ava-intel-ovms)に推論要求が送信されます。 この推論サーバー モジュールには、OpenVINO™ モデル サーバー (OVMS) が含まれています。これは OpenVINO™ ツールキットを使用する推論サーバーであり、コンピューター ビジョン ワークロード向けに高度に最適化され、また Intel® アーキテクチャ向けに開発されています。 OVMS には、推論サーバーと Video Analyzer モジュールの間でビデオ フレームおよび推論結果を簡単に交換するための拡張機能が追加されています。これにより、OpenVINO™ ツールキットに対応したモデルであれば、どれでも実行することができます (推論サーバー モジュールは、[コード](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)を変更することでカスタマイズできます)。 さらに、Intel® のハードウェアが提供するさまざまなアクセラレーション メカニズムから選択することができます。 これらには、CPU (Atom、Core、Xeon)、FPGA、VPU が含まれます。

この推論サーバーの初期リリースでは、次の[モデル](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)を利用できます。

- 車両検出 (推論 URL: http://{module-name}:4000/vehicleDetection)
- 人物、車両、バイクの検出 (推論 URL: http://{module-name}:4000/personVehicleBikeDetection)
- 車両の分類 (推論 URL: http://{module-name}:4000/vehicleClassification)
- 顔検出 (推論 URL: http://{module-name}:4000/faceDetection)

> [!NOTE]
> Edge モジュール (Intel の AI 拡張機能 OpenVINO モデル サーバーとそこに含まれるソフトウェア) をダウンロードして利用したことをもって、ユーザーは[ライセンス契約](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)の利用条件に同意したものと見なされます。
> Intel は人権を尊重すること、および人権侵害の共謀を回避することに全力で取り組んでいます。 [Intel のグローバルな人権の原則](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)に関するページを参照してください。 Intel の製品やソフトウェアは、国際的に認知された人権への侵害の原因となったり侵害に寄与したりすることのないアプリケーションでの使用のみを意図しています。

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.png" alt-text="概要":::

この図は、このクイックスタートでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[HTTP 拡張プロセッサ](../pipeline-extension.md#http-extension-processor)ノードにビデオ フレームを送信します。 

HTTP 拡張プロセッサ ノードは、プロキシの役割を果たします。 これは、受信ビデオ フレームのサブセットを選択し、それらのフレームを画像に変換します。 次に、その画像を、HTTP エンドポイントの背後で AI モデルを実行する別のエッジ モジュールに、REST 経由で中継します。 この例では、そのエッジ モジュールが Intel の AI 拡張機能 OpenVINO™ モデル サーバーです。 HTTP 拡張プロセッサ ノードは、検出結果を収集し、イベントを [IoT Hub メッセージ シンク](../pipeline.md#iot-hub-message-sink) ノードに発行します。 その後、後者のノードはこれらのイベントを [IoT Edge ハブ](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub)に送信します。

このチュートリアルでは、次のことについて説明します。

1. 使用する開発環境を設定する。
1. 必要なエッジ モジュールをデプロイする。
1. ライブ パイプラインを作成してデプロイする。
1. 結果を解釈する。
1. リソースをクリーンアップする。

## <a name="set-up-your-development-environment"></a>開発環境を設定する
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

### <a name="review-the-sample-video"></a>サンプル ビデオを確認する

Azure リソースを設定する際に、IoT Edge デバイスとして使用している、Azure の Linux VM に[駐車場の短いビデオ](https://avamedia.blob.core.windows.net/public/lots_015.mkv)がコピーされます。 このクイックスタートでは、このビデオ ファイルを使用してライブ ストリームをシミュレートします。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

## <a name="deploy-the-required-modules"></a>必要なモジュールをデプロイする

前提条件の一環として、サンプル コードをフォルダーにダウンロードしてあります。 必要なモジュールをデプロイするには、次の手順に従います。

1. Visual Studio Code で、*src/edge/deployment.openvino.template.json* ファイルを右クリックし、 **[Generate IoT Edge Deployment Manifest]\(IoT Edge デプロイ マニフェストの生成\)** を選択します。   
1. *deployment.openvino.amd64.json* マニフェスト ファイルが *src/edge/config* フォルダーに作成されます。
1. *src/edge/config/deployment.openvino.amd64.json* を右クリックし、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。
1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、**avasample-iot-edge-device** を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。
    * **avaedge** という名前の Video Analyzer エッジ モジュール。
    * **rtspsim** モジュール。RTSP サーバーをシミュレートし、ライブ ビデオ フィードのソースとして機能します。 
    * **openvino** モジュール。Intel の AI 拡張機能モジュールである OpenVINO™ モデル サーバーです。
 
## <a name="create-and-activate-the-live-pipeline"></a>ライブ パイプラインを作成してアクティブ化する

### <a name="edit-the-sample-code"></a>サンプル コードを編集する
1. Visual Studio Code で、*src/cloud-to-device-console-app* を参照し、*operations.json* ファイルを編集します。
    * ライブ パイプライン トポロジへのリンクを変更します。

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json"`

    * `livePipelineSet` で、上記のリンクの値と一致するようにライブ パイプライン トポロジの名前を編集します。

      `"topologyName" : "InferencingWithOpenVINO"`

    * `pipelineTopologyDelete` で、名前を編集します。

      `"name": "InferencingWithOpenVINO"`

### <a name="run-the-sample-program-to-detect-vehicles"></a>サンプル プログラムを実行して車両を検出する
このチュートリアルのパイプライン トポロジ (`pipelineTopologyUrl`) をブラウザーで開くと、`inferencingUrl` の値が `http://openvino:4000/vehicleDetection` に設定されていることがわかります。これは、推論サーバーがライブ ビデオで車両を検出しようとしていることを意味します。

1. デバッグ セッションを開始するには、F5 キーを押します。 **[ターミナル]** ウィンドウにメッセージが出力されるのを確認できます。
1. *operations.json* コードは、ダイレクト メソッド `pipelineTopologyList` および `livePipelineList` の呼び出しから始まります。 前のクイックスタートを完了した後にリソースをクリーンアップした場合、このプロセスでは空のリストが返されます。 **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。

     * 前の `pipelineTopologyUrl` を使用する `pipelineTopologySet` の呼び出し
     * 次の本文を使用する `livePipelineSet` の呼び出し。

         ```json
            {
              "@apiVersion": "1.1",
              "name": "Sample-Pipeline-1",
              "properties": {
                "topologyName": "InferencingWithOpenVINO",
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

     * パイプラインとビデオのフローを開始する `livePipelineActivate` の呼び出し。
1. **[ターミナル]** ウィンドウの出力は `Press Enter to continue` プロンプトで一時停止します。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の **[出力]** ウィンドウに切り替えます。 Video Analyzer モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. ライブ パイプラインは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 ライブ パイプラインを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。 

    次の一連の呼び出しによって、リソースがクリーンアップされます。
      * `livePipelineDeactivate` の呼び出しによって、パイプラインが非アクティブ化されます。
      * `livePipelineDelete` の呼び出しによって、パイプラインが削除されます。
      * `pipelineTopologyDelete` の呼び出しによって、トポロジが削除されます。
      * `pipelineTopologyList` の最後の呼び出しによって、リストが空であることが示されます。

## <a name="interpret-results"></a>結果を解釈する

ライブ パイプラインを実行すると、HTTP 拡張プロセッサ ノードから IoT Hub メッセージ シンク ノードを介して IoT ハブに結果が渡されます。 **[出力]** ウィンドウに表示されるメッセージには、`body` セクションが含まれています。 詳細については、「[IoT Hub メッセージを作成し、読み取る](../../../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

次のメッセージでは、Video Analyzer モジュールによって、アプリケーションのプロパティと本文の内容が定義されます。 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished イベント

ライブ パイプラインがアクティブ化されると、RTSP ソース ノードは、rtspsim-live555 コンテナーで実行されている RTSP サーバーへの接続を試みます。 接続に成功すると、 **[出力]** ウィンドウに表示されるイベントは次のようになります。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
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

* このメッセージは診断イベント **MediaSessionEstablished** です。 これは、RTSP ソース ノード (subject) が RTSP シミュレーターと接続され、(シミュレートされた) ライブ フィードの受信を開始したことを示します。
* `sdp` セクションには、診断イベントに関するデータが含まれています。 このケースでは、データは[セッション記述プロトコル (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) の詳細で構成されています。

### <a name="inference-event"></a>推論イベント

HTTP 拡張プロセッサ ノードは、AI 拡張機能モジュールの OpenVINO™ モデル サーバーから推論の結果を受け取ります。 その後、IoT Hub メッセージ シンク ノードを介して、推論イベントとして結果を出力します。 

これらのイベントの種類は `entity` に設定されており、それが車やトラックなどのエンティティであることを示しています。 次に示す、このようなイベントの `body` の例では、車両が検出され、信頼度の値が 0.9 を超えています。

```json
{
  "timestamp": 145819820073974,
  "inferences": [
    {
      "type": "entity",
      "subtype": "vehicleDetection",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9147264
        },
        "box": {
          "l": 0.6853116,
          "t": 0.5035262,
          "w": 0.04322505,
          "h": 0.03426218
        }
      }
    }
```

メッセージでは、次の詳細に注目します。

* `body` セクションには、分析イベントに関するデータが含まれています。 このケースでは、イベントは推論イベントであるため、本文には `inferences` データが含まれています。
* `inferences` セクションは、`type` が `entity` であることを示しています。 このセクションには、エンティティに関する追加のデータが含まれています。

## <a name="run-the-sample-program-to-detect-persons-vehicles-or-bikes"></a>サンプル プログラムを実行して人物、車両、またはバイクを検出する
別のモデルを使用する場合は、パイプライン トポロジと `operations.json` ファイルに変更を加える必要があります。

パイプライン トポロジ (`pipelineTopologyUrl` で使用される URL) をローカル ファイル `C:\TEMP\topology.json` にコピーします。 そのコピーを開き、`inferencingUrl` の値を `http://openvino:4000/personVehicleBikeDetection` に変更します。

次に、Visual Studio Code で *src/cloud-to-device-console-app* フォルダーに移動し、`operations.json` ファイルを開きます。 `pipelineTopologyUrl` を含む行を次のように編集します。

```
      "pipelineTopologyFile" : "C:\\TEMP\\topology.json" 
```
これで上記の手順を繰り返すと、新しいトポロジを使用して、再びサンプル プログラムを実行できるようになりました。 推論の結果は、車両検出モデルのものと (スキーマ) が似ています。`subtype` が `personVehicleBikeDetection` に設定されるだけです。

## <a name="run-the-sample-program-to-classify-vehicles"></a>サンプル プログラムを実行して車両を分類する
Visual Studio Code で、前の手順で使用した `topology.json` のローカル コピーを開き、`inferencingUrl` の値を `http://openvino:4000/vehicleClassification` に変更します。 前の例を実行して人物、車両、またはバイクを検出した場合、再度 `operations.json` ファイルを変更する必要はありません。

これで上記の手順を繰り返すと、新しいトポロジを使用して、再びサンプル プログラムを実行できるようになりました。 分類の結果のサンプルは次のとおりです。

```json
{
  "timestamp": 145819896480527,
  "inferences": [
    {
      "type": "classification",
      "subtype": "color",
      "classification": {
        "tag": {
          "value": "gray",
          "confidence": 0.683415
        }
      }
    },
    {
      "type": "classification",
      "subtype": "type",
      "classification": {
        "tag": {
          "value": "truck",
          "confidence": 0.9978394
        }
      }
    }
  ]
}

```

## <a name="run-the-sample-program-to-detect-faces"></a>サンプル プログラムを実行して顔を検出する
Visual Studio Code で、前の手順で使用した `topology.json` のローカル コピーを開き、`inferencingUrl` の値を `http://openvino:4000/faceDetection` に変更します。 前の例を実行して人物、車両、またはバイクを検出した場合、再度 `operations.json` ファイルを変更する必要はありません。

これで上記の手順を繰り返すと、新しいトポロジを使用して、再びサンプル プログラムを実行できるようになりました。 サンプルの検出結果は次のとおりです (注: 上で使用した駐車場のビデオには、検出可能な顔が一切含まれていません。このモデルを試すには別のビデオが必要です)。

```json
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  }
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のクイックスタートまたはチュートリアルに取り組む場合は、作成したリソースをそのまま残しておきます。 それ以外の場合は、Azure portal にアクセスして、ご利用のリソース グループに移動し、このチュートリアルを実行したリソース グループを選択して、そのリソースをすべて削除してください。

## <a name="next-steps"></a>次のステップ

上級ユーザー向けのその他の課題を確認します。

* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで検索できます。 プロファイル G、S、または T に準拠しているデバイスを探します。
* Azure Linux VM ではなく、ローカルの x64 Linux デバイスを使用します。 このデバイスは、IP カメラと同じネットワーク内にある必要があります。 [Linux への Azure IoT Edge ランタイムのインストール](../../../iot-edge/how-to-install-iot-edge.md)に関するページの手順を参照できます。 次に、「[初めての IoT Edge モジュールを Linux 仮想デバイスにデプロイする](../../../iot-edge/quickstart-linux.md)」の手順に従って、デバイスを Azure IoT Hub に登録します。
