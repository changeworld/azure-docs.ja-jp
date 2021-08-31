---
title: Azure Resource Manager テンプレートを使用して Azure でのロールの割り当て条件を追加する (プレビュー) - Azure ABAC
description: Azure Resource Manager テンプレートと Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure ロールの割り当てで属性ベースのアクセス制御 (ABAC) 条件を追加する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 06/29/2021
ms.author: rolyon
ms.openlocfilehash: 6e64afaab3b367ed807f77e5ebc0214904ed763f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094954"
---
# <a name="add-azure-role-assignment-conditions-using-azure-resource-manager-templates-preview"></a>Azure Resource Manager テンプレートを使用して Azure でのロールの割り当て条件を追加する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure ロールの割り当て条件](conditions-overview.md)は、必要に応じてロールの割り当てに追加して、よりきめ細かなアクセス制御を提供できるようにする追加のチェックです。 たとえば、特定のタグが設定されたオブジェクトしか読み取れない、という条件を設定できます。 この記事では、Azure Resource Manager テンプレートを使用して、ロールの割り当ての条件を追加する方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当て条件を追加するための前提条件については、[条件の前提条件](conditions-prerequisites.md)に関する記事をご覧ください。

## <a name="add-a-condition"></a>条件の追加

次のテンプレートは、条件を使用して[ストレージ BLOB データ 閲覧者](built-in-roles.md#storage-blob-data-reader)ロールを割り当てる方法を示しています。 この条件によって、コンテナー名が "blob-example-container" と等しいかどうかが確認されます。

テンプレートを使うには、次の入力を指定する必要があります。

- ロールの割り当て先となるユーザー、グループ、マネージド ID、またはアプリケーションの ID。
- プリンシパルの種類 (`User`、`Group`、`ServicePrincipal` など)。 詳細については、「[新しいサービス プリンシパル](role-assignments-template.md#new-service-principal)」をご覧ください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "Principal ID to assign the role to"
            }
        },
        "principalType": {
            "type": "string",
            "metadata": {
                "description": "Type of principal"
            }
        },
        "roleAssignmentGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "New GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "StorageBlobDataReader": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1')]" // ID for Storage Blob Data Reader role, but can be any valid role ID
    },
    "resources": [
        {
            "name": "[parameters('roleAssignmentGuid')]",
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview", // API version to call the role assignment PUT.
            "properties": {
                "roleDefinitionId": "[variables('StorageBlobDataReader')]",
                "principalId": "[parameters('principalId')]",
                "principalType": "[parameters('principalType')]",
                "description": "Role assignment condition created with an ARM template",
                "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))", // Role assignment condition
                "conditionVersion": "2.0"
            }
        }
    ]
}
```

ロール割り当てのスコープは、デプロイのレベルから特定できます。 次に示すのは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) および [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドの例です。リソース グループのスコープでデプロイを開始する方法を示しています。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName example-group -TemplateFile rbac-test.json -principalId $principalId -principalType "User"
```

```azurecli
az deployment group create --resource-group example-group --template-file rbac-test.json --parameters principalId=$principalId principalType="User"
```

## <a name="next-steps"></a>次のステップ

- [Azure ロールの割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [Azure ロール割り当て条件のトラブルシューティング (プレビュー)](conditions-troubleshoot.md)
- [Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](role-assignments-template.md)
