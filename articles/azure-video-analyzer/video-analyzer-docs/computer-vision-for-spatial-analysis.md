---
title: 空間分析用の Computer Vision でライブ ビデオを分析する - Azure
description: このチュートリアルでは、Azure Video Analyzer を Azure Cognitive Services の Computer Vision 空間分析 AI 機能と共に使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: d54983e25abc769a75923e59c483a4cf9495770f
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384037"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>チュートリアル: 空間分析用の Computer Vision でライブ ビデオを分析する (プレビュー)

このチュートリアルでは、Azure Video Analyzer を [Azure Cognitive Services の Computer Vision 空間分析 AI サービス](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)と共に使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 この推論サーバーでストリーミング ビデオを分析することで、物理領域内の人と動きの間の空間的な関係を理解できるようになります。 ビデオ フィード内のフレームのサブセットがこの推論サーバーに送信され、結果が IoT Edge ハブに送信されます。いくつかの条件が満たされると、ビデオ クリップは、Video Analyzer アカウントにビデオとして記録および保存されます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> - リソースを設定する。
> - コードを確認します。
> - サンプル コードを実行する。
> - イベントを監視する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>推奨される事前読み取り

始める前に、以下の記事をお読みください。

- [Video Analyzer の概要](overview.md)
- [Video Analyzer の用語](terminology.md)
- [パイプラインの概念](pipeline.md)
- [イベントベースのビデオ記録](record-event-based-live-video.md)
- [チュートリアル:IoT Edge モジュールを開発する](../../iot-edge/tutorial-develop-for-linux.md)

## <a name="prerequisites"></a>前提条件

spatial-analysis モジュールを Azure Video Analyzer モジュールに接続するための前提条件を次に示します。

- 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
  - 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。
- 空間分析のための [Azure Cognitive Service Computer Vision コンテナー](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)。
  このコンテナーを使用するには、関連付けられている **API キー** および **エンドポイント URI** を取得するための Computer Vision リソースが必要です。 この API キーは、Azure portal の Computer Vision の [概要] および [キー] ページで入手できます。 キーとエンドポイントは、コンテナーを開始するために必要です。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

