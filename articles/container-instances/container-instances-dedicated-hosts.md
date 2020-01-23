---
title: 専用ホストへのデプロイ
description: 専用ホストを使用してワークロードの真のホスト レベルの分離を実現する
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903552"
---
# <a name="deploy-on-dedicated-hosts"></a>専用ホストへのデプロイ

"Dedicated" は、コンテナーを安全に実行するための分離された専用コンピューティング環境を提供する Azure Container Instances (ACI) SKU です。 Dedicated SKU を使用すると、各コンテナー グループに Azure データセンター内の専用物理サーバーが割り当てられるため、組織のセキュリティおよびコンプライアンス要件を満たすために役立つ完全なワークロードの分離が保証されます。 

Dedicated SKU は、物理サーバーの観点からワークロードの分離を必要とするコンテナー ワークロードに適しています。

## <a name="using-the-dedicated-sku"></a>Dedicated SKU の使用

> [!IMPORTANT]
> Dedicated SKU の使用は、現在ロールアウトされている最新の API バージョン (2019-12-01) でのみ使用できます。デプロイ テンプレートでこの API バージョンを指定してください。 さらに、Dedicated SKU を使用するサブスクリプションに対する既定の制限は 0 です。 この SKU を運用コンテナーのデプロイに使用する場合は、[Azure サポート要求][azure-support]を作成してください。

API バージョン 2019-12-01 から、デプロイ テンプレートのコンテナー グループのプロパティのセクションには、ACI のデプロイに必要な "SKU" プロパティが存在します。 現在、このプロパティは、ACI 用の Azure Resource Manager デプロイ テンプレートの一部として使用できます。 ACI リソースのデプロイの詳細については、「[チュートリアル: Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)」にあるテンプレートで学習できます。 

SKU プロパティは、次の値のいずれかになります。
* Standard - ACI の標準のデプロイ選択。これにより、引き続きハイパーバイザー レベルのセキュリティが保証されます。 
* Dedicated - コンテナー グループの専用物理ホストによるワークロード レベルの分離に使用されます。

## <a name="modify-your-json-deployment-template"></a>JSON デプロイ テンプレートを変更する

コンテナー グループ リソースが指定されているデプロイ テンプレートで、`"apiVersion": "2019-12-01",` を確認します。 コンテナー グループ リソースのプロパティ セクションで、`"sku": "Dedicated",` を設定します。

Dedicated SKU を使用するコンテナー グループ デプロイ テンプレートのリソース セクションのスニペットの例を次に示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>コンテナー グループをデプロイする

デスクトップ上でデプロイ テンプレート ファイルを作成および編集した場合は、そのファイルをドラッグすることによって Cloud Shell ディレクトリにアップロードできます。 

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] コマンドで、テンプレートをデプロイします。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

数秒以内に、Azure から最初の応答を受信します。 デプロイが完了すると、ACI サービスによって保持されているそのデプロイに関連したデータはすべて、指定したキーで暗号化されます。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
