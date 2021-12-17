---
title: 空間分析用の Computer Vision でライブ ビデオを分析する - Azure
description: このチュートリアルでは、Azure Video Analyzer を Azure Cognitive Services の Computer Vision 空間分析 AI 機能と共に使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 341c3df126d0ff949c3ba40e5d679bb2e3147ff6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488641"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>チュートリアル: 空間分析用の Computer Vision でライブ ビデオを分析する (プレビュー)

[!INCLUDE [header](includes/edge-env.md)]

このチュートリアルでは、Azure Video Analyzer を [Azure Cognitive Services の Computer Vision 空間分析 AI サービス](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)と共に使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 この推論サーバーでストリーミング ビデオを分析することで、物理領域内の人と動きの間の空間的な関係を理解できるようになります。 ビデオ フィード内のフレームのサブセットがこの推論サーバーに送信され、結果が IoT Edge ハブに送信されます。いくつかの条件が満たされると、ビデオ クリップは、Video Analyzer アカウントにビデオとして記録および保存されます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> - リソースを設定する
> - コードを確認する
> - サンプル コードを実行します。
> - イベントの監視

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事

始める前に、以下の記事をお読みください。

- [Video Analyzer の概要](../overview.md)
- [Video Analyzer の用語](../terminology.md)
- [パイプラインの概念](../pipeline.md)
- [イベントベースのビデオ記録](record-event-based-live-video.md)
- 空間分析のための [Azure Cognitive Service Computer Vision コンテナー](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)。

## <a name="prerequisites"></a>前提条件

spatial-analysis モジュールを Azure Video Analyzer モジュールに接続するための前提条件を次に示します。

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

   > [!Note]
   > 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