1. 空間分析コンテナーを実行するには、[NVIDIA Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/) を搭載したコンピューティング デバイスが必要です。 GPU アクセラレーションを備えた [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) を使用することをお勧めしますが、コンテナーは、ホスト コンピューターに [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) がインストールされている他のすべてのデスクトップ マシンで実行できます。

   #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge デバイス](#tab/azure-stack-edge)

   Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 準備とセットアップの詳細な手順については、[Azure Stack Edge のドキュメント](../../databox-online/azure-stack-edge-deploy-prep.md)を参照してください。

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

   この記事では、次のソフトウェア パッケージをダウンロードしてインストールします。 ホスト コンピューターによって以下を実行できる必要があります (手順については以下を参照してください)。

   - [NVIDIA グラフィックス ドライバー](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)と [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
   - [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (マルチプロセス サービス) の構成。
   - [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) と [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker)
   - [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) ランタイム。

   #### <a name="azure-vm-with-gpu"></a>[GPU 搭載 Azure VM](#tab/virtual-machine)

   1 つの K80 GPU が搭載された [NC シリーズ VM](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用できます。

   1. VM に接続し、ターミナルで次のコマンドを入力します。

        `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
        Azure Video Analyzer モジュールは、エッジ デバイス上で、特権のないローカル ユーザー アカウントを使用して実行されます。 また、アプリケーションの構成データを格納するための特定のローカル フォルダーも必要となります。 最後に、この使用法ガイドでは、分析のためにビデオ フィードをリアルタイムで AVA モジュールに中継する [RTSP シミュレーター](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)を活用しています。 このシミュレーターは入力として、事前に録画されたビデオ ファイルを input ディレクトリから受け取ります。 
    
        上で使用した prep-device スクリプトにより、これらのタスクが自動化されます。そのため、1 つのコマンドを実行すれば、関連するすべての入力と構成フォルダー、ビデオ入力ファイル、特権付きのユーザー アカウントがシームレスに作成されます。 コマンドが正常に完了すると、次のフォルダーがエッジ デバイス上に作成されます。 
    
        * `/home/localedgeuser/samples`
        * `/home/localedgeuser/samples/input`
        * `/var/lib/videoanalyzer`
        * `/var/media`
    
        /home/localedgeuser/samples/input フォルダーにあるビデオ ファイル (*.mkv) に注目してください。これらが分析対象の入力ファイルとなります。 

1. [エッジ デバイスを設定します](../../cognitive-services/computer-vision/spatial-analysis-container.md#set-up-the-host-computer)

1. 次に、他の Azure リソースをデプロイします。

   [![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > 上のボタンをクリックすると、NVIDIA GPU が搭載されていない既定の仮想マシンが作成されて使用されます。 Azure Resource Manager (ARM) テンプレートで要求されたら、Use existing edge device\(既存のエッジ デバイスを使用する\) オプションを使用して、上記の手順の IoT Hub とデバイス情報を使用してください。
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="既存のデバイスを使用する":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="空間分析の概要":::

この図は、このチュートリアルでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](pipeline.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、`CognitiveServicesVisionProcessor` ノードにビデオ フレームを送信します。

`CognitiveServicesVisionProcessor` ノードはプロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、画像を **共有メモリ** 経由で、gRPC エンドポイントの背後で AI 操作を実行する別のエッジ モジュールに転送します。 この例では、このエッジ モジュールは spatial-analysis モジュールです。 `CognitiveServicesVisionProcessor` ノードでは次の 2 つのことが行われます。

- 結果を収集し、イベントを [IoT Hub シンク](pipeline.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub) に送信します。
- また、[シグナル ゲート プロセッサ](pipeline.md#signal-gate-processor)を使用して RTSP ソースから 30 秒のビデオ クリップをキャプチャし、それをビデオ ファイルとして格納します。

## <a name="create-the-computer-vision-resource"></a>Computer Vision リソースを作成する

[Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) または Azure CLI を使用して、Computer Vision という種類の Azure リソースを作成する必要があります。 コンテナーへのアクセス要求が承認され、Azure サブスクリプション ID が登録されると、リソースを作成できるようになります。  https://aka.ms/csgate にアクセスして、ユース ケースと Azure のサブスクリプション ID を送信します。 アクセス要求フォームに記載されているものと同じ Azure サブスクリプションを使用して、Azure リソースを作成する必要があります。

### <a name="gathering-required-parameters"></a>必須パラメーターの収集

spatialanalysis コンテナーなど、すべての Cognitive Services のコンテナーに対して必須である、3 つの主要なパラメーターがあります。 エンドユーザー使用許諾契約書 (EULA) が、accept の値と共に存在する必要があります。 さらに、エンドポイント URI と API キーの両方が必要です。

### <a name="keys-and-endpoint-uri"></a>キーとエンドポイント URI

キーは spatial-analysis コンテナーを起動するために使用され、Azure portal の対応する Cognitive Service リソースの `Keys and Endpoint` ページで入手できます。 そのページに移動し、キーとエンドポイント URI を見つけます。  

spatialanalysis コンテナーをデプロイするには、配置マニフェスト ファイルにこのキーとエンドポイント URI が必要です。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="エンドポイント URL":::

## <a name="set-up-your-development-environment"></a>開発環境を設定する

1. 次の場所からリポジトリをクローンします: [https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp)。
1. Visual Studio Code で、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、src/cloud-to-device-console-app フォルダーに移動します。 そこにファイルを作成し、*appsettings.json* という名前を付けます。 このファイルには、プログラムを実行するために必要な設定が格納されます。
1. 次の手順に従ってエッジ デバイスから `IotHubConnectionString` を取得します。

   - Azure portal の IoT Hub に移動し、左側のナビゲーション ペインで `Shared access policies` をクリックします。
   - `iothubowner` をクリックして共有アクセス キーを取得します。
   - `Connection String – primary key` をコピーし、VSCode の入力ボックスに貼り付けます。

     接続文字列は次のようになります。 <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`

1. 次の内容をファイルにコピーします。 変数を必ず置き換えてください。

   ```json
   {
     "IoThubConnectionString": "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
     "deviceId": "<your Edge Device name>",
     "moduleId": "avaedge"
   }
   ```

1. src/edge フォルダーに移動し、 .env という名前のファイルを作成します。
1. Azure portal から env ファイルの内容をコピーします。 テキストは次のコードのようになります。

   ```env
   SUBSCRIPTION_ID="<Subscription ID>"
   RESOURCE_GROUP="<Resource Group>"
   AVA_PROVISIONING_TOKEN="<The provisioning token>"
   VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
   VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
   APPDATA_FOLDER_ON_DEVICE="/var/lib/videoanalyzer"
   CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
   CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

## <a name="set-up-deployment-template"></a>展開テンプレートの設定

/src/edge/deployment.spatialAnalysis.template.json で配置ファイルを探します。 このテンプレートには、avaedge モジュール、rtspsim モジュール、spatialanalysis モジュールがあります。

展開テンプレート ファイルには、注意が必要な点がいくつかあります。

1. `spatialanalysis` モジュールでポートのバインドを設定します。

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

   1. [SMB 共有に接続](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)し、[サンプル stairwell ビデオ ファイル](https://lvamedia.blob.core.windows.net/public/2018-03-05.10-27-03.10-30-01.admin.G329.mp4)をローカル共有にコピーします。

      > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWDRJd]

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
## <a name="generate-and-deploy-the-deployment-manifest"></a>配置マニフェストを生成してデプロイする

エッジ デバイスにデプロイされるモジュールは、配置マニフェストによって定義されます。 また、これらのモジュールの構成設定も定義されます。

マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、これらの手順に従います。

1. Visual Studio Code を開きます。
1. [`AZURE IOT HUB`] ペインの横にある [その他のアクション] アイコンを選択して、IoT Hub 接続文字列を設定します。 この文字列は、`src/cloud-to-device-console-app/appsettings.json` ファイルからコピーできます。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="空間分析: 接続文字列":::

1. `src/edge/deployment.spatialAnalysis.template.json` を右クリックし、[Generate IoT Edge Deployment Manifest]\(IoT Edge 配置マニフェストの生成\) を選択します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="空間分析: 展開 amd64 json":::

   このアクションによって、`deployment.spatialAnalysis.amd64.json` という名前のマニフェスト ファイルが src/edge/config フォルダーに作成されます。

1. `src/edge/config/deployment.spatialAnalysis.amd64.json` を右クリックし、 **[単一デバイスのデプロイを作成する]** を選択して、エッジ デバイスの名前を選択します。

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

`src/cloud-to-device-console-app/operations.json` のダイレクト メソッドを呼び出す program.cs があります。 operations.json を設定し、パイプラインで使用するための pipelineTopology を提供する必要があります。

operations.json で、次のようにします。

- 次のように pipelineTopology を設定します。

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "topologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json"
      }
  },
  ```

- 次のような livePipeline を作成し、ここで pipelineTopology のパラメーターを設定します。

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "InferencingWithCVExtension",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": " rtsp://rtspsim:554/media/stairwell.mkv"
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

  > [!Note]
  > `CognitiveServicesVisionExtension` を使用した spatialanalysis モジュールとの接続を確認します。 ${grpcUrl} を **tcp://spatialAnalysis:<PORT_NUMBER>** に設定します (例: tcp://spatialAnalysis:50051)

  ```json
  {
          "@type": "#Microsoft.VideoAnalyzer.CognitiveServicesVisionProcessor",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "operation": {
            "@type": "#Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation",
            "zones": [
              {
                "zone": {
                  "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
                  "polygon": "[[0.37,0.43],[0.48,0.42],[0.53,0.56],[0.34,0.57],[0.34,0.46]]",
                  "name": "stairlanding"
                },
                "events": [
                  {
                    "trigger": "event",
                    "outputFrequency": "1",
                    "threshold": "16",
                    "focus": "bottomCenter"
                  }
                ]
              }
            ]
          }
        }
      ],
  ```

デバッグ セッションを実行し、**ターミナル** の指示に従います。これにより、pipelineTopology の設定、livePipeline の設定、livePipeline のアクティブ化が行われ、最後にリソースの削除が行われます。

## <a name="interpret-results"></a>結果を解釈する

pipelineTopology のインスタンスが作成されると、"MediaSessionEstablished" イベントが表示されます。サンプルの MediaSessionEstablished イベントは[こちら](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)にあります。

また、spatialanalysis モジュールによって、AI 分析情報イベントが Azure Video Analyzer に、そして IoTHub に送信され、**出力** ウィンドウにも表示されます。 ENTITY は検出オブジェクトであり、EVENT はスペース分析イベントです。 この出力は、Azure Video Analyzer に渡されます。

(`SpatialAnalysisPersonZoneCrossingOperation` 操作からの) personZoneEvent のサンプル出力:

```
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": [
        "8724dff43d3c4716936aa6c9a808ee2e"
      ],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="operations"></a>操作:

### <a name="person-zone-crossing"></a>Person Zone Crossing

#### <a name="parameters"></a>パラメーター:

| 名前                      | 型    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ゾーン                     | list    | ゾーンのリスト。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | このゾーンのフレンドリ名。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | string  | 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 その人がゾーン内のこのピクセル数よりも大きい場合に threshold float イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。 |
| eventType                 | string  | cognitiveservices.vision.spatialanalysis-personcrossingpolygon の場合、これは zonecrossing または zonedwelltime である必要があります。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| トリガー (trigger)                   | string  | イベントを送信するためのトリガーの種類。 サポートされている値: "event": だれかがゾーンに入ったときまたはゾーンから出たときに発生します。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| focus                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": ["8724dff43d3c4716936aa6c9a808ee2e"],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="more-operations"></a>その他の操作:

<details>
  <summary>展開するにはクリック</summary>

### <a name="person-line-crossing"></a>Person Line Crossing

#### <a name="parameters"></a>パラメーター:

| 名前                      | 型    | Description                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lines                     | list    | ラインのリスト。                                                                                                                                                                                                                                                                |
| name                      | string  | このラインのフレンドリ名。                                                                                                                                                                                                                                                  |
| line                      | string  | 各値ペアは、ラインの開始点と終了点を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。                            |
| outputFrequency           | INT     | イベントが送信される頻度。 output_frequency = X の場合、X おきにイベントが送信されます。たとえば、 output_frequency = 2 は、イベントが 1 つおきに出力されることを意味します。 output_frequency は、イベントと間隔の両方に適用されます。                                                       |
| focus                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。 |
| threshold                 | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。 既定値は 16 です。 これは、最大の精度を実現するために推奨される値です。                                                                                                |
| enableFaceMaskClassifier  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。          |
| detectorNodeConfiguration | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                      |

#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="person-distance"></a>Person Distance

#### <a name="parameters"></a>パラメーター:

| 名前                      | 型    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ゾーン                     | list    | ゾーンのリスト。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | このゾーンのフレンドリ名。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | string  | 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 その人がゾーン内のこのピクセル数よりも大きい場合に threshold float イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。 |
| outputFrequency           | INT     | イベントが送信される頻度。 output_frequency = X の場合、X おきにイベントが送信されます。たとえば、 output_frequency = 2 は、イベントが 1 つおきに出力されることを意味します。 output_frequency は、イベントと間隔の両方に適用されます。                                                                                                                                                                                                                                                                                                                                                     |
| focus                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。                                                                                                                                                                                                                                                                                               |
| threshold                 | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| outputFrequency           | INT     | イベントが送信される頻度。 output_frequency = X の場合、X おきにイベントが送信されます。たとえば、 output_frequency = 2 は、イベントが 1 つおきに出力されることを意味します。 output_frequency は、イベントと間隔の両方に適用されます。                                                                                                                                                                                                                                                                                                                                                     |
| minimumDistanceThreshold  | float   | 人と人との距離がその距離よりも近いときに "TooClose" イベントをトリガーする距離 (フィート単位)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| maximumDistanceThreshold  | float   | 人と人との距離がその距離よりも離れているときに "TooFar" イベントをトリガーする距離 (フィート単位)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| aggregationMethod         | string  | 個人距離の結果を集計するためのメソッド。 aggregationMethod は、モードと平均の両方に適用できます。                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

### <a name="person-count"></a>Person Count

#### <a name="parameters"></a>パラメーター:

| 名前                      | 型    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ゾーン                     | list    | ゾーンのリスト。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | このゾーンのフレンドリ名。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | string  | 各値のペアは、多角形の頂点の x、y を表します。 多角形は、人の追跡または人数のカウントを行う領域を表します。 浮動小数点値は、左上隅を基準とした頂点の位置を表します。 x、y の絶対値を計算するには、これらの値とフレーム サイズを乗算します。 その人がゾーン内のこのピクセル数よりも大きい場合に threshold float イベントが送信されます。 既定値は、type が zonecrossing の場合は 48、time が DwellTime の場合は 16 です。 これらは、最大の精度を実現するために推奨される値です。 |
| outputFrequency           | INT     | イベントが送信される頻度。 output_frequency = X の場合、X おきにイベントが送信されます。たとえば、 output_frequency = 2 は、イベントが 1 つおきに出力されることを意味します。 output_frequency は、イベントと間隔の両方に適用されます。                                                                                                                                                                                                                                                                                                                                                     |
| トリガー (trigger)                   | string  | イベントを送信するためのトリガーの種類。 サポートされている値は、event (カウントが変わったときにイベントを送信する場合)、または interval (カウントが変わったかどうかに関係なく、イベントを定期的に送信する場合) です。                                                                                                                                                                                                                                                                                                                                                           |
| focus                     | string  | イベントの計算に使用される人の境界ボックス内のポイントの場所。 focus の値は、footprint (人の占有領域)、bottom_center (人の境界ボックスの下部中央)、center (人の境界ボックスの中央) にすることができます。 既定値は footprint です。                                                                                                                                                                                                                                                                                               |
| threshold                 | float   | その人がゾーン内のこのピクセル数よりも大きい場合にイベントが送信されます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| enableFaceMaskClassifier  | boolean | ビデオ ストリームでフェイス マスクを着用している人の検出を有効にするには true、無効にするには false にします。 既定では、この構成は無効です。 フェイス マスクの検出には、入力ビデオ幅パラメーターを 1920 にする必要があります ("INPUT_VIDEO_WIDTH": 1920)。 フェイス マスク属性は返されません。                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | string  | すべての空間分析操作の DETECTOR_NODE_CONFIG パラメーター。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

### <a name="custom"></a>カスタム

#### <a name="parameters"></a>パラメーター:

| 名前                   | 型   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | string | 操作の JSON 表現。 |

#### <a name="output"></a>出力:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

</details>

## <a name="video-player"></a>ビデオ プレーヤー

下に示すように、ビデオ プレーヤーを使用して、推論 (境界ボックス) を含む生成されたビデオを表示できます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/inference.png" alt-text="境界ボックス":::

## <a name="troubleshooting"></a>トラブルシューティング

spatialanalysis は大きなコンテナーであり、起動時間は最大で 30 秒かかります。 spatialanalysis コンテナーが起動して実行されると、推論イベントの送信が開始されます。

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/cafeteria.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
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
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:38:42 PM] Message received from [ase03-edge/avaedge]:
{
  "timestamp": 145860472980260,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "647aacf9d8bc47078a1ed31d1c459c24",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.7583008
        },
        "box": {
          "l": 0.48213565,
          "t": 0.21217245,
          "w": 0.056364775,
          "h": 0.29961595
        }
      },
      "extensions": {
        "centerGroundPointY": "0.0",
        "centerGroundPointX": "0.0",
        "footprintX": "0.5087100982666015",
        "footprintY&quot;: &quot;0.49634415356080924"
      }
    },
    {
      "type": "event",
      "inferenceId": "dae6c2b742634196b615c128654845dc",
      "relatedInferences": [
        "647aacf9d8bc47078a1ed31d1c459c24"
      ],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone&quot;: &quot;stairlanding"
        }
      }
    }
  ]
}

```

> [!NOTE]
> **"初期化中"** のメッセージが表示される場合があります。 これらのメッセージは spatialAnalysis モジュールの起動中に表示され、実行中の状態になるまでに最大 60 秒かかることがあります。 しばらくお待ちください。推論イベント フローが表示されます。

## <a name="next-steps"></a>次の手順

`spatialAnalysis` モジュールが提供するさまざまな操作を試してください。次の pipelineTopology を参照してください。

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-pperation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [personZoneCrossing](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)

> [!Tip]
> フレームに複数の人が含まれている **[サンプル ビデオ ファイル](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)** を使用します。
