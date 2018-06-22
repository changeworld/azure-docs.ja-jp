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
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9bb7808f2b483fe9cd7d22c6df3fe80d4a98f1f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266858"
---
# <a name="understand-role-definitions"></a>ロール定義について

ロールのしくみを理解しようとしている場合、または独自の[カスタム ロール](custom-roles.md)を作成している場合は、ロールの定義方法を理解すると便利です。 この記事では、ロール定義の詳細について説明し、いくつかの例を示します。

## <a name="role-definition-structure"></a>ロール定義の構造

*ロール定義*はアクセス許可のコレクションです。 単に*ロール*と呼ばれることもあります。 ロール定義には、実行できる操作 (読み取り、書き込み、削除など) が登録されています。 実行できない操作または基となるデータに関連する操作も一覧表示できます。 ロール定義の構造を次に示します。

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
roleName
roleType
type
```

操作は、次の形式の文字列で指定します。

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

操作文字列の `{action}` 部分には、リソースの種類に対して実行できる操作の種類を指定します。 たとえば、`{action}` には次の部分文字列が表示されます。

| アクションの部分文字列    | 説明         |
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
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>管理操作とデータ操作 (プレビュー)

管理操作のロールベースのアクセス制御は、ロール定義の `actions` セクションと `notActions` セクションで指定されています。 Azure での管理操作の例をいくつか示します。

- ストレージ アカウントに対するアクセスを管理する
- BLOB コンテナーの作成、更新、または削除
- リソース グループとそのすべてのリソースを削除する

管理アクセスはデータに継承されません。 この分離により、ワイルドカード (`*`) を使用したロールはデータに無制限にアクセスできなくなります。 たとえば、ユーザーがサブスクリプションで[閲覧者](built-in-roles.md#reader)ロールを持っている場合、ストレージ アカウントは表示できますが、既定では基になるデータを表示することはできません。

以前は、ロールベースのアクセス制御はデータ操作には使用されませんでした。 データ操作のアクセス許可はリソース プロバイダーによって異なります。 管理操作に使用する同じロールベースのアクセス制御許可モデルがデータ操作に拡張されました (現在プレビュー中)。

データ操作をサポートするために、新しいデータ セクションがロール定義構造体に追加されました。 データ操作は `dataActions` セクションおよび `notDataActions` セクションで指定されます。 これらのデータ セクションを追加することによって、管理とデータの分離が維持されます。 このことによって、ワイルドカード (`*`) を含む現在のロール割り当てが突然データにアクセスする動作が防止されます。 `dataActions` および `notDataActions` で指定できるデータ操作の一部を次に示します。

- コンテナーの BLOB の一覧の読み取り
- コンテナーのストレージ BLOB の書き込み
- キュー内のメッセージの削除

以下は、[ストレージ BLOB データ閲覧者 (プレビュー)](built-in-roles.md#storage-blob-data-reader-preview) ロール定義で、`actions` セクションと `dataActions` セクション両方の操作が含まれています。 このロールでは、BLOB コンテナーおよび基になる BLOB データを読み取ることができます。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

`dataActions` セクションと `notDataActions` セクションに追加できるのはデータ操作のみです。 リソース プロバイダーはどの操作がデータ操作かを指定します。そのためには `isDataAction` プロパティを `true` に設定します。 `isDataAction` が `true` である操作の一覧を確認するには、「[リソース プロバイダー操作](resource-provider-operations.md)」を参照してください。 データ操作のないロールは、ロール定義内に `dataActions` セクションと `notDataActions` セクションを含める必要はありません。

すべての管理操作 API 呼び出しの許可は Azure Resource Manager によって処理されます。 データ操作 API 呼び出しの許可はリソース プロバイダーまたは Azure Resource Manager のいずれかによって処理されます。

### <a name="data-operations-example"></a>データ操作例

管理操作とデータ操作の動作方法の理解を深めるために、具体例を考えてみましょう。 Alice はサブスクリプション スコープで[所有者](built-in-roles.md#owner)ロールに割り当てられています。 Bob はストレージ アカウント スコープで[ストレージ BLOB データ共同作成者 (プレビュー)](built-in-roles.md#storage-blob-data-contributor-preview) ロールに割り当てられています。 この例を次の図に示します。

![ロールベースのアクセス制御が管理操作とデータ操作の両方をサポートするように拡張されます](./media/role-definitions/rbac-management-data.png)

Alice の[所有者](built-in-roles.md#owner)ロールおよび Bob の[ストレージ BLOB データ共同作成者 (プレビュー)](built-in-roles.md#storage-blob-data-contributor-preview) ロールは次のアクションを実行できます。

Owner

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

ストレージ BLOB データ共同作成者 (プレビュー)

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Alice にはサブスクリプション スコープにワイルドカード (`*`) アクションがあるため、Alice のアクセス許可は継承され、すべての管理アクションを実行できます。 ただし、Alice はデータ操作を実行することはできません。 たとえば既定では、Alice はコンテナー内の BLOB を読み取ることはできませんが、コンテナーを読み取り、書き込み、および削除することは可能です。

Bob のアクセス許可は[ストレージ BLOB データ共同作成者 (プレビュー)](built-in-roles.md#storage-blob-data-contributor-preview) ロールで指定された `actions` および `dataActions` のみに制限されます。 Bob はロールに基づいて、管理操作とデータ操作の両方を実行できます。 たとえば、Bob は指定されたストレージ アカウントのコンテナーを読み取り、書き込み、および削除でき、また BLOB も読み取り、書き込み、および削除できます。

### <a name="what-tools-support-using-rbac-for-data-operations"></a>RBAC を使用してデータ操作をサポートするツール

データ操作を表示し、操作するには、正しいバージョンのツールまたは SDK が必要です。

| ツール  | バージョン  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 以降 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 以降 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0 プレビュー以降 |
| [Azure SDK for Go](/go/azure/azure-sdk-go-install) | 15.0.0 以降 |
| [Azure for Java](/java/azure/) | 1.9.0 以降 |
| [Azure for Python](/python/azure) | 0.40.0 以降 |
| [Azure SDK for Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 以降 |

## <a name="actions"></a>アクション

`actions` アクセス許可は、ロールがアクセスを許可する管理操作を指定します。 このプロパティに文字列で指定された一連の操作によって、Azure リソース プロバイダーのセキュリティ保護可能な操作が識別されます。 `actions` で使用できる管理操作の例をいくつか示します。

| 操作文字列    | 説明         |
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

## <a name="dataactions-preview"></a>dataActions (プレビュー)

`dataActions` アクセス許可では、そのオブジェクト内のデータへのアクセスがロールによって付与されるデータ操作が指定されます。 たとえば、ユーザーがあるストレージ アカウントへの BLOB データの読み取りアクセス許可を持っている場合、そのユーザーはそのストレージ アカウント内の BLOB を読み取ることができます。 次に `dataActions` で使用できるデータ操作の例を示します。

| 操作文字列    | 説明         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | BLOB または BLOB の一覧を返します。 |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | BLOB の書き込みの結果を返します。 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | メッセージを返します。 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | メッセージまたはメッセージの書き込みまたは削除の結果を返します。 |

## <a name="notdataactions-preview"></a>notDataActions (プレビュー)

`notDataActions` アクセス許可では、許可された `dataActions` から除外されるデータ操作を指定します。 ロール (有効なアクセス許可) によって付与されたアクセスは、`dataActions` 操作から `notDataActions` 操作を引くことによって計算されます。 各リソース プロバイダーは、それぞれの API セットを提供し、データ操作をサポートします。

> [!NOTE]
> `notDataActions` であるデータ操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにそのデータ操作へのアクセス権を付与する別のロールを割り当てた場合、ユーザーはそのデータ操作の実行が許可されます。 `notDataActions` は拒否ルールとは異なり、特定のデータ操作を除外する必要があるときに、許可の対象となる一連のデータ操作を指定しやすくすることを目的としたものに過ぎません。
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

| タスク | 操作 | 説明 |
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
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

## <a name="see-also"></a>関連項目

* [組み込みのロール](built-in-roles.md)
* [カスタム ロール](custom-roles.md)
* [Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)
