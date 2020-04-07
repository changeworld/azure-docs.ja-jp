---
title: Azure リソースのカスタム ロール | Microsoft Docs
description: Azure リソースの詳細なアクセス管理を行うために、ロールベースのアクセス制御 (RBAC) を使用してカスタム ロールを作成する方法について説明します。
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062165"
---
# <a name="custom-roles-for-azure-resources"></a>Azure リソースのカスタム ロール

> [!IMPORTANT]
> `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure リソースの組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 組み込みロールと同様に、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、管理グループ、サブスクリプション、およびリソース グループのスコープで割り当てることができます。

カスタム ロールは、同じ Azure AD ディレクトリを信頼するサブスクリプション間で共有できます。 カスタム ロールの数は、ディレクトリあたり **5,000** 個という制限があります (Azure Germany と Azure China 21Vianet の場合、制限は 2,000 カスタム ロールです)。カスタム ロールは、Azure portal (プレビュー)、Azure PowerShell、Azure CLI、または REST API を使用して作成できます。

## <a name="custom-role-example"></a>カスタム ロールの例

JSON 形式で表示されるカスタム ロールの例を次に示します。 このカスタム ロールは、仮想マシンの監視と再起動に使用できます。

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

カスタム ロールを作成すると、Azure portal にオレンジ色のリソース アイコンと共に表示されます。

![カスタム ロールのアイコン](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>カスタム ロールの作成手順

1. カスタム ロールを作成する方法を決定する

    カスタム ロールは、[Azure portal](custom-roles-portal.md) (プレビュー)、[Azure PowerShell](custom-roles-powershell.md)、[Azure CLI](custom-roles-cli.md)、または [REST API](custom-roles-rest.md) を使用して作成できます。

1. 必要なアクセス許可を決定する

    カスタム ロールを作成する場合、アクセス許可を定義するために使用可能なリソース プロバイダーの操作を把握しておく必要があります。 操作の一覧を確認するには、「[Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)」を参照してください。 操作を[ロール定義](role-definitions.md)の `Actions` または `NotActions` プロパティに追加します。 データ操作をする場合は、それらを `DataActions` または `NotDataActions` プロパティに追加します。

1. カスタム ロールを作成する

    通常は、まず、既存の組み込みロールを使用し、必要に応じてそれを変更します。 次に、[New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) または [az role definition create](/cli/azure/role/definition#az-role-definition-create) コマンドを使用して、カスタム ロールを作成します。 カスタム ロールを作成するには、[所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、すべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` アクセス許可が必要になります。

1. カスタム ロールをテストする

    カスタム ロールを作成したら、それをテストして期待どおりに動作することを確認する必要があります。 後で調整する必要がある場合は、カスタム ロールを更新できます。

カスタム ロールの作成方法に関するステップバイステップのチュートリアルが必要な場合は、「[チュートリアル:Azure PowerShell を使用してカスタム ロールを作成する」か「チュートリアル:](tutorial-custom-role-powershell.md)」か「[チュートリアル:Azure CLI を使用してカスタム ロールを作成する](tutorial-custom-role-cli.md)」を参照してください。

## <a name="custom-role-properties"></a>カスタム ロールのプロパティ

カスタム ロールには、次のプロパティがあります。

| プロパティ | 必須 | Type | 説明 |
| --- | --- | --- | --- |
| `Name` | はい | String | カスタム ロールの表示名。 ロールの定義は、管理グループまたはサブスクリプション レベルのリソースですが、同じ Azure AD ディレクトリを共有する複数のサブスクリプションで使用できます。 この表示名は、Azure AD ディレクトリ範囲で一意である必要があります。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 128 文字です。 |
| `Id` | はい | String | カスタム ロールの一意の ID。 Azure PowerShell と Azure CLI では、新しいロールを作成するときに自動的にこの ID が生成されます。 |
| `IsCustom` | はい | String | これがカスタム ロールであるかどうかを示します。 カスタム ロールの場合は `true` に設定します。 |
| `Description` | はい | String | カスタム ロールの説明。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 1024 文字です。 |
| `Actions` | はい | String[] | ロールで実行できる管理操作を指定する文字列の配列。 詳細については、「[Actions](role-definitions.md#actions)」を参照してください。 |
| `NotActions` | いいえ | String[] | 許可された `Actions` から除外される管理操作を指定する文字列の配列。 詳細については、「[notActions](role-definitions.md#notactions)」を参照してください。 |
| `DataActions` | いいえ | String[] | 対象のオブジェクト内のデータに対して、ロールで実行できるデータ操作を指定する文字列の配列。 `DataActions` が含まれるカスタム ロールを作成する場合、そのロールは管理グループのスコープで割り当てることができません。 詳細については、「[DataActions](role-definitions.md#dataactions)」を参照してください。 |
| `NotDataActions` | いいえ | String[] | 許可された `DataActions` から除外されるデータ操作を指定する文字列の配列。 詳細については、「[NotDataActions](role-definitions.md#notdataactions)」を参照してください。 |
| `AssignableScopes` | はい | String[] | 割り当てにカスタム ロールを使用できるスコープを指定する文字列の配列。 カスタム ロールの `AssignableScopes` に定義できる管理グループは 1 つだけです。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。 詳細については、「[AssignableScopes](role-definitions.md#assignablescopes)」を参照してください。 |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>カスタム ロールを作成、削除、更新、または表示できるユーザー

組み込みロールと同じように、`AssignableScopes` プロパティでは、割り当てにロールを使用できるスコープを指定します。 カスタム ロールの `AssignableScopes` プロパティでは、カスタム ロールを作成、削除、更新、または表示できるユーザーも制御されます。

| タスク | Operation | 説明 |
| --- | --- | --- |
| カスタム ロールの作成/削除 | `Microsoft.Authorization/ roleDefinitions/write` | カスタム ロールのすべての `AssignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープで使用するカスタム ロールを作成 (または削除) できます。 たとえば、管理グループ、サブスクリプション、リソース グループの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの更新 | `Microsoft.Authorization/ roleDefinitions/write` | カスタム ロールのすべての `AssignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープ内のカスタム ロールを更新できます。 たとえば、管理グループ、サブスクリプション、リソース グループの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの表示 | `Microsoft.Authorization/ roleDefinitions/read` | あるスコープでこの操作を許可されたユーザーは、そのスコープで割り当て可能なカスタム ロールを表示できます。 すべての組み込みロールでは、カスタム ロールを割り当てることができます。 |

## <a name="custom-role-limits"></a>カスタム ロールの制限事項

次の一覧では、カスタム ロールの制限事項について説明します。

- 各ディレクトリは、最大 **5,000** 個のカスタム ロールを持つことができます
- Azure Germany と Azure China 21Vianet では、各ディレクトリに対して最大 2,000 のカスタム ロールを持つことができます。
- ルート スコープ (`"/"`) には `AssignableScopes` を設定できません。
- カスタム ロールの `AssignableScopes` に定義できる管理グループは 1 つだけです。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
- `DataActions` が含まれるカスタム ロールを管理グループのスコープで割り当てることはできません。
- ロールの定義の割り当て可能なスコープに管理グループが存在するかどうかは、Azure Resource Manager では確認されません。

カスタム ロールと管理グループの詳細については、「[Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [Azure portal を使用して Azure カスタム ロールを作成または更新する (プレビュー)](custom-roles-portal.md)
- [Azure リソースのロール定義の概要](role-definitions.md)
- [Azure リソースの RBAC のトラブルシューティング](troubleshooting.md)
