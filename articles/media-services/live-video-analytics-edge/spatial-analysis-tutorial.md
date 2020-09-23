---
title: 空間分析用の Computer Vision でライブ ビデオを分析する - Azure
description: このチュートリアルでは、Live Video Analytics を Azure Cognitive Services の Computer Vision 空間分析 AI 機能と共に使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929652"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>空間分析用の Computer Vision でライブ ビデオを分析する (プレビュー)

このチュートリアルでは、Live Video Analytics を [Azure Cognitive Services の Computer Vision 空間分析 AI サービス](https://azure.microsoft.com/services/cognitive-services/computer-vision/)と共に使用して、(シミュレートされた) IP カメラからのライブ ビデオ フィードを分析する方法について説明します。 この推論サーバーでストリーミング ビデオを分析することで、物理領域内の人と動きの間の空間的な関係を理解できるようになります。  ビデオ フィード内のフレームのサブセットがこの推論サーバーに送信され、結果が IoT Edge ハブに送信されます。いくつかの条件が満たされると、Azure Media Services アセットとしてビデオ クリップが記録、保存されます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * リソースを設定する。
> * コードを確認します。
> * サンプル コードを実行する。
> * イベントを監視する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>推奨される事前読み取り

始める前に、以下の記事をお読みください。

* [IoT Edge の Live Video Analytics (プレビュー)](overview.md)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)
* [チュートリアル:IoT Edge モジュールを開発する](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Azure Stack Edge に Live Video Analytics をデプロイする](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>前提条件

次に、spatial-analysis モジュールを Live Video Analytics モジュールに接続するための前提条件を示します。

* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
    * 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。
* GPU アクセラレーションを備えた [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)。  
    GPU アクセラレーションを備えた Azure Stack Edge を使用することをお勧めしますが、コンテナーは [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) を使用する他のデバイス上で実行されます。 
* 空間分析のための [Azure Cognitive Service Computer Vision コンテナー](https://azure.microsoft.com/services/cognitive-services/computer-vision/)。  
    このコンテナーを使用するには、関連付けられている **API キー**および **エンドポイント URI** を取得するための Computer Vision リソースが必要です。 この API キーは、Azure portal の Computer Vision の [概要] および [キー] ページで入手できます。 キーとエンドポイントは、コンテナーを開始するために必要です。

## <a name="overview"></a>概要

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="空間分析の概要":::
 
この図は、このチュートリアルでのシグナルの流れを示しています。 [エッジ モジュール](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)は、リアルタイム ストリーミング プロトコル (RTSP) サーバーをホストする IP カメラをシミュレートします。 [RTSP ソース](media-graph-concept.md#rtsp-source) ノードは、このサーバーからビデオ フィードをプルし、[フレーム レート フィルター プロセッサ](media-graph-concept.md#frame-rate-filter-processor) ノードにビデオ フレームを送信します。 このプロセッサは、 MediaGraphCognitiveServicesVisionExtension プロセッサ ノードに到達するビデオ ストリームのフレーム レートを制限します。

MediaGraphCognitiveServicesVisionExtension ノードは、プロキシの役割を果たします。 ビデオ フレームを、指定した画像の種類に変換します。 次に、画像を**共有メモリ**経由で、gRPC エンドポイントの背後で AI 操作を実行する別のエッジ モジュールに転送します。 この例では、このエッジ モジュールは spatial-analysis モジュールです。 MediaGraphCognitiveServicesVisionExtension プロセッサ ノードでは、次の 2 つの処理が行われます。

* 結果を収集し、イベントを [IoT Hub シンク](media-graph-concept.md#iot-hub-message-sink) ノードに発行します。 次に、このノードはこれらのイベントを [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub) に送信します。 
* また、[シグナル ゲート プロセッサ](media-graph-concept.md#signal-gate-processor)を使用して RTSP ソースから 30 秒のビデオ クリップをキャプチャし、それを Media Services アセットとして格納します。

## <a name="create-the-computer-vision-resource"></a>Computer Vision リソースを作成する

[Azure portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) または Azure CLI を使用して、Computer Vision という種類の Azure リソースを作成する必要があります。 コンテナーへのアクセス要求が承認され、Azure サブスクリプション ID が登録されると、リソースを作成できるようになります。 https://aka.ms/csgate にアクセスして、ユース ケースと Azure のサブスクリプション ID を送信します。  アクセス要求フォームに記載されているものと同じ Azure サブスクリプションを使用して、Azure リソースを作成する必要があります。

### <a name="gathering-required-parameters"></a>必須パラメーターの収集

spatial-analysis コンテナーなど、すべての Cognitive Services のコンテナーに対して必須である、3 つの主要なパラメーターがあります。 エンドユーザー使用許諾契約書 (EULA) が、accept の値と共に存在する必要があります。 さらに、エンドポイント URL と API キーの両方が必要です。

### <a name="endpoint-uri-endpoint_uri"></a>エンドポイント URI {ENDPOINT_URI}

エンドポイント URI の値は、Azure portal の Cognitive Service リソースの [概要] ページで入手できます。 [概要] ページに移動し、エンドポイント URI を見つけます。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="キーとエンドポイント":::

### <a name="keys-api_key"></a>キー {API_KEY}

このキーは spatial-analysis コンテナーを起動するために使用されるものであり、Azure portal の対応する Cognitive Service リソースの [キー] ページで入手できます。 [キー] ページに移動し、キーを見つけます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="エンドポイント URL":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack Edge を設定する

[次の手順](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)に従って Azure Stack Edge を設定し、引き続きその後の手順に従って、Live Video Analytics と spatial analysis モジュールをデプロイします。

## <a name="set-up-your-development-environment"></a>開発環境を設定する

1. 次の場所からリポジトリをクローンします: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 。
1. Visual Studio Code で、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、src/cloud-to-device-console-app フォルダーに移動します。 そこにファイルを作成し、appsettings.json という名前を付けます。 このファイルには、プログラムを実行するために必要な設定が格納されます。
1. 以下のステップに従って、IotHubConnectionString を Azure Stack Edge から取得します。

    * Azure portal の IoT Hub に移動し、左側のナビゲーション ペインで `Shared access policies` をクリックします。
    * `iothubowner` をクリックして共有アクセス キーを取得します。
    * `Connection String – primary key` をコピーし、VSCode の入力ボックスに貼り付けます。
    
        接続文字列は次のようになります。 <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. 次の内容をファイルにコピーします。 変数を必ず置き換えてください。
    
    ```json
    {
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. src/edge フォルダーに移動し、 .env という名前のファイルを作成します。
1. /clouddrive/lva-sample/edge-deployment/.env ファイルの内容をコピーします。 テキストは次のコードのようになります。

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>展開テンプレートの設定  

/src/edge/deployment.template.jso に spatial-analysis モジュールを追加します。 このテンプレートには、lvaEdge モジュール、rtspsim モジュール、spatial-analysis モジュールがあります。

<p>
<details>
<summary>これを展開して、サンプルの展開テンプレートを表示します。  
ここから内容をコピーし、/src/edge/deployment.template.json に貼り付けます。
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
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
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

注意が必要な点がいくつかあります。

1. ポートのバインドを設定します。
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. lvaEdge の IpcMode および spatial analysis モジュールの createOptions は同じであり、host に設定されている必要があります。
1. 展開テンプレート ファイルのファイル名に "deployment" が含まれている必要があります。そうしないと、認識されず、展開用のマニフェストを生成できません。
1. RTSP シミュレーターを機能させるには、ボリュームの境界が設定されていることを確認します。 詳細については、[Docker Volume マウントの設定](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts)に関するページを参照してください。

    1. [SMB 共有](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share)に接続し、[ビデオ ファイル](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv)をローカル共有にコピーします。
    1. rtspsim モジュールには次のものがあることを確認してください。
        
        ```json
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
1. [Azure IoT Hub] ペインの横にある [その他のアクション] アイコンを選択して、IoT Hub 接続文字列を設定します。 この文字列は、src/cloud-to-device-console-app/appsettings.json ファイルからコピーすることができます。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="空間分析: 接続文字列":::
1. src/edge/deployment.template.json を右クリックして、 [Generate IoT Edge deployment manifest]\(IoT Edge 配置マニフェストの生成\) を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="空間分析: 展開 amd64 json":::
    
    このアクションによって、deployment.amd64.json という名前のマニフェスト ファイルが src/edge/config フォルダーに作成されます。
1. src/edge/config/deployment.amd64.json を右クリックして、 [Create Deployment for Single Device]\(単一デバイスのデプロイの作成\) を選択し、エッジ デバイスの名前を選択します。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="空間分析: 展開テンプレート json":::   
1. IoT Hub デバイスを選択するように求めるメッセージが表示されたら、ドロップダウン メニューから Azure Stack Edge 名を選択します。
1. 約 30 秒後に、ウィンドウの左下隅で Azure IoT Hub を最新の情報に更新します。 エッジ デバイスには、次のデプロイ済みモジュールが表示されます。
    
    * Live Video Analytics on IoT Edge (モジュール名 lvaEdge)。
    * リアルタイム ストリーミング プロトコル (RTSP) シミュレーター (モジュール名 rtspsim)。
    * 空間分析 (モジュール名 spatialAnalysis)。
    
デプロイが正常に行われると、次のようなメッセージが出力に表示されます。

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

また、[Devices/Modules]\(デバイス/モジュール\) の下に `lvaEdge`、`rtspsim`、`spatialAnalysis`、`rtspsim` のモジュールがあり、それらの状態は "実行中" になっています。

## <a name="prepare-to-monitor-events"></a>イベントの監視の準備をする

それらのイベントを確認するには、次の手順に従います。

1. Visual Studio Code の [エクスプローラー] ペインを開き、左下隅にある [Azure IoT Hub] を探します。
1. [Devices] ノードを展開します。
1. Azure Stack Edge を右クリックして、[組み込みイベント エンドポイントの監視を開始する] を選択します。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="空間分析: 監視の開始":::
     
## <a name="run-the-program"></a>プログラムの実行

src/cloud-to-device-console-app/operations.json のダイレクト メソッドを呼び出す program.cs があります。 operations.json を設定し、メディア グラフで使用するためのトポロジを提供する必要があります。
operations.json で、次のようにします。

次のようなトポロジを設定します (ローカル トポロジの場合は topologyFile、オンライン トポロジの場合は topologyUrl)。

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

次のようなグラフ インスタンスを作成し、トポロジのパラメーターは次のように設定します。

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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

<p>
<details>
<summary>展開して、spatialAnalysis モジュールのサンプルのトポロジ ファイルを表示します。
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
        {
          "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "image": {
            "scale": {
              "mode": "pad",
              "width": "1408",
              "height": "786"
            },
            "format": {
              "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
              "pixelFormat": "bgr24"
            }
          },
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


MediaGraphRealTimeComputerVisionExtension を使用して、spatial-analysis モジュールと接続します。 tcp://spatialAnalysis:<PORT_NUMBER> で ${grpcUrl} を設定します (つまり、tcp://spatialAnalysis:50051)

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

デバッグ セッションを実行し、ターミナルの指示に従って、トポロジの設定、グラフ インスタンスの設定、グラフ インスタンスのアクティブ化を行い、最後にリソースの削除を行います。

## <a name="interpret-results"></a>結果を解釈する

メディア グラフのインスタンスが作成されると、"MediaSessionEstablished" イベントが表示されます。ここでは、[サンプルの MediaSessionEstablished イベント](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)です。

また、spatial-analysis モジュールは、AI 分析情報イベントを Live Video Analytics に送信した後、IoTHub に送信し、出力にも表示します。 ENTITY は検出オブジェクトであり、EVENT はスペース分析イベントです。 この出力は、Live Video Analytics に渡されます。

personZoneEvent のサンプル出力 (cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics 操作から):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>次のステップ

配置マニフェスト ファイルのグラフ ノードで "enabled" フラグを切り替えることによって、`spatialAnalysis` モジュールが提供するさまざまな操作 (**personCount** や **personDistance** など) を試してみてください。
>[!Tip]
> フレームに複数の人が含まれている[ビデオ ファイル](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)を使用します。

> [!NOTE]
> 一度に実行できる操作は 1 つだけです。 したがって、**true** に設定されているフラグは 1 つだけで、他のフラグは **false** に設定されていることを確認してください。
