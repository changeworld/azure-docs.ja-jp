---
title: Azure AD を使用して Azure Cosmos DB アカウントのマネージド ID を構成する
description: Azure Active Directory を使用して Azure Cosmos DB アカウントのマネージド ID を構成する方法について説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/23/2021
ms.author: thweiss
ms.openlocfilehash: 3f33cc08fcb9f3c43d9da312ce9ff12d9b20d722
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065297"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Active Directory を使用して Azure Cosmos DB アカウントのマネージド ID を構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この記事では、Azure Cosmos DB アカウントのマネージド ID を作成する方法について説明します。

> [!NOTE]
> 現在、Azure Cosmos DB では、システム割り当てのマネージド ID のみがサポートされています。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID について不明な場合は、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。 マネージド ID の種類の詳細については、「[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)」を参照してください。
- マネージド ID を設定するには、アカウントに [DocumentDB Account Contributor ロール](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)が必要です。

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する

### <a name="using-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートの使用

> [!IMPORTANT]
> マネージド ID を操作するときは、`apiVersion` の `2021-03-15` 以降を使用してください。

新規または既存の Azure Cosmos DB アカウントでシステム割り当て ID を有効にするには、リソース定義に次のプロパティを含めます。

```json
"identity": {
    "type": "SystemAssigned"
}
```

ARM テンプレートの `resources` セクションは、次のようになります。

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Azure Cosmos DB アカウントが作成または更新されると、次のプロパティが表示されます。

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

新しい Azure Cosmos DB アカウントの作成中にシステム割り当て ID を有効にするには、`--assign-identity` オプションを追加します。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity
```

`az cosmosdb identity assign` コマンドを使用して、システム割り当て ID を既存のアカウントに追加することもできます。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
```

Azure Cosmos DB アカウントが作成または更新された後、割り当てられた ID を `az cosmosdb identity show` コマンドで取得できます。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-identity"></a>システム割り当て ID を削除する

### <a name="using-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートの使用

> [!IMPORTANT]
> マネージド ID を操作するときは、`apiVersion` の `2021-03-15` 以降を使用してください。

システム割り当て ID を Azure Cosmos DB アカウントから削除するには、`identity` プロパティの `type` を `None` に設定します。

```json
"identity": {
    "type": "None"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

システム割り当て ID を Azure Cosmos DB アカウントから削除するには、`az cosmosdb identity remove` コマンドを使用します。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity remove \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

- 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について学びます。
- [Azure Cosmos DB でのカスタマー マネージド キー](how-to-setup-cmk.md)について学習してください
