---
title: Azure Container Instances (ACI) でゾーン コンテナー グループをデプロイする
description: 可用性ゾーンにコンテナー グループをデプロイする方法について説明します。
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: a2cce289dc95ecc876ba308ed9406fffb37b9e18
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858443"
---
# <a name="deploy-an-azure-container-instances-aci-container-group-in-an-availability-zone-preview"></a>可用性ゾーンに Azure Container Instances (ACI) コンテナー グループをデプロイする (プレビュー)

[可用性ゾーン][availability-zone-overview]とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンを使用すると、コンテナー化されたアプリケーションをデータ センター全体のめったにない障害や損失から保護できます。 Azure Container Instances (ACI) は、ゾーン コンテナー グループのデプロイをサポートしています。したがって、インスタンスは、特定の自己選択された可用性ゾーンに固定されます。 可用性ゾーンは、コンテナー グループ レベルで指定します。 コンテナー グループ内のコンテナーは、一意の可用性ゾーンを持つことができません。 コンテナー グループの可用性ゾーンを変更するには、そのコンテナー グループを削除し、新しい可用性ゾーンを持つ別のコンテナー グループを作成する必要があります。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、追加使用条件に同意することを条件に使用できます。

> [!IMPORTANT]
> ゾーン コンテナー グループのデプロイは、Linux および Windows サーバー 2019 のコンテナー グループで ACI が使用できるほとんどのリージョンでサポートされています。 詳細については、[リージョンとリソースの可用性][container-regions]に関するページをご覧ください。

> [!NOTE]
> この記事の例は、Bash シェル形式で示してあります。 別のシェルを使用する場合は、必要に応じて行連結文字を調整してください。

## <a name="limitations"></a>制限事項

> [!IMPORTANT]
> この機能は、Azure portal では現在使用できません。

* 現時点では、GPU リソースを含むコンテナー グループは可用性ゾーンをサポートしていません。
* 現時点では、仮想ネットワークで挿入されたコンテナー グループは可用性ゾーンをサポートしていません。
* 現時点では、Windows Sever 2016 のコンテナー グループは可用性ゾーンをサポートしていません。

### <a name="version-requirements"></a>バージョンの要件

* Azure CLI を使用する場合は、バージョン `2.30.0` 以降がインストールされていることを確認します。
* PowerShell を使用する場合は、バージョン `2.1.1-preview` 以降がインストールされていることを確認します。
* Java SDK を使用する場合は、バージョン `2.9.0` 以降がインストールされていることを確認します。
* 可用性ゾーンのサポートは、ACI API バージョン `09-01-2021` 以降でのみ使用できます。

## <a name="deploy-a-container-group-using-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートを使用してコンテナー グループをデプロイする

### <a name="create-the-arm-template"></a>ARM テンプレートを作成する

まず、次の JSON を `azuredeploy.json` という名前の新しいファイルにコピーします。 このテンプレート例では、1 つのコンテナーを含むコンテナー グループが、米国東部の可用性ゾーン 1 にデプロイされます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "_generator": {
            "name": "bicep",
            "version": "0.4.1.14562",
            "templateHash": "12367894147709986470"
        }
    },
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "acilinuxpublicipcontainergroup",
            "metadata": {
                "description": "Name for the container group"
            }
        },
        "image": {
            "type": "string",
            "defaultValue": "mcr.microsoft.com/azuredocs/aci-helloworld",
            "metadata": {
                "description": "Container image to deploy. Should be of the form repoName/imagename:tag for images stored in public Docker Hub, or a fully qualified URI for other registries. Images from private registries require additional registry credentials."
            }
        },
        "port": {
            "type": "int",
            "defaultValue": 80,
            "metadata": {
                "description": "Port to open on the container and the public IP address."
            }
        },
        "cpuCores": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The number of CPU cores to allocate to the container."
            }
        },
        "memoryInGb": {
            "type": "int",
            "defaultValue": 2,
            "metadata": {
                "description": "The amount of memory to allocate to the container in gigabytes."
            }
        },
        "restartPolicy": {
            "type": "string",
            "defaultValue": "Always",
            "allowedValues": [
                "Always",
                "Never",
                "OnFailure"
            ],
            "metadata": {
                "description": "The behavior of Azure runtime if container has stopped."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "eastus",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2021-09-01",
            "zones": [
                "1"
            ],
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "properties": {
                "containers": [
                    {
                        "name": "[parameters('name')]",
                        "properties": {
                            "image": "[parameters('image')]",
                            "ports": [
                                {
                                    "port": "[parameters('port')]",
                                    "protocol": "TCP"
                                }
                            ],
                            "resources": {
                                "requests": {
                                    "cpu": "[parameters('cpuCores')]",
                                    "memoryInGB": "[parameters('memoryInGb')]"
                                }
                            }
                        }
                    }
                ],
                "osType": "Linux",
                "restartPolicy": "[parameters('restartPolicy')]",
                "ipAddress": {
                    "type": "Public",
                    "ports": [
                        {
                            "port": "[parameters('port')]",
                            "protocol": "TCP"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {
        "containerIPv4Address": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups', parameters('name'))).ipAddress.ip]"
        }
    }
}
```

### <a name="deploy-the-arm-template"></a>ARM テンプレートをデプロイする

[az group create][az-group-create] コマンドでリソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] コマンドを使用してテンプレートをデプロイします。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

## <a name="get-container-group-details"></a>コンテナー グループの詳細を取得する

コンテナー グループが可用性ゾーンに正常にデプロイされたことを確認するには、[az container show][az-container-show] コマンドを使用してコンテナー グループの詳細を表示します。

```azurecli
az containershow --name acilinuxcontainergroup --resource-group myResourceGroup
```

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[container-regions]: container-instances-region-availability.md
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment#az_deployment_group_create
[availability-zone-overview]: /availability-zones/az-overview.md