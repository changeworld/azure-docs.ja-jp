---
title: 'チュートリアル: 入れ子になった IoT Edge デバイスに接続済みレジストリをデプロイする'
description: Azure CLI コマンドを使用して、入れ子になった Azure IoT Edge 階層に接続済み Azure コンテナー レジストリをデプロイします。
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3652a87de4e517f2f40821a5e6e7367c1c88b078
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092598"
---
# <a name="tutorial-deploy-a-connected-registry-to-a-nested-iot-edge-hierarchy"></a>チュートリアル: 入れ子になった IoT Edge 階層に接続済みレジストリをデプロイする

このチュートリアルでは、Azure CLI コマンドを使用して Azure IoT Edge デバイスの 2 層の階層を作成し、各層で[接続済みレジストリ](intro-connected-registry.md)をモジュールとしてデプロイします。

IoT Edge での接続済みレジストリの使用の概要については、「[Azure IoT Edge で接続済みレジストリを使用する](overview-connected-registry-and-iot-edge.md)」を参照してください。 このシナリオでは、[上層](overview-connected-registry-and-iot-edge.md#top-layer)のデバイスはクラウド レジストリと通信し、[下層](overview-connected-registry-and-iot-edge.md#top-layer)のデバイスは、上層の親である接続済みレジストリと通信します。 

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub。 デプロイ手順については、「[Azure portal を使用して IoT Hub を作成する](../iot-hub/iot-hub-create-through-portal.md)」を参照してください。
* Azure 内の 2 つの接続済みレジストリ リソース。 デプロイ手順については、[Azure CLI][quickstart-connected-registry-cli] または [Azure portal][quickstart-connected-registry-portal] を使用するクイックスタートを参照してください。 

    * 上層では、接続済みレジストリは、ReadWrite または ReadOnly のいずれかのモードになります。 この記事は ReadWrite モードを前提としており、接続済みレジストリ名は環境変数 *$CONNECTED_REGISTRY_RW* に保存されます。
    * 下層では、接続済みレジストリは ReadOnly モードである必要があります。 この記事は、接続済みレジストリ名が環境変数 *$CONNECTED_REGISTRY_RO* に保存されることを前提としています。

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>接続済みレジストリの構成を取得する 

各接続済みレジストリを階層内の IoT Edge デバイスにデプロイするには、Azure 内の接続済みレジストリ リソースから構成設定を取得する必要があります。 必要に応じて、接続済みレジストリごとに [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] コマンドを実行して構成を取得します。 

既定では、設定情報に[同期トークン](overview-connected-registry-access.md#sync-token)のパスワードは含まれていません。接続済みレジストリをデプロイするには、これも必要です。 必要に応じて、`--generate-password 1` または `generate-password 2` パラメーターを渡してパスワードの 1 つを生成します。 生成されたパスワードを安全な場所に保存します。 もう一度取得することはできません。

> [!WARNING]
> パスワードを再生成すると、同期トークンの資格情報がローテーションされます。 以前のパスワードを使用してデバイスを構成した場合は、構成を更新する必要があります。

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

# Run the command for each registry resource in the hierarchy

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --parent-protocol https
```

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-deployment-manifests-for-the-nested-iot-edge-devices"></a>入れ子になった IoT Edge デバイスの配置マニフェストを構成する

配置マニフェストは、IoT Edge デバイスにデプロイするモジュールを記述した JSON ドキュメントです。 詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](../iot-edge/module-composition.md)」を参照してください。

Azure CLI を使用して接続済みレジストリ モジュールを各 IoT Edge デバイスにデプロイするには、次の配置マニフェストを JSON ファイルとしてローカルに保存します。 前のセクションの情報を使用して、各マニフェストで関連する JSON 値を更新します。 コマンドを実行して構成をデバイスに適用するとき、次のセクションのファイル パスを使用します。

### <a name="deployment-manifest-for-the-top-layer"></a>上層の配置マニフェスト

上層のデバイスのために、次の内容を含む配置マニフェスト ファイル `deploymentTopLayer.json` を作成します。 このマニフェストは、「[クイック スタート: 接続済みレジストリを IoT Edge デバイスに展開する](quickstart-deploy-connected-registry-iot-edge-cli.md)」で使用したものと似ています。 

> [!NOTE]
> [クイックスタート](quickstart-deploy-connected-registry-iot-edge-cli.md)を使用して接続済みレジストリを上層の IoT Edge デバイスに既にデプロイしている場合、入れ子になった階層の上層でそのレジストリを使用できます。 階層でこれを構成するには、このチュートリアルのデプロイ手順を変更する必要があります (これは示されていません)。

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

### <a name="deployment-manifest-for-the-lower-layer"></a>下層の配置マニフェスト

下層のデバイスのために、次の内容を含む配置マニフェスト ファイル `deploymentLowerLayer.json` を作成します。

下層の配置ファイルは上層の配置ファイルに全体的に似ています。 違いは次のとおりです。 

- クラウド レジストリからではなく、上層の接続済みレジストリから必要なイメージをプルします。 
    
    上層の接続済みレジストリを設定するとき、ローカルで必要なすべてのイメージ (`azureiotedge-agent`、`azureiotedge-hub`、`azureiotedge-api-proxy`、`acr/connected-registry`) が同期するようにします。 下層の IoT デバイスは、上層の接続済みレジストリからこれらのイメージをプルする必要があります。
- 上層の接続済みレジストリで認証を行うために、下層で構成された同期トークンが使用されます。
- クラウド レジストリの FQDN ではなく、上層の接続済みレジストリの IP アドレスまたは FQDN を使用して、親ゲートウェイ エンドポイントが構成されます。 

> [!IMPORTANT]
> 次の配置マニフェストでは、親の接続済みレジストリをホストするデバイスの IP アドレスまたは FQDN として `$upstream` が使用されます。 ただし、`$upstream` は環境変数ではサポートされません。 接続済みレジストリは、親ゲートウェイ エンドポイントを取得するために環境変数 `ACR_PARENT_GATEWAY_ENDPOINT` を読み取る必要があります。 `$upstream` を使用する代わりに、接続済みレジストリは、別の環境変数からの IP アドレスまたは FQDN の動的解決をサポートしています。 入れ子になった IoT Edge では、親デバイスの IP アドレスまたは FQDN と同等の環境変数 `$IOTEDGE_PARENTHOSTNAME` が下層にあります。 親 IP アドレスまたは FQDN のハードコーディングを回避するために、この環境変数を、接続文字列で `ParentGatewayEndpoint` の値として手動で置き換えてください。 この例の親デバイスは、ポート 8000 で nginx を実行するため、`$IOTEDGE_PARENTHOSTNAME:8000` を渡します。 また、`ParentEndpointProtocol` で適切なプロトコルを選択する必要もあります。

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "$upstream:8000/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=<REPLACE_WITH_SYNC_TOKEN_PASSWORD>;ParentGatewayEndpoint=$IOTEDGE_PARENTHOSTNAME:8000;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeApiProxy": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\": \"8000\"}]}}}"
                        },
                        "type": "docker",
                        "version": "1.0",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "NGINX_CONFIG_ENV_VAR_LIST": {
                                    "value": "NGINX_DEFAULT_PORT,BLOB_UPLOAD_ROUTE_ADDRESS,CONNECTED_ACR_ROUTE_ADDRESS,IOTEDGE_PARENTHOSTNAME,DOCKER_REQUEST_ROUTE_ADDRESS"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "startupOrder": 3
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "connectedregistry": {
                                "address": "$upstream:8000",
                                "password": "<REPLACE_WITH_SYNC_TOKEN_PASSWORD>",
                                "username": "<REPLACE_WITH_SYNC_TOKEN_NAME>"
                            }
                        }
                    },
                    "type": "docker"
                },
                "schemaVersion": "1.1",
                "systemModules": {
                    "edgeAgent": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-agent:1.2.4",
                            "createOptions": ""
                        },
                        "type": "docker",
                        "env": {
                            "SendRuntimeQualityTelemetry": {
                                "value": "false"
                            }
                        }
                    },
                    "edgeHub": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-hub:1.2.4",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                        },
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always"
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "routes": {
                    "route": "FROM /messages/* INTO $upstream"
                },
                "schemaVersion": "1.1",
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 7200
                }
            }
        }
    }
}
```

## <a name="set-up-and-deploy-connected-registry-modules-on-nested-iot-edge-devices"></a>接続済みレジストリ モジュールを設定し、入れ子になった IoT Edge デバイスにデプロイする

次の手順は、「[チュートリアル: IoT Edge デバイスの階層を作成する](../iot-edge/tutorial-nested-iot-edge.md)」を応用したもので、接続済みレジストリ モジュールを IoT Edge 階層にデプロイすることに特化しています。 個々の手順について詳しくは、このチュートリアルを参照してください。

### <a name="create-top-layer-and-lower-layer-devices"></a>上層と下層のデバイスを作成する

既存の [ARM テンプレート](https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json)を使用して、上層と下層の VM を作成します。 このテンプレートでは、IoT Edge エージェントもインストールされます。 代わりに独自のデバイスからデプロイする場合は、デバイスを手動で設定する方法を学習するために、「[チュートリアル: Azure IoT Edge for Linux をインストールまたはアンインストールする](../iot-edge/how-to-install-iot-edge.md)」を参照してください。

> [!IMPORTANT]
> 上層のデバイスにデプロイされたモジュールに後でアクセスするために、受信ポート 8000、443、5671、8883 を開く必要があります。 構成手順については、「[Azure portal を使用して仮想マシンへのポートを開く方法](../virtual-machines/windows/nsg-quickstart-portal.md)」を参照してください。

### <a name="create-and-configure-the-hierarchy"></a>階層を作成して構成する

Azure CLI または Azure Cloud Shell で次の手順を実行して、`iotedge-config` ツールで階層を作成して構成します。

1. 構成ツールをダウンロードします。
    ```bash
    mkdir nestedIotEdgeTutorial
    cd ~/nestedIotEdgeTutorial
    wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
    tar -xvf iotedge_config.tar
    ```

    この手順では、チュートリアル ディレクトリに `iotedge_config_cli_release` フォルダーが作成されます。 デバイス階層の作成に使用されるテンプレート ファイルは、`~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` にある `iotedge_config.yaml` ファイルです。 同じディレクトリに、上層と下層の 2 つの配置マニフェスト `deploymentTopLayer.json` および `deploymentLowerLayer.json` ファイルがあります。 

1. 自分の情報を使用して `iotedge_config.yaml` を編集します。 これには、`iothub_hostname`、`iot_name`、上層と下層の配置マニフェスト ファイル名、各層でアップストリームからイメージをプルするために作成したクライアント トークン資格情報が含まれます。 構成の例を次に示します

    ```yaml
    config_version: "1.0"

    iothub:
        iothub_hostname: <REPLACE_WITH_HUB_NAME>.azure-devices.net
        iothub_name: <REPLACE_WITH_HUB_NAME>
        ## Authentication method used by IoT Edge devices: symmetric_key or x509_certificate
        authentication_method: symmetric_key 

        ## Root certificate used to generate device CA certificates. Optional. If not provided a self-signed CA will be generated
        # certificates:
        #   root_ca_cert_path: ""
        #   root_ca_cert_key_path: ""

        ## IoT Edge configuration template to use
    configuration:
        template_config_path: "./templates/tutorial/device_config.toml"
        default_edge_agent: "$upstream:8000/azureiotedge-agent:1.2.4"

        ## Hierarchy of IoT Edge devices to create
    edgedevices:
        device_id: top-layer
        edge_agent: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4" ## Optional. If not provided, default_edge_agent will be used
        deployment: "./templates/tutorial/deploymentTopLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
            # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
        container_auth: ## The token used to pull the image from cloud registry
            serveraddress: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io"
            username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_TOP_LAYER>"
            password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_TOP_LAYER>"
        child:
            - device_id: lower-layer
              deployment: "./templates/tutorial/deploymentLowerLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
               # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
              container_auth: ## The token used to pull the image from parent connected registry
                serveraddress: "$upstream:8000"
                username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_LOWER_LAYER>"
                password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_LOWER_LAYER>"
    ```


1. 上層と下層の配置ファイル (deploymentTopLayer.json と deploymentLowerLayer.json) を準備します。 この記事の前半で作成した[配置マニフェスト ファイル](#configure-deployment-manifests-for-the-nested-iot-edge-devices)を次のフォルダーにコピーします: `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`。

1. `iotedge_config_cli_release` ディレクトリに移動し、ツールを実行して IoT Edge デバイスの階層を作成します。

    ```bash
    cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
    ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
    ```

    `--output` フラグを指定すると、このツールでは、選択したディレクトリにデバイス証明書、証明書バンドル、ログ ファイルが作成されます。 `-f` フラグを設定すると、エラーを回避し、IoT ハブをクリーンな状態に保つために、IoT ハブにある既存の IoT Edge デバイスが自動的に検索され、削除されます。

    このツールの実行時間は数分間になる場合があります。

1. `scp` を使用して、前の手順で生成された `top-layer.zip` および `lower-layer.zip` ファイルを対応する上層と下層の VM にコピーします。

    ```bash
    scp <PATH_TO_CONFIGURATION_BUNDLE>   <USER>@<VM_IP_OR_FQDN>:~
    ```

1. 上層のデバイスに接続して、構成バンドルをインストールします。
    1. 構成バンドルを解凍します。 最初に、zip をインストールしておく必要があります。

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip top-layer.zip
        ```
    1. `sudo ./install.sh` を実行し、IP アドレスまたはホスト名を入力します。 IP アドレスを使用することをお勧めします。
    1. `sudo iotedge list` を実行して、すべてのモジュールが実行されていることを確認します。

1. 下層のデバイスに接続して、構成バンドルをインストールします。
    1. 構成バンドルを解凍します。 最初に、zip をインストールしておく必要があります。

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip lower-layer.zip
        ```
    1. `sudo ./install.sh` を実行し、デバイスと親の IP アドレスまたはホスト名を入力します。 IP アドレスを使用することをお勧めします。
    1. `sudo iotedge list` を実行して、すべてのモジュールが実行されていることを確認します。
 
    
デバイス構成用の配置ファイルを指定しなかった場合、あるいは上層または下層のデバイスで、無効な配置マニフェストなどのデプロイの問題が発生した場合は、手動でモジュールをデプロイします。 次のセクションを参照してください。

## <a name="manually-deploy-the-connected-registry-module"></a>接続済みレジストリ モジュールを手動でデプロイする

次のコマンドを使用して、接続済みレジストリ モジュールを IoT Edge デバイスに手動でデプロイします。

```azurecli
az iot edge set-modules \
  --device-id <device-id> \
  --hub-name <hub-name> \
  --content <deployment-manifest-filename>
```

詳細については、「[Azure CLI を使用して Azure IoT Edge モジュールをデプロイする](../iot-edge/how-to-deploy-modules-cli.md)」を参照してください。

デプロイが正常に完了すると、接続済みレジストリで `Online` 状態が示されます。

接続済みレジストリの状態を確認するには、次の [az acr connected-registry show][az-acr-connected-registry-show] コマンドを使用します。

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --output table
```

接続済みレジストリのデプロイが完了するまで、数分間待機する必要がある場合があります。

デプロイが正常に完了すると、接続済みレジストリで `Online` 状態が示されます。

デプロイのトラブルシューティングを行うには、影響を受けたデバイスで `iotedge check` を実行します。 詳細については、[トラブルシューティングのヒント](../iot-edge/tutorial-nested-iot-edge.md#troubleshooting)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、接続済みレジストリを入れ子になった IoT Edge デバイスにデプロイする方法について学習しました。 次のガイドに進み、新しくデプロイされた接続済みレジストリからイメージをプルする方法について学習してください。

> [!div class="nextstepaction"]
> [コネクテッド レジストリからイメージをプルする][pull-images-from-connected-registry]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-acr-token-credential-generate]: /cli/azure/acr/credential#az-acr-token-credential-generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
