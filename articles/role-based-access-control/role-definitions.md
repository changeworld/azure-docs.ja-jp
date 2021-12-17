---
title: Azure ロールの定義について - Azure RBAC
description: Azure リソースの詳細なアクセス管理を行うための Azure ロールベースのアクセス制御 (RBAC) の Azure ロール定義について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/28/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: e60c9f2e7cf4c2f2ed19612ff8e01260a5f8fcb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055107"
---
# <a name="understand-azure-role-definitions"></a>Azure ロールの定義について

Azure ロールのしくみを理解しようとしている場合、または独自の [Azure カスタム ロール](custom-roles.md)を作成している場合は、ロールの定義方法を理解すると便利です。 この記事では、ロール定義の詳細について説明し、いくつかの例を示します。

## <a name="role-definition"></a>ロール定義

*ロール定義* はアクセス許可のコレクションです。 単に *ロール* と呼ばれることもあります。 ロール定義では、実行できるアクション (読み取り、書き込み、削除など) が一覧表示されます。 また、許可されるアクション、あるいは基となるデータに関連するアクションから除外されるアクションを一覧表示することもできます。

次に示すのは、Azure PowerShell を使用して表示される場合のロール定義のプロパティの例です。

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

次に示すのは、Azure portal、Azure CLI、または REST API を使用して表示される場合のロール定義のプロパティの例です

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

次の表で、ロールのプロパティについて説明します。

| プロパティ | 説明 |
| --- | --- |
| `Name`</br>`roleName` | ロールの表示名です。 |
| `Id`</br>`name` | ロールの一意の ID です。 組み込みロールは、クラウド全体で同じロール ID を持ちます。 |
| `IsCustom`</br>`roleType` | これがカスタム ロールであるかどうかを示します。 カスタム ロールの場合は `true` または `CustomRole` に設定します。 組み込みロールの場合は `false` または `BuiltInRole` に設定します。 |
| `Description`</br>`description` | ロールの説明です。 |
| `Actions`</br>`actions` | ロールで実行できるコントロール プレーン アクションを指定する文字列の配列。 |
| `NotActions`</br>`notActions` | 許可される `Actions` から除外されるコントロール プレーン アクションを指定する文字列の配列。 |
| `DataActions`</br>`dataActions` | 対象のオブジェクト内のデータに対して、ロールで実行できるコントロール プレーン アクションを指定する文字列の配列。 |
| `NotDataActions`</br>`notDataActions` | 許可される `DataActions` から除外されるデータ プレーン アクションを指定する文字列の配列。 |
| `AssignableScopes`</br>`assignableScopes` | 割り当てにロールを使用できるスコープを指定する文字列の配列。 |

### <a name="actions-format"></a>アクションの形式

アクションは、次の形式の文字列で指定されます。

- `{Company}.{ProviderName}/{resourceType}/{action}`

アクション文字列の `{action}` 部分では、リソースの種類に対して実行できるアクションの種類を指定します。 たとえば、`{action}` には次の部分文字列が表示されます。

| アクションの部分文字列    | 説明         |
| ------------------- | ------------------- |
| `*` | ワイルドカード文字では、文字列と一致するすべてのアクションに対するアクセスを許可します。 |
| `read` | 読み取りアクション (GET) を有効にします。 |
| `write` | 書き込みアクション (PUT または PATCH) を有効にします。 |
| `action` | 仮想マシンの再起動 (POST) などのカスタムアクションを有効にします。 |
| `delete` | 削除アクション (DELETE) を有効にします。 |

### <a name="role-definition-example"></a>ロールの定義の例

