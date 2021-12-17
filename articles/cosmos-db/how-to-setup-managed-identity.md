---
title: Azure AD を使用して Azure Cosmos DB アカウントのマネージド ID を構成する
description: Azure Active Directory を使用して Azure Cosmos DB アカウントのマネージド ID を構成する方法について説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2021
ms.author: thweiss
ms.openlocfilehash: 826afef5d637278628146af8a35f78232e5b3531
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132923"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Active Directory を使用して Azure Cosmos DB アカウントのマネージド ID を構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この記事では、Azure Cosmos DB アカウントのマネージド ID を作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID について不明な場合は、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。 マネージド ID の種類の詳細については、「[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)」を参照してください。
- マネージド ID を設定するには、アカウントに [DocumentDB Account Contributor ロール](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)が必要です。

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

既存の Azure Cosmos DB アカウントでシステム割り当てマネージド ID を有効にするには、Azure portal でアカウントに移動し、左側のメニューから **[ID]** を選択します。

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="ID メニューの項目" border="true":::

**[システム割り当て済み]** セクションで、**[状態]** をオンに切り替えて、**[保存]** を選択します。 システム割り当てマネージド ID の作成を確認するメッセージが表示されます。

:::image type="content" source="./media/how-to-setup-managed-identity/enable-system-assigned.png" alt-text="システム割り当て ID の有効化" border="true":::

ID の作成と割り当てが完了すると、そのオブジェクト (プリンシパル) ID を取得できます。

:::image type="content" source="./media/how-to-setup-managed-identity/system-identity-enabled.png" alt-text="システム割り当て ID のオブジェクト ID を取得する" border="true":::

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

Azure Cosmos DB アカウントが作成または更新されると、割り当てられた ID を `az cosmosdb identity show` コマンドで取得できます。

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

## <a name="add-a-user-assigned-identity"></a>ユーザー割り当て ID を追加する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

既存の Azure Cosmos DB アカウントでユーザー割り当てマネージド ID を有効にするには、Azure portal でアカウントに移動し、左側のメニューから **[ID]** を選択します。

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="ID メニューの項目" border="true":::

**[ユーザー割り当て済み]** セクションで、**[追加]** を選択します。

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-1.png" alt-text="ユーザー割り当て ID の有効化" border="true":::

Azure Cosmos DB アカウントに割り当てる ID をすべて検索して選択し、**[追加]** を選択します。

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-2.png" alt-text="割り当てる ID をすべて選択する" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートの使用

> [!IMPORTANT]
> マネージド ID を操作するときは、`apiVersion` の `2021-03-15` 以降を使用してください。

新規または既存の Azure Cosmos DB アカウントでユーザー割り当て ID を有効にするには、リソース定義に次のプロパティを含めます。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<identity-resource-id>": {}
    }
}
```

ARM テンプレートの `resources` セクションは、次のようになります。

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "<identity-resource-id>": {}
            }
        },
        // ...
    },
    // ...
]
```

Azure Cosmos DB アカウントが作成または更新されると、次のプロパティが表示されます。

```json
"identity": {
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure Cosmos DB の新規アカウントを作成する際にユーザー割り当て ID を有効にするには、`--assign-identity` オプションを追加し、割り当てる ID のリソース ID を渡します。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity <identity-resource-id>
```

`az cosmosdb identity assign` コマンドを使用して、ユーザー割り当て ID を既存のアカウントに追加することもできます。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
    --identities <identity-resource-id>
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
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-or-user-assigned-identity"></a>システム割り当て ID またはユーザー割り当て ID を削除する

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

すべてのマネージド ID を Azure Cosmos DB アカウントから削除するには、`az cosmosdb identity remove` コマンドを使用します。

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
