---
title: Container Apps プレビューの ARM テンプレートの API 仕様
description: Container Apps の ARM テンプレートで使用できるプロパティを調べます。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: reference
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0f0c4c360b8b9b3cdbe78c833df2a76c2b27d84
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323526"
---
# <a name="container-apps-preview-arm-template-api-specification"></a>Container Apps プレビューの ARM テンプレートの API 仕様

Azure Container Apps のデプロイでは、Azure Resource Manager (ARM) テンプレートが使われます。 次の表では、コンテナー アプリ ARM テンプレートで使えるプロパティについて説明します。

[使用例のサンプル ARM テンプレート](#example)。

## <a name="resources"></a>リソース

ARM テンプレートの `resources` 配列内のエントリには、次のプロパティがあります。

| プロパティ | 説明 | データの種類 |
|---|---|--|
| `name` | Container Apps アプリケーションの名前。 | string |
| `location` | Container Apps のインスタンスがデプロイされている Azure リージョン。 | string |
| `tags` | コンテナー アプリに関連付けられている Azure タグのコレクション。 | array |
| `type` | 常に、`Microsoft.Web/containerApps` ARM エンドポイントによって転送先の API が決まります  | string |

この例では、`<>` 角かっこで囲まれたプレースホルダー トークンの代わりに値を指定します。

## <a name="properties"></a>properties

リソースの `properties` オブジェクトには、次のプロパティがあります。

| プロパティ | 説明 | データの種類 | 読み取り専用 |
|---|---|---|---|
| `provisioningState` | 新しいコンテナー リビジョンが作成されるときなど、実行時間の長い操作の状態。 設定される可能性のある値は、provisioning、provisioned、failed です。 アプリが稼働しているかどうかを調べます。 | string | はい |
| `environmentId` | コンテナー アプリの環境 ID。 **このプロパティは必須です。** | string | いいえ |
| `latestRevisionName` | 最新のリビジョンの名前。 | string | はい |
| `latestRevisionFqdn` | 最新のリビジョンの URL。 | string | はい |

`environmentId` の値の形式は次のとおりです。

```console
/subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/environmentId/<ENVIRONMENT_NAME>
```

この例では、`<>` 角かっこで囲まれたプレースホルダー トークンの代わりに値を指定します。

## <a name="propertiesconfiguration"></a>properties.configuration

リソースの `properties.configuration` オブジェクトには、次のプロパティがあります。

| プロパティ | 説明 | データの種類 |
|---|---|---|
| `activeRevisionsMode` | `multiple` に設定すると、複数のリビジョンを保持できます。 `single` に設定すると、古いリビジョンは自動的に非アクティブにされ、最新のリビジョンのみがアクティブになります。 | string |
| `secrets` | コンテナー アプリでのシークレットの値を定義します。 | object |
| `ingress` | コンテナー アプリのパブリック アクセス可能性の構成を定義するオブジェクト。 | object |
| `registries` | プライベート コンテナー レジストリの資格情報を参照する構成オブジェクト。 `secretref` で定義されるエントリでは、シークレット構成オブジェクトが参照されます。 | object |

`configuration` セクションの変更は[アプリケーション スコープの変更](revisions.md#application-scope-changes)であり、新しいリビジョンはトリガーされません。

## <a name="propertiestemplate"></a>properties.template

リソースの `properties.template` オブジェクトには、次のプロパティがあります。

| プロパティ | 説明 | データの種類 |
|---|---|---|
| `revisionSuffix` | リビジョンのフレンドリ名。 既存のリビジョン名のサフィックス値と競合するとランタイムによって拒否されるので、この値は一意である必要があります。 | string |
| `containers` | コンテナー アプリに含まれるコンテナー イメージを定義する構成オブジェクト。 | object |
| `scale` | コンテナー アプリのスケーリング ルールを定義する構成オブジェクト。 | object |
| `dapr` | コンテナー アプリの Dapr の設定を定義する構成オブジェクト。 | object  |

`template` セクションの変更は[リビジョン スコープの変更](revisions.md#revision-scope-changes)であり、新しいリビジョンがトリガーされます。

## <a name="example"></a>例

次に示すのは、コンテナー アプリのデプロイに使用される ARM テンプレートの例です。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "containerappName": {
            "defaultValue": "mycontainerapp",
            "type": "String"
        },
        "location": {
            "defaultValue": "canadacentral",
            "type": "String"
        },
        "environment_name": {
            "defaultValue": "myenvironment",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2021-03-01",
            "type": "Microsoft.Web/containerApps",
            "name": "[parameters('containerappName')]",
            "location": "[parameters('location')]",
            "properties": {
                "kubeEnvironmentId": "[resourceId('Microsoft.Web/kubeEnvironments', parameters('environment_name'))]",
                "configuration": {
                    "secrets": [
                        {
                            "name": "mysecret",
                            "value": "thisismysecret"
                        }
                    ],
                    "ingress": {
                        "external": true,
                        "targetPort": 80,
                        "allowInsecure": false,
                        "traffic": [
                            {
                                "latestRevision": true,
                                "weight": 100
                            }
                        ]
                    }
                },
                "template": {
                    "revisionSuffix": "myrevision",
                    "containers": [
                        {
                            "name": "nginx",
                            "image": "nginx",
                            "env": [
                                {
                                    "name": "HTTP_PORT",
                                    "value": "80"
                                },
                                {
                                    "name": "SECRET_VAL",
                                    "secretRef": "mysecret"
                                }
                            ],
                            "resources": {
                                "cpu": 0.5,
                                "memory": "1Gi"
                            }
                        }
                    ],
                    "scale": {
                        "minReplicas": 1,
                        "maxReplicas": 3
                    }
                }
            }
        }
    ]
}
```
