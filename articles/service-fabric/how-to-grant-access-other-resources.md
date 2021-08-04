---
title: アプリケーションに他の Azure リソースへのアクセス権を付与する
description: この記事では、マネージド ID が有効になった Service Fabric アプリケーションに、Azure Active Directory ベースの認証をサポートする他の Azure リソースへのアクセス権を付与する方法について説明します。
ms.topic: article
ms.date: 12/09/2019
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7f49a3f97862c3a141ea9376d0ffc9bf510d3e6f
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110782964"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Service Fabric アプリケーションのマネージド ID に Azure リソースへのアクセス権を付与する

アプリケーションがマネージド ID を使用して他のリソースにアクセスするためには、アクセスする保護された Azure リソースに対するアクセス許可がその ID に付与されている必要があります。 通常、アクセス許可の付与は、Azure Resource Manager 経由でルーティングされる保護されたリソースを所有している Azure サービスの "コントロール プレーン" に対する管理アクションです。これにより、適用可能なすべてのロールベースのアクセス チェックが実施されます。

正確な一連の手順は、アクセスする Azure リソースの種類と、アクセス許可を付与するために使用される言語/クライアントによって異なります。 この記事の残りの部分では、ユーザー割り当ての ID がアプリケーションに割り当てられていることを前提としています。参考のために、いくつかの一般的な例が含まれていますが、このトピックを網羅するリファレンスではありません。アクセス許可を付与するための最新の手順については、それぞれの Azure サービスのドキュメントを参照してください。  

## <a name="granting-access-to-azure-storage"></a>Azure Storage へのアクセス権を付与する
Service Fabric アプリケーションのマネージド ID (この場合はユーザー割り当て) を使用して、Azure Storage BLOB からデータを取得できます。 [Storage Blob Data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) ロールを *resource-group* スコープでアプリケーションのマネージド ID に割り当てることで、ID にストレージ アカウントに必要なアクセス許可を付与します。

詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。


## <a name="granting-access-to-azure-key-vault"></a>Azure Key Vault へアクセス権を付与する
ストレージへのアクセスと同様に、Service Fabric アプリケーションのマネージド ID を活用して Azure Key Vault にアクセスできます。 Azure portal でアクセス権を付与する手順は、上記の手順と同様であり、ここでは繰り返しません。 相違点については、次の図を参照してください。

![Key Vault アクセス ポリシー](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

次の例では、テンプレートのデプロイによってコンテナーへのアクセス権を付与する方法を示します。テンプレートの `resources` 要素の下に、別のエントリとして次のスニペットを追加します。 このサンプルでは、ユーザーが割り当てた ID の種類とシステムによって割り当てられたID の種類の両方に付与されるアクセス権を示します。適切なほうを選択してください。

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
システムによって割り当てられたマネージド ID の場合:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

詳細については、「[コンテナー - アクセス ポリシーの更新](/rest/api/keyvault/vaults/updateaccesspolicy)」を参照してください。

## <a name="next-steps"></a>次の手順
* [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
