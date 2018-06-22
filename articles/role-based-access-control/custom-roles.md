---
title: Azure RBAC のカスタム ロールの作成 | Microsoft Docs
description: Azure サブスクリプションのきめ細かい ID 管理を実現するために Azure のロールベースのアクセス制御でカスタム ロールを定義する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3baf616e448f1f6d5292161ae125502d72141940
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266596"
---
# <a name="create-custom-roles-in-azure"></a>Azure でカスタム ロールを作成する

[組み込みロール](built-in-roles.md)が特定のアクセス ニーズを満たしていない場合、独自のカスタム ロールを作成することができます。 組み込みロールと同様、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、サブスクリプション、リソース グループ、リソースのスコープで割り当てることができます。 カスタム ロールは Azure Active Directory (Azure AD) テナントに保存され、サブスクリプション間で共有することができます。 各テナントは、最大 2,000 個のカスタム ロールを持つことができます。 カスタム ロールは、Azure PowerShell、Azure CLI、REST API で作成することができます。

この記事では、PowerShell と Azure CLI を使用して、カスタム ロールの作成を開始する方法の例について説明します。

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>サポート要求を開くためのカスタム ロールを PowerShell を使用して作成する

カスタム ロールを作成するには、組み込みのロールから始めて、そのロールを編集して新しいロールを作成します。 この例では、組み込みの[閲覧者](built-in-roles.md#reader)ロールをカスタマイズして、"Reader support tickets access level" というカスタム ロールを作成します。 このロールが割り当てられたユーザーは、サブスクリプション内のすべてを閲覧し、サポート要求を開くこともできます。

> [!NOTE]
> ユーザーがサポート要求を開くことを許可できる組み込みロールは、[所有者](built-in-roles.md#owner)と[共同作成者](built-in-roles.md#contributor)の 2 つのみです。 ユーザーがサポート要求を開けるようにするには、そのユーザーにサブスクリプション スコープを指定してロールを割り当てる必要があります。これは、すべてのサポート要求が Azure サブスクリプションに基づき作成されるためです。

PowerShell では、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドを使用して、[閲覧者](built-in-roles.md#reader)ロールを JSON 形式でエクスポートします。

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

[閲覧者](built-in-roles.md#reader)ロールの JSON 出力を次に示します。 一般的なロールは 3 つの主要なセクション、`Actions`、`NotActions`、`AssignableScopes` で構成されています。 `Actions`セクションには、このロールに許可されているすべての操作が一覧表示されています。 `Actions` から操作を除外するには、`NotActions` に追加します。 有効なアクセス許可は、`NotActions` 操作を `Actions` 操作から減算して計算されます。

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

次に、JSON 出力を編集してカスタム ロールを作成します。 この場合、サポート チケットを作成できるようにするには、`Microsoft.Support/*` 操作を追加する必要があります。 各操作は、リソース プロバイダーから提供されています。 リソース プロバイダーの操作一覧を取得するには、[Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) コマンドを使用するか、[Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)を参照してください。

ロールには、ロールが使用されるサブスクリプションの明示的なサブスクリプション ID が含まれていなければなりません。 サブスクリプション ID は `AssignableScopes` に一覧表示されています。記載されていない場合は、ロールをサブスクリプションにインポートすることができません。

最後に、これがカスタム ロールであることを指定するために、`IsCustom` プロパティを `true` に設定する必要があります。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

新しいカスタム ロールを作成するには、[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) コマンドを使用して、更新された JSON ロール定義ファイルを指定します。

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) を実行した後、新しいカスタム ロールは Azure Portal で使用できるようになり、ユーザーに割り当てることができます。

![Azure Portal にインポートされたカスタム ロールのスクリーンショット](./media/custom-roles/18.png)

![同じディレクトリのユーザーに対するインポート済みカスタム ロールの割り当てのスクリーンショット](./media/custom-roles/19.png)

![インポート済みカスタム ロールのアクセス許可のスクリーンショット](./media/custom-roles/20.png)

このカスタム ロールを持つユーザーは、新しいサポート要求を作成できます。

![サポート要求を作成するカスタム ロールのスクリーンショット](./media/custom-roles/21.png)

このカスタム ロールを持つユーザーは、VM の作成やリソース グループの作成など、他のアクションを実行できません。

![VM を作成できないカスタム ロールのスクリーンショット](./media/custom-roles/22.png)

![新しい RG を作成できないカスタム ロールのスクリーンショット](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>サポート要求を開くためのカスタム ロールを Azure CLI を使用して作成する

Azure CLI を使用してカスタム ロールを作成する手順は、JSON 出力が異なる点を除き、PowerShell を使用する場合と似ています。

この例では、組み込みの[閲覧者](built-in-roles.md#reader)ロールから始めることができます。 [閲覧者](built-in-roles.md#reader)ロールのアクションを一覧表示するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) コマンドを使用します。

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

次の形式の JSON ファイルを作成します。 `Microsoft.Support/*` 操作が `Actions` セクションに追加されたので、このユーザーは引き続き閲覧者でありながら、サポート要求を開くことができます。 このロールが使用されるサブスクリプションの ID を `AssignableScopes` セクションに追加する必要があります。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

新しいカスタム ロールを作成するには、[az role definition create](/cli/azure/role/definition#az_role_definition_create) コマンドを使用します。

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

新しいカスタム ロールが Azure Portal で使用できるようになりました。このロールを使用するプロセスは、前述の PowerShell セクションと同じです。

![Azure Portal のスクリーンショット: CLI 1.0 を使用して作成されたカスタム ロール](./media/custom-roles/26.png)


## <a name="see-also"></a>関連項目
- [ロール定義について](role-definitions.md)
- [Azure PowerShell を使用したロールベースのアクセス制御の管理](role-assignments-powershell.md)
- [Azure CLI を使用したロールベースのアクセス制御の管理](role-assignments-cli.md)
- [REST API を使用してロールベースのアクセス制御を管理する](role-assignments-rest.md)