Azure PowerShell と Azure CLI に表示される[共同作成者](built-in-roles.md#contributor)ロールの定義を次に示します。 `Actions` 以下のワイルドカード (`*`) アクションは、このロールに割り当てられたプリンシパルがすべてのアクションを実行できること、つまり、すべてを管理できることを示します。 これには、今後、Azure が新しいリソースの種類を追加するときに定義されるアクションも含まれます。 `NotActions` 以下のアクションは、`Actions` から取り除かれます。 [共同作成者](built-in-roles.md#contributor)ロールの場合、`NotActions` は、リソースに対するアクセスを管理するこのロールの機能を削除し、Azure Blueprints の割り当ても管理します。

Azure PowerShell に表示される共同作成者ロール:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI に表示される共同作成者ロール:

```json
{
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
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="control-and-data-actions"></a>コントロールおよびデータ アクション

コントロール プレーン アクションのロールベースのアクセス制御は、ロール定義の `Actions` および `NotActions` プロパティで指定されます。 Azure でのコントロール プレーン アクションの例をいくつか以下に示します。

- ストレージ アカウントに対するアクセスを管理する
- BLOB コンテナーの作成、更新、または削除
- リソース グループとそのすべてのリソースを削除する

コンテナーの認証方法が、"アクセス キー" ではなく "Azure AD ユーザー アカウント" に設定されている場合、コントロール プレーン アクセスはデータ プレーンに継承されません。 この分離により、ワイルドカード (`*`) を使用したロールはデータに無制限にアクセスできなくなります。 たとえば、ユーザーがサブスクリプションで[閲覧者](built-in-roles.md#reader)ロールを持っている場合、ストレージ アカウントは表示できますが、既定では基になるデータを表示することはできません。

以前は、ロールベースのアクセス制御はデータ アクションには使用されませんでした。 データ アクションの承認は、リソース プロバイダーによって異なります。 コントロール プレーン アクションで使用されるのと同じロールベースのアクセス制御承認モデルは、データ プレーン アクションに拡張されました。

データ プレーン アクションをサポートするために、新しいデータ プロパティがロール定義に追加されました。 データ プレーン アクションは `DataActions` および `NotDataActions` プロパティで指定されます。 これらのデータ プロパティを追加することによって、コントロール プレーンとデータ プレーンの分離が維持されます。 このことによって、ワイルドカード (`*`) を含む現在のロール割り当てが突然データにアクセスする動作が防止されます。 `DataActions` および `NotDataActions` で指定できるデータ プレーン アクションをいくつか以下に示します。

- コンテナーの BLOB の一覧の読み取り
- コンテナーのストレージ BLOB の書き込み
- キュー内のメッセージの削除

以下は[ストレージ BLOB データ閲覧者](built-in-roles.md#storage-blob-data-reader)ロール定義であり、`Actions` および `DataActions` プロパティの両方のアクションが含まれています。 このロールでは、BLOB コンテナーおよび基になる BLOB データを読み取ることができます。

Azure PowerShell に表示される Storage BLOB データ リーダー ロール:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI に表示される Storage BLOB データ リーダー ロール:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

`DataActions` および `NotDataActions` プロパティに追加できるのは、データ プレーン アクションのみです。 リソース プロバイダーはどのアクションがデータ アクションかを特定します。そのためには `isDataAction` プロパティを `true` に設定します。 `isDataAction` が `true` であるアクションの一覧を表示する場合は、[リソース プロバイダーの操作](resource-provider-operations.md)に関するページを参照してください。 データ アクションがないロールの場合、ロール定義内に `DataActions` および `NotDataActions` プロパティを含める必要はありません。

すべてのコントロール プレーン API 呼び出しの承認は、Azure Resource Manager によって処理されます。 データ プレーン API 呼び出しの承認は、リソース プロバイダーまたは Azure Resource Manager のいずれかによって処理されます。

### <a name="data-actions-example"></a>データ アクションの例

コントロール プレーンおよびデータ プレーン アクションの動作の理解を深めるために、具体例を考えてみましょう。 Alice はサブスクリプション スコープで[所有者](built-in-roles.md#owner)ロールに割り当てられています。 Bob はストレージ アカウント スコープで[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールに割り当てられています。 この例を次の図に示します。

![ロールベースのアクセス制御が、コントロール プレーンおよびデータ プレーン アクションの両方をサポートするように拡張されている](./media/role-definitions/rbac-data-plane.png)

Alice の[所有者](built-in-roles.md#owner)ロールおよび Bob の[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールは次のアクションを実行できます。

所有者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

ストレージ BLOB データ共同作成者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Alice のサブスクリプション スコープにはワイルドカード (`*`) アクションがあるため、そのアクセス許可が継承され、すべてのコントロール プレーン アクションを実行できます。 Alice は、コンテナーの読み取り、書き込み、および削除を行うことができます。 しかし、Alice は追加の手順を行わずにデータ プレーン アクションを実行することはできません。 たとえば、既定では、Alice はコンテナー内の BLOB を読み取ることができません。 BLOB を読み取るには、Alice はストレージ アクセス キーを取得し、それを使用して BLOB にアクセスする必要があります。

Bob のアクセス許可は[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールで指定された `Actions` および `DataActions` のみに制限されます。 Bob は、ロールに基づいて、コントロール プレーンおよびデータ プレーン アクションの両方を実行できます。 たとえば、Bob は指定されたストレージ アカウントのコンテナーを読み取り、書き込み、および削除でき、また BLOB も読み取り、書き込み、および削除できます。

ストレージのコントロールおよびデータ プレーン セキュリティの詳細については、[Azure Storage のセキュリティ ガイド](../storage/blobs/security-recommendations.md)を参照してください。

### <a name="what-tools-support-using-azure-roles-for-data-actions"></a>データ アクションのために Azure ロールの使用がサポートされているツール

データ アクションを表示して操作するには、正しいバージョンのツールまたは SDK が必要です。

| ツール  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 以降 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 以降 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0 プレビュー以降 |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 以降 |
| [Azure for Java](/java/azure/) | 1.9.0 以降 |
| [Azure for Python](/azure/python/) | 0.40.0 以降 |
| [Azure SDK for Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 以降 |

REST API でデータ アクションを表示して使用するには、次のバージョン以降に **api-version** パラメーターを設定する必要があります。

- 2018-07-01

## <a name="actions"></a>Actions

`Actions` アクセス許可では、ロールで実行できるコントロール プレーン アクションを指定します。 これは、Azure リソース プロバイダーのセキュリティ保護可能なアクションを特定する文字列のコレクションです。 `Actions` で使用できるコントロール プレーン アクションの例をいくつか以下に示します。

> [!div class="mx-tableFixed"]
> | アクションの文字列    | 説明         |
> | ------------------- | ------------------- |
> | `*/read` | すべての Azure リソース プロバイダーのすべてのリソースの種類を対象に、読み取りアクションのアクセス権を付与します。|
> | `Microsoft.Compute/*` | Microsoft.Compute リソース プロバイダーのすべてのリソースの種類を対象に、すべてのアクションのアクセス権を付与します。|
> | `Microsoft.Network/*/read` | Microsoft.Network リソース プロバイダーのすべてのリソースの種類を対象に、読み取りアクションのアクセス権を付与します。|
> | `Microsoft.Compute/virtualMachines/*` | 仮想マシンとその子リソースの種類を対象に、すべてのアクションのアクセス権を付与します。|
> | `microsoft.web/sites/restart/Action` | Web アプリを再起動するためのアクセス権を付与します。|

## <a name="notactions"></a>NotActions

`NotActions` アクセス許可では、許可される `Actions` (ワイルドカード (`*`) を使用) から取り除く (除外する) コントロール プレーン アクションを指定します。 `Actions` (ワイルドカード (`*`) を使用) から取り除く方が、許可するアクションのセットを容易に定義できる場合は、`NotActions` アクセス許可を使用します。 ロール (有効なアクセス許可) によって付与されたアクセス権は、`Actions` アクションから `NotActions` アクションを取り除くことによって計算されます。

`Actions - NotActions = Effective control plane permissions`

次の表に、[Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement) ワイルドカード アクションの有効なコントロール プレーン アクセス許可の例を 2 つ示します。

> [!div class="mx-tableFixed"]
> | Actions | NotActions | 有効なコントロール プレーンのアクセス許可 |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | "*なし*" | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> `NotActions` でアクションを除外するロールをユーザーに割り当てたうえで、同じアクションへのアクセス権を付与する 2 番目のロールを割り当てた場合、ユーザーはそのアクションの実行が許可されます。 `NotActions` は拒否ルールとは異なり、特定のアクションを除外する必要があるときに、許可されるアクションのセットを作成するのに便利な方法にすぎません。
>

### <a name="differences-between-notactions-and-deny-assignments"></a>NotActions と拒否の割り当ての違い

`NotActions` と拒否の割り当ては同じではなく、別の目的に使用されます。 `NotActions` は、ワイルドカード (`*`) アクションから特定のアクションを取り除くための便利な方法です。

拒否割り当てでは、ロールの割り当てでアクセスを許可されている場合であっても、指定したアクションがユーザーによって実行されるのをブロックします。 詳細については、[Azure 拒否割り当ての概要](deny-assignments.md)に関するページを参照してください。

## <a name="dataactions"></a>DataActions

`DataActions` アクセス許可では、対象のオブジェクト内のデータに対して、ロールで実行できるデータ プレーン アクションを指定します。 たとえば、ユーザーがあるストレージ アカウントへの BLOB データの読み取りアクセス許可を持っている場合、そのユーザーはそのストレージ アカウント内の BLOB を読み取ることができます。 `DataActions` で使用できるデータ アクションの例をいくつか以下に示します。

> [!div class="mx-tableFixed"]
> | データ アクションの文字列    | 説明         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | BLOB または BLOB の一覧を返します。 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | BLOB の書き込みの結果を返します。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | メッセージを返します。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | メッセージまたはメッセージの書き込みまたは削除の結果を返します。 |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions` アクセス許可では、許可される `DataActions` (ワイルドカード (`*`) を使用) から取り除かれる (除外される) データ プレーン アクションを指定します。 `DataActions` (ワイルドカード (`*`) を使用) から取り除く方が、許可するアクションのセットを容易に定義できる場合は、`NotDataActions` アクセス許可を使用します。 ロール (有効なアクセス許可) によって付与されたアクセス権は、`DataActions` アクションから `NotDataActions` アクションを取り除くことによって計算されます。 各リソース プロバイダーはそれぞれの API セットを提供し、データ アクションを実行します。

`DataActions - NotDataActions = Effective data plane permissions`

次の表に、[Microsoft.Storage](resource-provider-operations.md#microsoftstorage) ワイルドカード アクションの有効なデータ プレーン アクセス許可の例を 2 つ示します。

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | 有効なデータ プレーンのアクセス許可 |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | "*なし*" | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> `NotDataActions` でデータ アクションを除外するロールをユーザーに割り当てたうえで、同じデータ アクションへのアクセス権を付与する 2 番目のロールを割り当てた場合、ユーザーはそのデータ アクションの実行が許可されます。 `NotDataActions` は拒否ルールとは異なり、特定のデータ アクションを除外する必要があるときに、許可されるデータ アクションのセットを作成するのに便利な方法にすぎません。
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes` プロパティでは、このロール定義を割り当てることができるスコープ (管理グループ、サブスクリプション、またはリソース グループ) を指定します。 ロールを必要とする管理グループ、サブスクリプション、またはリソース グループのみで、その割り当てを利用できるようにすることができます。 少なくとも 1 つの管理グループ、サブスクリプション、またはリソース グループを使用する必要があります。

組み込みロールでは `AssignableScopes` がルート スコープ (`"/"`) に設定されています。 ルート スコープは、すべてのスコープでそのロールを割り当て可能であることを示します。 有効な AssignableScopes の例を次に示します。

> [!div class="mx-tableFixed"]
> | 割り当てにロールを使用できる | 例 |
> |----------|---------|
> | 1 つのサブスクリプション | `"/subscriptions/{subscriptionId1}"` |
> | 2 つのサブスクリプション | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Network resource group (ネットワーク リソース グループ) | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | 1 つの管理グループ | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | 管理グループとサブスクリプション | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | すべてのスコープ (組み込みロールにのみ適用) | `"/"` |

カスタム ロールの `AssignableScopes` の詳細については、[Azure カスタム ロール](custom-roles.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure 組み込みロール](built-in-roles.md)
* [Azure カスタム ロール](custom-roles.md)
* [Azure リソース プロバイダーの操作](resource-provider-operations.md)
