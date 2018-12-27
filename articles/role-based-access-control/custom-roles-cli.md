---
title: Azure CLI を使用してカスタム ロールを作成する | Microsoft Docs
description: Azure CLI を使用して、ロールベースのアクセス制御 (RBAC) のカスタム ロールを作成する方法について説明します。 これには、カスタム ロールを一覧表示、作成、更新、削除する方法が含まれます。
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3b5d18a3e0bf846137dfdf68b8e5dd9e2db58792
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437258"
---
# <a name="create-custom-roles-using-azure-cli"></a>Azure CLI を使用してカスタム ロールを作成する

[組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 この記事では、Azure CLI を使用して、カスタム ロールを作成して管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、次のものが必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可
- ローカルにインストールされた [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

割り当てに使用できるカスタム ロールを一覧表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。 次の例では、現在のサブスクリプションのすべてのカスタム ロールを一覧表示します。

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[az role definition create](/cli/azure/role/definition#az-role-definition-create) を使用します。 ロール定義には、JSON 記述を含むファイルへの JSON 記述またはパスを指定できます。

```azurecli
az role definition create --role-definition <role_definition>
```

次の例では、"*仮想マシン オペレーター*" というカスタム ロールが作成されます。 このカスタム ロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作に対するアクセス許可を割り当てて、仮想マシンの起動、再起動、監視を行うためのアクセス許可を割り当ています。 このカスタム ロールは、2 つのサブスクリプションで使うことができます。 この例では、入力として JSON ファイルを使用します。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを更新するには、最初に [az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用して、ロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、[az role definition update](/cli/azure/role/definition#az-role-definition-update) を使用して、更新されたロール定義を保存します。

```azurecli
az role definition update --role-definition <role_definition>
```

次の例では、*Microsoft.Insights/diagnosticSettings/* 操作が "*仮想マシン オペレーター*" の *Actions* に追加されます。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[az role definition delete](/cli/azure/role/definition#az-role-definition-delete) を使用します。 削除するロールを指定するには、ロール名またはロール ID を使用します。 ロール ID を決定するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。

```azurecli
az role definition delete --name <role_name or role_id>
```

次の例では、*仮想マシン オペレーター* カスタム ロールが削除されます。

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>次の手順

- [チュートリアル: Azure CLI を使用してカスタム ロールを作成する](tutorial-custom-role-cli.md)
- [Azure のカスタム ロール](custom-roles.md)
- [Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)