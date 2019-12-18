---
title: Azure RBAC と Azure CLI を使用してロールの割り当てを一覧表示する
description: Azure ロールベースのアクセス制御 (RBAC) と Azure CLI を使用して、ユーザー、グループ、サービス プリンシパル、およびマネージド ID のアクセスを決定する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74709895"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Azure RBAC と Azure CLI を使用してロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] この記事では、Azure CLI を使用してロールの割り当てを一覧表示する方法を説明します。

## <a name="prerequisites"></a>前提条件

- [Azure Cloud Shell の Bash](/azure/cloud-shell/overview) または [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

特定のユーザーのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

```azurecli
az role assignment list --assignee <assignee>
```

既定では、サブスクリプションをスコープとする直接割り当てのみが表示されます。 リソースまたはグループをスコープとする割り当てを表示する場合は `--all` を使用し、継承された割り当てを表示する場合は `--include-inherited` を使用します。

次の例では、*patlong\@contoso.com* ユーザーに直接割り当てられているロールの割り当てを一覧表示します。

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>リソース グループに対するロールの割り当ての一覧表示

リソース グループ スコープに存在するロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

```azurecli
az role assignment list --resource-group <resource_group>
```

次の例では、*pharma-sales* リソース グループに対するロールの割り当てを一覧表示します。

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>サブスクリプションのロールの割り当ての一覧表示

サブスクリプション スコープに存在するすべてのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで確認するか、[az account list](/cli/azure/account#az-account-list) を使用できます。

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>管理グループに対するロールの割り当ての一覧表示

管理グループ スコープに存在するすべてのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list) を使用します。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>次の手順

- [Azure RBAC と Azure CLI を使用してロールの割り当てを追加または削除する](role-assignments-cli.md)
