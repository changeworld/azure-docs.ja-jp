---
title: Azure RBAC のロール定義の概要 | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) のロール定義と、Azure のリソースの詳細なアクセス管理に合わせてカスタム ロールを定義する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161815"
---
# <a name="understand-role-definitions"></a>ロール定義について

ロールのしくみを理解しようとしている場合、または独自の[カスタム ロール](custom-roles.md)を作成している場合は、ロールの定義方法を理解すると便利です。 この記事では、ロール定義の詳細について説明し、いくつかの例を示します。

## <a name="role-definition-structure"></a>ロール定義の構造

*ロール定義*はアクセス許可のコレクションです。 単に*ロール*と呼ばれることもあります。 ロール定義には、実行できる操作 (読み取り、書き込み、削除など) が登録されています。 また、実行できない操作も登録できます。 ロール定義の構造を次に示します。

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

操作は、次の形式の文字列で指定します。

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

操作文字列の `{action}` 部分には、リソースの種類に対して実行できる操作の種類を指定します。 たとえば、`{action}` には次の部分文字列が表示されます。

| アクションの部分文字列    | [説明]         |
| ------------------- | ------------------- |
| `*` | ワイルドカード文字は、文字列と一致するすべての操作に対するアクセスを許可します。 |
| `read` | 読み取り操作 (GET) を有効にします。 |
| `write` | 書き込み操作 (PUT、POST、および PATCH) を有効にします。 |
| `delete` | 削除操作 (DELETE) を有効にします。 |

JSON 形式の[共同作成者](built-in-roles.md#contributor)ロール定義を次に示します。 `actions` 以下のワイルドカード (`*`) 操作は、このロールに割り当てられたプリンシパルがすべてのアクションを実行できること、つまりすべてを管理できることを示します。 これには、今後、Azure が新しいリソースの種類を追加するときに定義されるアクションも含まれます。 `notActions` 以下の操作は `actions` から引かれます。 [共同作成者](built-in-roles.md#contributor)ロールの場合、`notActions` は、リソースに対するアクセスを管理するこのロールの機能を削除し、リソースへのアクセスも割り当てます。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>管理操作

管理操作のロールベースのアクセス制御は、ロール定義の `actions` セクションと `notActions` セクションで指定されています。 Azure での管理操作の例をいくつか示します。

- ストレージ アカウントに対するアクセスを管理する
- BLOB コンテナーの作成、更新、または削除
- リソース グループとそのすべてのリソースを削除する

管理アクセスはデータに継承されません。 この分離により、ワイルドカード (`*`) を使用したロールはデータに無制限にアクセスできなくなります。 たとえば、ユーザーがサブスクリプションで[閲覧者](built-in-roles.md#reader)ロールを持っている場合、ストレージ アカウントは表示できますが、基になるデータを表示することはできません。

## <a name="actions"></a>アクション

`actions` アクセス許可は、ロールがアクセスを許可する管理操作を指定します。 このプロパティに文字列で指定された一連の操作によって、Azure リソース プロバイダーのセキュリティ保護可能な操作が識別されます。 `actions` で使用できる管理操作の例をいくつか示します。

| 操作文字列    | [説明]         |
| ------------------- | ------------------- |
| `*/read` | すべての Azure リソース プロバイダーの全リソースの種類を対象に読み取り操作のアクセス権を付与します。|
| `Microsoft.Compute/*` | Microsoft.Compute リソース プロバイダーのすべてのリソースの種類を対象にすべての操作のアクセス権を付与します。|
| `Microsoft.Network/*/read` | Microsoft.Network リソース プロバイダーの全リソース タイプを対象に読み取り操作のアクセス権を付与します。|
| `Microsoft.Compute/virtualMachines/*` | Virtual Machines とその子リソース タイプを対象にすべての操作のアクセス権を付与します。|
| `microsoft.web/sites/restart/Action` | Web アプリを再起動するためのアクセス権を付与します。|

## <a name="notactions"></a>notActions

`notActions` アクセス許可には、許可された `actions` から除外される管理操作を指定します。 制限対象の操作を除外する方が、許可する操作セットを容易に定義できる場合は、`notActions` アクセス許可を使用します。 ロール (有効なアクセス許可) によって付与されたアクセスは、`actions` 操作から `notActions` 操作を引くことによって計算されます。

> [!NOTE]
> `notActions` で特定の操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにその操作へのアクセス権を付与する別のロールを割り当てた場合、ユーザーはその操作の実行が許可されます。 `notActions` は拒否ルールとは異なり、特定の操作を除外する必要があるときに、許可の対象となる一連の操作を指定しやすくすることを目的としたものに過ぎません。
>

## <a name="assignablescopes"></a>assignableScopes

`assignableScopes` セクションでは、ロールを割り当て可能なスコープ (管理グループ (現在はプレビュー段階)、サブスクリプション、リソース グループ、またはリソース) を指定します。 そのロールを必要とするサブスクリプションやリソース グループのみに割り当てを限定し、それ以外のサブスクリプションやリソース グループについては元のユーザー エクスペリエンスを保ち、不要な混乱を避けることができます。 少なくとも 1 つの管理グループ、サブスクリプション、リソース グループ、またはリソース ID を使用する必要があります。

組み込みロールでは `assignableScopes` がルート スコープ (`"/"`) に設定されています。 ルート スコープは、すべてのスコープでそのロールを割り当て可能であることを示します。 作成したカスタム ロールには、ルート スコープを使用できません。 試すと、承認エラーが発生します。

有効な AssignableScopes の例を次に示します。

| シナリオ | 例 |
|----------|---------|
| 単一のサブスクリプションの割り当てにロールを使用できる | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| 2 つのサブスクリプションの割り当てにロールを使用できる | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| ネットワーク リソース グループでのみ割り当てにロールを使用できる | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| すべてのスコープの割り当てにロールを使用できる | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes とカスタム ロール

カスタム ロールの `assignableScopes` セクションでは、カスタム ロールを作成、削除、変更、または表示できるユーザーも制御されます。

| タスク | 操作 | [説明] |
| --- | --- | --- |
| カスタム ロールの作成/削除 | `Microsoft.Authorization/ roleDefinition/write` | カスタム ロールのすべての `assignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープで使用するカスタム ロールを作成 (または削除) できます。 たとえば、サブスクリプション、リソース グループ、リソースの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの修正 | `Microsoft.Authorization/ roleDefinition/write` | カスタム ロールのすべての `assignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープ内のカスタム ロールを変更できます。 たとえば、サブスクリプション、リソース グループ、リソースの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの表示 | `Microsoft.Authorization/ roleDefinition/read` | あるスコープでこの操作を許可されたユーザーは、そのスコープで割り当て可能なカスタム ロールを表示できます。 すべての組み込みロールでは、カスタム ロールを割り当てることができます。 |

## <a name="role-definition-examples"></a>ロール定義の例

次の例は、Azure CLI を使用して表示される、[閲覧者](built-in-roles.md#reader)ロール定義を示しています。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

次の例は、Azure PowerShell を使用して表示される、仮想マシンの監視と再起動を行うカスタム ロールを示しています。

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
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>関連項目

* [組み込みのロール](built-in-roles.md)
* [カスタム ロール](custom-roles.md)
* [Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)
