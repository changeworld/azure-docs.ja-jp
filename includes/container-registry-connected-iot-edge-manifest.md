---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/10/2021
ms.author: danlep
ms.openlocfilehash: 4153c44c60e8da560b5523e5263f0f7e0c81fcec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090071"
---
### <a name="connected-registry-module-settings"></a>接続されたレジストリ モジュールの設定

* 前のセクションのトークン資格情報と接続文字列を使用して、`env` ノード内の関連する JSON 値を更新します。 
* 次の環境変数は、`env` ノードでは省略可能です。

    |変数  |説明  |
    |---------|---------|
    | `ACR_REGISTRY_LOGIN_SERVER` |  一意のホスト名または FQDN を指定します。 使用された場合、接続されたレジストリでは、このログイン サーバー値に対して行われた要求のみが受け入れられます。 <br/><br/>値が指定されていない場合、接続されたレジストリには、任意のログイン サーバー値を使用してアクセスできます。  |
    |`ACR_REGISTRY_CERTIFICATE_VOLUME`     |   接続されたレジストリに HTTPS 経由でアクセスできる場合は、HTTPS 証明書が格納されているボリュームをポイントします。<br/><br/>設定されていない場合、既定の場所は `/var/acr/certs` です。      |
    |`ACR_REGISTRY_DATA_VOLUME`     |  接続されたレジストリによってイメージが格納される既定の場所 `/var/acr/data` を上書きします。<br/><br/>この場所は、コンテナーのボリューム バインドと一致している必要があります。       |

    > [!IMPORTANT]
    > 接続されたレジストリが 80 および 443 とは異なるポートでリッスンする場合は、`ACR_REGISTRY_LOGIN_SERVER` 値 (指定されている場合) にポートを含める必要があります。 例: `192.168.0.100:8080`.
* API プロキシ モジュールが使用されていない場合は、接続されたレジストリの `HostPort` バインドを設定する必要があります。 例:

    ```azurecli
     "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"],\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"8080\"}]}}}"
    ```

### <a name="api-proxy-module-settings"></a>API プロキシ モジュールの設定

* API プロキシは、`NGINX_DEFAULT_PORT` として構成されたポート 8000 でリッスンします。 API プロキシ設定の詳細については、[IoT Edge の GitHub リポジトリ](https://github.com/Azure/iotedge/tree/master/edge-modules/api-proxy-module)を参照してください。 

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=REPLACE_WITH_SYNC_TOKEN_PASSWORD;ParentGatewayEndpoint=<REPLACE_WITH_CLOUD_REGISTRY_NAME>.<REPLACE_WITH_CLOUD_REGISTRY_REGION>.data.azurecr.io;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeAPIProxy": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8000/tcp\":[{\"HostPort\":\"8000\"}]}}}"
                        },
                        "type": "docker",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "cloudregistry": {
                                "address": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-hub:1.2.4",
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
