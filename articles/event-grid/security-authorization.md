---
title: Azure Event Grid のセキュリティと認証
description: Azure Event Grid とその概念について説明します。
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601042"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Event Grid リソースへのアクセスの承認
Azure Event Grid を使用すると、イベント サブスクリプションの一覧表示、新しいサブスクリプションの作成、キーの生成など、多様な **管理操作** を実行する各ユーザーに付与されるアクセス レベルを制御できます。 Event Grid では、Azure ロールベースのアクセス制御 (Azure RBAC) が使用されます。

> [!NOTE]
> Event Grid トピックまたはドメインへのイベントの発行については、Event Grid は Azure RBAC をサポートしません。 Shared Access Signature (SAS) キーまたはトークンを使用して、イベントを発行するクライアントを認証します。 詳細については、[発行クライアントの認証](security-authenticate-publishing-clients.md)に関する記事を参照してください。 

## <a name="operation-types"></a>操作の種類
Azure Event Grid でサポートされている操作の一覧を表示するには、次の Azure CLI コマンドを実行します。 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

次の操作は秘密情報を返す可能性がありますが、これは通常の読み取り操作からはフィルターで除外されます。 このような操作へのアクセスは制限することをお勧めします。 

* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action


## <a name="built-in-roles"></a>組み込みのロール
Event Grid には、次の 3 つの組み込みのロールが用意されています。 

Event Grid サブスクリプション閲覧者と Event Grid サブスクリプションの共同作成者のロールは、イベント サブスクリプションを管理するためのものです。 これらは、イベント ドメイン内のトピックをサブスクライブするために必要なアクセス許可をユーザーに提供するため、[イベント ドメイン](event-domains.md)を実装するときに重要です。 これらのロールはイベント サブスクリプションだけを対象としたものであり、トピックの作成などのアクションに対するアクセス権は付与されません。

Event Grid の共同作成者ロールを使用すると、Event Grid リソースを作成および管理できます。 


| Role | 説明 |
| ---- | ----------- | 
| [Event Grid サブスクリプション閲覧者](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Event Grid のイベント サブスクリプション操作を管理できます。 |
| [Event Grid サブスクリプションの共同作成者](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Event Grid のイベント サブスクリプションを読み取ることができます。 |
| [Event Grid の共同作成者](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Event Grid リソースの作成と管理を行うことができます。 |


> [!NOTE]
> ロールに関する詳細情報を提供する記事に移動するには、最初の列にあるリンクを選択します。 RBAC ロールにユーザーまたはグループを割り当てる方法については、[こちらの記事](../role-based-access-control/quickstart-assign-role-user-portal.md)を参照してください。


## <a name="custom-roles"></a>カスタム ロール

組み込みロールとは異なるアクセス許可を指定する必要がある場合は、カスタム ロールを作成できます。

さまざまなアクションの実行をユーザーに許可する Event Grid ロール定義の例を以下に示します。 これらのカスタム ロールは、イベント サブスクリプションより広範囲のアクセス権を付与するため、組み込みロールとは異なります。

**EventGridReadOnlyRole.json**:読み取り専用操作のみを許可します。

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**:制限付きの投稿アクションを許可しますが、削除アクションは禁止します。

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**:すべての Event Grid アクションを許可します。

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

カスタム ロールは、[PowerShell](../role-based-access-control/custom-roles-powershell.md)、[Azure CLI](../role-based-access-control/custom-roles-cli.md)、[REST API](../role-based-access-control/custom-roles-rest.md) で作成できます。



### <a name="encryption-at-rest"></a>保存時の暗号化

Event Grid サービスによってディスクに書き込まれるすべてのイベントまたはデータは、保存時に確実に暗号化されるように、Microsoft マネージド キーによって暗号化されます。 また、[Event Grid の再試行ポリシー](delivery-and-retry.md)に従って、イベントまたはデータが保持される最大期間は 24 時間です。 Event Grid では、24時間またはイベントの Time-To-Live のいずれか少ない方が経過すると、すべてのイベントまたはデータが自動的に削除されます。

## <a name="permissions-for-event-subscriptions"></a>イベント サブスクリプション用のアクセス許可
WebHook ではないイベント ハンドラー (イベント ハブ、キュー ストレージなど) を使用している場合は、そのリソースへの書き込みアクセスが必要です。 このアクセス許可のチェックにより、未認証のユーザーはリソースにイベントを送信できなくなります。

イベント ソースであるリソースに対する **Microsoft.EventGrid/EventSubscriptions/Write** アクセス許可を持っている必要があります。 リソースのスコープで新しいサブスクリプションを作成するため、このアクセス許可が必要です。 必要なリソースは、サブスクライブしているのがシステム トピックかカスタム トピックかによって異なります。 ここでは、この 2 つの種類について説明します。

### <a name="system-topics-azure-service-publishers"></a>システム トピック (Azure サービスの発行元)
システム トピックで、ソース リソースの所有者または共同作成者ではない場合は、イベントを発行するリソースのスコープで新しいイベント サブスクリプションを作成するためのアクセス許可が必要です。 リソースの形式は次のとおりです。`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

たとえば、**myacct** というストレージ アカウントでイベントにサブスクライブするには、Microsoft.EventGrid/EventSubscriptions/Write アクセス許可が必要です。`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>カスタム トピック
カスタム トピックの場合、Event Grid トピックのスコープに新しいイベント サブスクリプションを書き込むアクセス許可が必要です。 リソースの形式は次のとおりです。`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

たとえば、**mytopic** というカスタム トピックにサブスクライブするには、Microsoft.EventGrid/EventSubscriptions/Write アクセス許可が必要です。`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
