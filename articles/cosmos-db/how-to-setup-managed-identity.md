---
title: Azure AD を使用して Azure Cosmos DB アカウントのマネージド ID を構成する
description: Azure Active Directory を使用して Azure Cosmos DB アカウントのマネージド ID を構成する方法について説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230925"
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

## <a name="next-steps"></a>次のステップ

- 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について学びます。
- [Azure Cosmos DB でのカスタマー マネージド キー](how-to-setup-cmk.md)について学習してください