1. **コンピューティング デバイスを選択する**  

    空間分析コンテナーを実行するには、[NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) を搭載したコンピューティング デバイスが必要です。 GPU アクセラレーションを備えた **[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)** を使用することをお勧めしますが、コンテナーは、ホスト コンピューターに [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) がインストールされている他のすべての **デスクトップ マシン** または **Azure VM** で実行できます。

   #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge デバイス](#tab/azure-stack-edge)

   Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 準備とセットアップの詳細な手順については、[Azure Stack Edge のドキュメント](../../../databox-online/azure-stack-edge-deploy-prep.md)を参照してください。

   #### <a name="desktop-machine"></a>[デスクトップ コンピューター](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>ハードウェアに最低限必要な条件

   - 4 GB のシステム RAM
   - 4 GB の GPU RAM
   - 8 コア CPU
   - NVIDIA Tesla T4 GPU x 1
   - 20 GB の HDD 領域

   #### <a name="recommended-hardware"></a>推奨ハードウェア

   - 32 GB のシステム RAM
   - 16 GB の GPU RAM
   - 8 コア CPU
   - NVIDIA Tesla T4 GPU x 2
   - 50 GB の SSD 領域

   #### <a name="azure-vm-with-gpu"></a>[GPU 搭載 Azure VM](#tab/virtual-machine)

   1 つの K80 GPU が搭載された [NC シリーズ VM](../../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用できます。
        
1. **エッジ デバイスを設定します**

    #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge デバイス](#tab/azure-stack-edge)
    [Azure Stack Edge ポータルでコンピューティングを構成する](../../../cognitive-services/computer-vision/spatial-analysis-container.md#configure-compute-on-the-azure-stack-edge-portal)
    #### <a name="desktop-machine"></a>[デスクトップ コンピューター](#tab/desktop-machine)
    [使用するホスト コンピューターが Azure Stack Edge デバイスでない場合は、次の手順に従います。](../../../cognitive-services/computer-vision/spatial-analysis-container.md#install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer)
    #### <a name="azure-vm-with-gpu"></a>[GPU 搭載 Azure VM](#tab/virtual-machine)
    1. [VM を作成](../../../cognitive-services/computer-vision/spatial-analysis-container.md?tabs=virtual-machine#create-the-vm)し、その VM に必要な docker をインストールします

        > [!Important]
        > このドキュメントで説明されている **IoT 配置マニフェストの手順はスキップ** してください。 必要なコンテナーをデプロイするには、独自の **[配置マニフェスト](#configure-deployment-template)** ファイルを使用します。

    1. VM に接続し、ターミナルで次のコマンドを入力します。
    ```bash
    bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"
    ```
    Azure Video Analyzer モジュールは、エッジ デバイス上で、特権のないローカル ユーザー アカウントを使用して実行されます。 また、アプリケーションの構成データを格納するための特定のローカル フォルダーも必要となります。 最後に、この使用法ガイドでは、分析のためにビデオ フィードをリアルタイムで AVA モジュールに中継する [RTSP シミュレーター](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)を使用しています。 このシミュレーターは入力として、事前に録画されたビデオ ファイルを input ディレクトリから受け取ります。 
    
    上で使用した prep-device スクリプトにより、これらのタスクが自動化されます。そのため、1 つのコマンドを実行すれば、関連するすべての入力と構成フォルダー、ビデオ入力ファイル、特権付きのユーザー アカウントがシームレスに作成されます。 コマンドが正常に完了すると、次のフォルダーがエッジ デバイス上に作成されます。 
    
      * `/home/localedgeuser/samples`
      * `/home/localedgeuser/samples/input`
      * `/var/lib/videoanalyzer`
      * `/var/media`
    
     /home/localedgeuser/samples/input フォルダーにあるビデオ ファイル (*.mkv) に注目してください。これらが分析対象の入力ファイルとなります。  

1. 次に、他の Azure リソースをデプロイします。

   [![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > 上のボタンをクリックすると、NVIDIA GPU が搭載されていない既定の仮想マシンが作成されて使用されます。 Azure Resource Manager (ARM) テンプレートで要求されたら、Use existing edge device\(既存のエッジ デバイスを使用する\) オプションを使用して、上記の手順の IoT Hub とデバイス情報を使用してください。
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="既存のデバイスを使用する":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="空間分析の概要":::

この図は、このチュートリアルでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](../pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、`CognitiveServicesVisionProcessor` ノードにビデオ フレームを送信します。

`CognitiveServicesVisionProcessor` ノードはプロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、画像を **共有メモリ** 経由で、gRPC エンドポイントの背後で AI 操作を実行する別のエッジ モジュールに転送します。 この例では、このエッジ モジュールは spatial-analysis モジュールです。 `CognitiveServicesVisionProcessor` ノードでは次の 2 つのことが行われます。

- 結果を収集し、イベントを [IoT Hub シンク](../pipeline.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub) に送信します。
- また、[シグナル ゲート プロセッサ](../pipeline.md#signal-gate-processor)を使用して RTSP ソースから 30 秒のビデオ クリップをキャプチャし、それをビデオ シンクとして保存します。

## <a name="create-the-computer-vision-resource"></a>Computer Vision リソースを作成する

[Azure portal](../../../iot-edge/how-to-deploy-modules-portal.md) または Azure CLI を使用して、**Standard S1 レベル** で [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) という種類の Azure リソースを作成する必要があります。 

## <a name="set-up-your-development-environment"></a>開発環境を設定する

[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="configure-deployment-template"></a>配置テンプレートを構成する
#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge デバイス](#tab/azure-stack-edge)
**/src/edge/deployment.spatialAnalysis.ase.template.json** で配置ファイルを探します。 このテンプレートには、`avaedge`、`rtspsim`、`spatialanalysis` モジュールがあります。 

配置テンプレート ファイルで、以下を行います。

1. `avaedge` と `spatialanalysis` モジュールの間の通信のために、配置マニフェストでポート 50051 を使用します。 このポートが他のアプリケーションによって使用されている場合、`spatialanalysis` モジュールでポート バインドを設定してポートを開きます。

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `avaedge` の `IpcMode` と `spatialanalysis` モジュールの createOptions は同じで、**host** に設定されている必要があります。
1. RTSP シミュレーターを機能させるには、Azure Stack Edge デバイスを使用するときに、ボリュームの境界が設定されていることを確認します。

   1. [SMB 共有](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)に接続し、[サンプルの小売店のビデオ ファイル](https://avamedia.blob.core.windows.net/public/retailshop-15fps.mkv)をローカル共有にコピーします。

      > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMIPP]

   1. rtspsim モジュールに次の構成が含まれていることを確認します。
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
#### <a name="desktop-machine"></a>[デスクトップ コンピューター](#tab/desktop-machine)
**/src/edge/deployment.spatialAnalysis.generic.template.json** で配置ファイルを探します。 このテンプレートには、`avaedge`、`rtspsim`、`spatialanalysis` モジュールがあります。 

#### <a name="azure-vm-with-gpu"></a>[GPU 搭載 Azure VM](#tab/virtual-machine)
**/src/edge/deployment.spatialAnalysis.generic.template.json** で配置ファイルを探します。 このテンプレートには、`avaedge`、`rtspsim`、`spatialanalysis` モジュールがあります。

---

次の表は、IoT Edge モジュールで使用されるさまざまな環境変数を示しています。 また、前述の配置マニフェストで `spatialanalysis` の `env` 属性を使用して、これらを設定することもできます。

| 設定名 | 値 | 説明|
|---------|---------|---------|
| DISPLAY | :1 | この値は、ホスト コンピューター上の `echo $DISPLAY` の出力と同じである必要があります。 Azure Stack Edge デバイスにはディスプレイがありません。 この設定は適用されません|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | **変更しないでください**|
| ARCHON_LOG_LEVEL | 情報。詳細 | ログ レベルで 2 つの値のいずれかを選択します|
| QT_X11_NO_MITSHM | 1 | **変更しないでください**|
| OMP_WAIT_POLICY | PASSIVE | **変更しないでください**|
| EULA | accept | コンテナーを実行するには、この値を *accept* に設定する必要があります |
| ARCHON_TELEMETRY_IOTHUB | true | テレメトリ イベントを IoT Hub に送信するには、この値を true に設定します |
| 課金 | エンドポイント URI| この値は、Azure portal で Computer Vision リソースから収集します。 お使いのリソースの **[キーとエンドポイント]** ブレードで見つけることができます。|
| APIKEY | API キー| この値は、Azure portal で Computer Vision リソースから収集します。 お使いのリソースの **[キーとエンドポイント]** ブレードで見つけることができます。 |
| LAUNCHER_TYPE | avaBackend | **変更しないでください** |
| ARCHON_GRAPH_READY_TIMEOUT | 600 | GPU が T4 または NVIDIA 2080 Ti **ではない** 場合は、この環境変数を追加します|

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。 

### <a name="gathering-keys-and-endpoint-uri"></a>キーとエンドポイント URI を収集する

API キーは spatial-analysis コンテナーを起動するために使用され、Azure portal の Computer Vision リソースの `Keys and Endpoint` ページで入手できます。 このページに移動し、`spatialAnalysis` コンテナーで必要となるキーとエンドポイント URI を見つけます。  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="エンドポイント URL":::

## <a name="generate-and-deploy-the-deployment-manifest"></a>配置マニフェストを生成してデプロイする

エッジ デバイスにデプロイされるモジュールは、配置マニフェストによって定義されます。 また、これらのモジュールの構成設定も定義されます。

マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、これらの手順に従います。

1. Visual Studio Code を開きます。
1. [`AZURE IOT HUB`] ペインの横にある [その他のアクション] アイコンを選択して、IoT Hub 接続文字列を設定します。 この文字列は、`src/cloud-to-device-console-app/appsettings.json` ファイルからコピーできます。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="空間分析: 接続文字列":::

1. フォルダー エクスプローラーで、配置テンプレート ファイルを右クリックし、[IoT Edge 配置マニフェストの生成] を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="空間分析: 展開 amd64 json":::

   このアクションによって、**src/edge/config** フォルダー内にマニフェスト ファイルが作成されます。

1. 生成されたマニフェスト ファイルを右クリックし、 **[単一デバイスのデプロイを作成する]** を選択し、エッジ デバイスの名前を選びます。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="空間分析: 1 つのデバイスにデプロイする":::

1. ページの上部で、IoT Hub デバイスを選択するよう求められます。ドロップダウン メニューから該当するエッジ デバイス名を選択します。
1. 約 30 秒後に、ウィンドウの左下隅の **[AZURE IOT HUB]** ペインを更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。

   - Azure Video Analyzer (モジュール名 **avaedge**)。
   - リアルタイム ストリーミング プロトコル (RTSP) シミュレーター (モジュール名 **rtspsim**)。
   - 空間分析 (モジュール名 **spatialanalysis**)。

正常にデプロイされると、出力ウィンドウに次のようなメッセージが表示されます。

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

その後、[Devices/Modules]\(デバイス/モジュール\) の下に `avaedge`、`spatialanalysis`、`rtspsim` のモジュールが表示され、それらの状態は "**実行中**" になっているはずです。

## <a name="prepare-to-monitor-events"></a>イベントの監視の準備をする

それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. 右クリックして、 **[拡張機能の設定]** を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="拡張機能の設定":::

1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="詳細メッセージの表示":::

1. [エクスプローラー] ペインを開き、左下隅にある **[AZURE IOT HUB]** を見つけ、右クリックして、[組み込みイベント エンドポイントの監視を開始する] を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="空間分析: 監視の開始":::

## <a name="run-the-program"></a>プログラムの実行

`src/cloud-to-device-console-app/operations.json` のダイレクト メソッドを呼び出す program.cs があります。 `operations.json` ファイルを編集し、パイプライン トポロジの URL、トポロジの名前、RTSP の URL を更新する必要があります。

operations.json で、次のようにします。

- 次のようにパイプライン トポロジを設定します。

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json"
      }
  },
  ```
* `livePipelineSet` で、前のリンクの値と一致するようにトポロジの名前を編集します。
    * `"topologyName" : "PersonZoneCrossingTopology"`
* `pipelineTopologyDelete` で、名前を編集します。
    * `"name" : "PersonZoneCrossingTopology"`

> [!Important]
> 上で使用したトポロジには、VideoSink リソース `videoSink` のハードコーディングされた名前が付けられています。 別のビデオ ソースを選ぶ場合は、必ずこの値を変更してください。

- ここでは、次のようなライブ パイプラインを作成し、パイプライン トポロジでパラメーターを設定します。

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "PersonZoneCrossingTopology",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": "rtsp://rtspsim:554/media/retailshop-15fps.mkv"
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
  },
  ```

F5 を選択してデバッグ セッションを実行し、**ターミナル** の指示に従います。これにより、パイプライン トポロジの設定、ライブ パイプラインの設定、ライブ パイプラインのアクティブ化が行われ、最後にリソースが削除されます。

> [!Note]
> プログラムは、ライブ パイプラインのアクティブ化ステップで一時停止します。 ターミナル タブを開き、**Enter** キーを押して続行し、リソースの非アクティブ化と削除のステップを開始します。

## <a name="interpret-results"></a>結果を解釈する

`spatialanalysis` は大きなコンテナーであり、起動時間は最大で 30 秒かかります。 spatialanalysis コンテナーが起動して実行されると、推論イベントの送信が開始されます。 次のようなイベントが表示されます。

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/retailshop-15fps.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}

```
> [!NOTE]
> **"初期化中"** のメッセージが表示されます。 これらのメッセージは spatialAnalysis モジュールの起動中に表示され、実行中の状態になるまでに最大 60 秒かかることがあります。 しばらくお待ちください。推論イベント フローが表示されます。

パイプライン トポロジのインスタンスが作成されると、"MediaSessionEstablished" イベントが表示されます。サンプルの MediaSessionEstablished イベントは[こちら](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)にあります。

また、spatialanalysis モジュールによって、AI 分析情報イベントが Azure Video Analyzer に、そして IoTHub に送信され、**出力** ウィンドウにも表示されます。 これらの AI 分析情報は、ビデオ シンク ノードでビデオと共に記録されます。 以下で説明するように、Video Analyzer を使用してこれらを表示できます。

## <a name="supported-spatial-analysis-operations"></a>サポートされる空間分析操作

`spatialAnalysis` モジュールが提供し、Azure Video Analyzer でサポートされる操作を次に示します。

- **personZoneCrossing**
- **personCrossingLine**
- **personDistance**
- **personCount**
- **customOperation**


さまざまな操作とそれらの操作でサポートされるパイプラインについて詳しくは、**サポートされる** **[空間分析操作](../spatial-analysis-operations.md)** に関するリファレンス ドキュメントを参照してください。

## <a name="playing-back-the-recording"></a>録画を再生する

ライブ パイプラインによって作成された Video Analyzer ビデオ リソースを調べるには、Azure portal にログインしてビデオを視聴します。

1. Web ブラウザーを開き、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。
1. サブスクリプション内のリソースの中から Video Analyzers アカウントを見つけ、アカウント ペインを開きます。
1. **[Video Analyzer]** のリストで **[ビデオ]** を選択します。
1. `personzonecrossing` という名前で一覧に含まれているビデオが見つかります。 これは、パイプライン トポロジ ファイルで選択された名前です。
1. ビデオを選択します。
1. ビデオの詳細ページで、 **[再生]** アイコンをクリックします。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="ビデオ再生のスクリーンショット":::
   
1. ビデオに推論メタデータを表示するには、 **[Metadata rendering]\(メタデータのレンダリング\)** アイコンをクリックします
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="[Metadata rendering]\(メタデータのレンダリング\) アイコン":::

    ビデオにオーバーレイとして表示するには、次の 3 つのオプションがあります。  
      - **境界ボックス**: 一意の ID を持つ各個人の周囲に境界ボックスを表示します
      - **属性**: 使用可能な場合に、速度 (ft/s)、方向 (矢印を使用) などの個人属性を表示します
      - **オブジェクト パス**: 使用可能な場合に、個人の移動ごとに短い軌跡を表示します

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback-bounding-boxes.png" alt-text="境界ボックスを使用したビデオ再生のスクリーンショット":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>次の手順

`spatialAnalysis` モジュールが提供するさまざまな操作を試してください。次のパイプライン トポロジを参照してください。

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)
