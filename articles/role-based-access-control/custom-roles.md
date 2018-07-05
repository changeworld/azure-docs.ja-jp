---
title: Azure のカスタム ロール | Microsoft Docs
description: Azure のリソースの詳細なアクセス管理を行うために、ロールベースのアクセス制御 (RBAC) を使用してカスタム ロールを定義する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 446cb34f2de8d0de3ee52e23df6cd26644d31bba
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435972"
---
# <a name="custom-roles-in-azure"></a>Azure のカスタム ロール

[組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 組み込みロールと同様、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、サブスクリプション、リソース グループ、リソースのスコープで割り当てることができます。 カスタム ロールは Azure Active Directory (Azure AD) テナントに保存され、サブスクリプション間で共有することができます。 各テナントは、最大 2,000 個のカスタム ロールを持つことができます。 カスタム ロールは、Azure PowerShell、Azure CLI、REST API で作成することができます。

## <a name="custom-role-example"></a>カスタム ロールの例

以下は、Azure PowerShell を使用して表示される、仮想マシンの監視と再起動を行うカスタム ロールを示しています。

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

カスタム ロールを作成した後、Azure portal にオレンジ色のリソース アイコンと共に表示されます。

![カスタム ロールのアイコン](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>カスタム ロールの作成手順

1. 必要なアクセス許可を決定する

    カスタム ロールを作成する場合、アクセス許可を定義するために使用可能なリソース プロバイダーの操作を把握しておく必要があります。 操作の一覧を表示するために、[Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) または [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list) コマンドを使用できます。
    カスタム ロールのアクセス許可を指定するには、操作を[ロール定義](role-definitions.md)の `actions` または `notActions` プロパティに追加します。 データ操作をする場合は、それらを `dataActions` または `notDataActions` プロパティに追加します。

2. カスタム ロールを作成する

    Azure PowerShell または Azure CLI を使用して、カスタム ロールを作成することができます。 通常は、まず、既存の組み込みロールを使用し、必要に応じてそれを変更します。 次に、[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) または [az role definition create](/cli/azure/role/definition#az-role-definition-create) コマンドを使用して、カスタム ロールを作成します。 カスタム ロールを作成するには、[所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、すべての `assignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` アクセス許可が必要になります。

3. カスタム ロールをテストする

    カスタム ロールを作成したら、それをテストして期待どおりに動作することを確認する必要があります。 調整が必要な場合は、カスタム ロールを更新できます。

## <a name="custom-role-properties"></a>カスタム ロールのプロパティ

カスタム ロールには、次のプロパティがあります。

| プロパティ | 必須 | type | 説明 |
| --- | --- | --- | --- |
| `Name` | [はい] | String | カスタム ロールの表示名。 テナントに対して一意である必要があります。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 128 文字です。 |
| `Id` | [はい] | String | カスタム ロールの一意の ID。 Azure PowerShell と Azure CLI では、新しいロールを作成するときに自動的にこの ID が生成されます。 |
| `IsCustom` | [はい] | String | これがカスタム ロールであるかどうかを示します。 カスタム ロールの場合は `true` に設定します。 |
| `Description` | [はい] | String | カスタム ロールの説明。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 1024 文字です。 |
| `Actions` | [はい] | String[] | ロールで実行できる管理操作を指定する文字列の配列。 詳細については、「[actions](role-definitions.md#actions)」を参照してください。 |
| `NotActions` | いいえ  | String[] | 許可された `actions` から除外される管理操作を指定する文字列の配列。 詳細については、「[notActions](role-definitions.md#notactions)」を参照してください。 |
| `DataActions` | いいえ  | String[] | 対象のオブジェクト内のデータに対して、ロールで実行できるデータ操作を指定する文字列の配列。 詳細については、「[dataActions (プレビュー)](role-definitions.md#dataactions-preview)」を参照してください。 |
| `NotDataActions` | いいえ  | String[] | 許可された `dataActions` から除外されるデータ操作を指定する文字列の配列。 詳細については、「[notDataActions (プレビュー)](role-definitions.md#notdataactions-preview)」を参照してください。 |
| `AssignableScopes` | [はい] | String[] | 割り当てにカスタム ロールを使用できるスコープを指定する文字列の配列。 ルート スコープ (`"/"`) に設定することはできません。 詳細については、「[assignableScopes](role-definitions.md#assignablescopes)」を参照してください。 |

## <a name="assignablescopes-for-custom-roles"></a>カスタム ロールの assignableScopes

組み込みロールと同じように、`assignableScopes` プロパティでは、割り当てにロールを使用できるスコープを指定します。 ただし、独自のカスタム ロールにルート スコープ (`"/"`) を使用することはできません。 試すと、承認エラーが発生します。 カスタム ロールの `assignableScopes` プロパティでは、カスタム ロールを作成、削除、変更、または表示できるユーザーも制御されます。

| タスク | 操作 | 説明 |
| --- | --- | --- |
| カスタム ロールの作成/削除 | `Microsoft.Authorization/ roleDefinition/write` | カスタム ロールのすべての `assignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープで使用するカスタム ロールを作成 (または削除) できます。 たとえば、サブスクリプション、リソース グループ、リソースの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの修正 | `Microsoft.Authorization/ roleDefinition/write` | カスタム ロールのすべての `assignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープ内のカスタム ロールを変更できます。 たとえば、サブスクリプション、リソース グループ、リソースの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの表示 | `Microsoft.Authorization/ roleDefinition/read` | あるスコープでこの操作を許可されたユーザーは、そのスコープで割り当て可能なカスタム ロールを表示できます。 すべての組み込みロールでは、カスタム ロールを割り当てることができます。 |

## <a name="next-steps"></a>次の手順
- [Azure PowerShell を使用してカスタム ロールを作成する](custom-roles-powershell.md)
- [Azure CLI を使用してカスタム ロールを作成する](custom-roles-cli.md)
- [ロール定義について](role-definitions.md)
