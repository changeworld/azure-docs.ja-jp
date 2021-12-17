---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 06/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce6451bb1293f9cf3d85ff2e92cf6a5f8f42ba52
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220609"
---
Event Hubs では、次のカテゴリの診断ログをキャプチャします。

| カテゴリ | 説明 | 
| -------- | ----------- | 
| アーカイブ ログ | [Event Hubs Capture](../event-hubs-capture-overview.md) 操作、特にログ関連のキャプチャ エラーに関する情報をキャプチャします。 |
| 操作ログ | Azure Event Hubs 名前空間上で実行されるすべての管理操作がキャプチャされます。 Azure Event Hubs で実行される大量のデータ操作のため、データ操作はキャプチャされません。 |
| 自動スケール ログ | Event Hubs 名前空間で実行される自動拡張操作をキャプチャします。 |
| Kafka コーディネーター ログ | Event Hubs に関連する Kafka コーディネーター操作をキャプチャします。 |
| Kafka ユーザー エラー ログ | Event Hubs で呼び出される Kafka API に関する情報をキャプチャします。 |
| Event Hubs 仮想ネットワーク (VNet) 接続イベント | Event Hubs にトラフィックを送信する IP アドレスと仮想ネットワークに関する情報をキャプチャします。 |
| カスタマー マネージド キーのユーザー ログ | カスタマー マネージド キーに関連する操作をキャプチャします。 |


すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下のセクションで説明している形式を使用する文字列フィールドがあります。

### <a name="archive-logs-schema"></a>アーカイブ ログ スキーマ

アーカイブ ログの JSON 文字列には、次の表に示す要素が含まれます。

名前 | 説明
------- | -------
`TaskName` | 失敗したタスクの説明
`ActivityId` | 内部 ID。追跡目的で使用されます
`trackingId` | 内部 ID。追跡目的で使用されます
`resourceId` | Azure Resource Manager リソース ID
`eventHub` | イベント ハブの完全名 (名前空間の名前を含みます)
`partitionId` | 書き込み先のイベント ハブ パーティション
`archiveStep` | 使用可能な値:ArchiveFlushWriter、DestinationInit
`startTime` | 障害開始時刻
`failures` | 障害が発生した回数
`durationInSeconds` | 障害の時間
`message` | エラー メッセージ
`category` | ArchiveLogs

次のコードは、アーカイブ ログの JSON 文字列の例です。

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>操作ログのスキーマ

操作ログの JSON 文字列には、次の表に示す要素が含まれます。

名前 | 説明
------- | -------
`ActivityId` | 内部 ID。追跡目的で使用されます |
`EventName` | 操作の名前。 この要素の値の一覧については、「[イベント名](#event-names)」を参照してください。 |
`resourceId` | Azure Resource Manager リソース ID |
`SubscriptionId` | サブスクリプション ID |
`EventTimeString` | 操作時間 |
`EventProperties` |操作のプロパティ。 次の例に示すように、この要素によってイベントに関するより詳細な情報が提供されます。 |
`Status` | 操作の状態。 この値は、 **[成功]** または **[失敗]** のどちらかです。  |
`Caller` | 操作の呼び出し元 (Azure Portal または管理クライアント) |
`Category` | OperationalLogs |

次のコードは、操作ログの JSON 文字列の例です。

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

#### <a name="event-names"></a>イベント名
イベント名は、次の列挙型の中の操作の種類 + リソースの種類として設定されます。 たとえば、「`Create Queue`」、「`Retrieve Event Hu`」、「`Delete Rule`」のように指定します。 

| 操作の種類 | リソースの種類 | 
| -------------- | ------------- | 
| <ul><li>作成</li><li>更新</li><li>削除</li><li>取得</li><li>Unknown</li></ul> | <ul><li>名前空間</li><li>キュー</li><li>トピック</li><li>サブスクリプション</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>ルール</li>ConsumerGroup</li> |

### <a name="autoscale-logs-schema"></a>自動スケール ログのスキーマ
自動スケール ログの JSON には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ---- | ----------- | 
| `TrackingId` | 内部 ID。トレース目的で使用されます |
| `ResourceId` | Azure Resource Manager リソース ID。 |
| `Message` | 情報メッセージ。自動インフレ アクションに関する詳細情報を提供します。 このメッセージには、特定の名前空間のスループット ユニットの以前の値と現在の値、および TU のインフレをもたらした要因が含まれます。 |

自動スケーリング イベントの例を次に示します。 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2021 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

### <a name="kafka-coordinator-logs-schema"></a>Kafka コーディネーター ログのスキーマ
Kafka コーディネーター ログの JSON には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ---- | ----------- | 
| `RequestId` | 要求 ID。トレース目的で使用されます |
| `ResourceId` | Azure Resource Manager リソース ID |
| `Operation` | グループの調整中に実行された操作の名前 |
| `ClientId` | クライアント ID |
| `NamespaceName` | 名前空間名 | 
| `SubscriptionId` | Azure サブスクリプション ID |
| `Message` | 情報または警告メッセージ。グループの調整中に実行されたアクションに関する詳細情報を提供します。 |

#### <a name="example"></a>例

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

### <a name="kafka-user-error-logs-schema"></a>Kafka ユーザー エラー ログのスキーマ
Kafka ユーザー エラー ログの JSON には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ---- | ----------- |
| `TrackingId` | 追跡 ID。トレース目的で使用されます。 |
| `NamespaceName` | 名前空間名 |
| `Eventhub` | イベント ハブ名 |
| `PartitionId` | Partition ID |
| `GroupId` | グループ ID |
| `ClientId` | クライアント ID |
| `ResourceId` | Azure Resource Manager リソース ID。 |
| `Message` | 情報メッセージ。エラーに関する詳細情報を提供します |

### <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs 仮想ネットワーク接続イベントのスキーマ
Event Hubs 仮想ネットワーク (VNet) 接続イベントの JSON には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ---  | ----------- | 
| `SubscriptionId` | Azure サブスクリプション ID |
| `NamespaceName` | 名前空間名 |
| `IPAddress` | Event Hubs サービスに接続しているクライアントの IP アドレス |
| `Action` | 接続要求を評価するときに Event Hubs サービスによって実行されたアクション。 サポートされているアクションは、**接続の受け入れ** と **接続の拒否** です。 |
| `Reason` | アクションが実行された理由を提供します |
| `Count` | 指定されたアクションの発生回数 |
| `ResourceId` | Azure Resource Manager リソース ID。 |

仮想ネットワーク ログが生成されるのは、名前空間で **選択されたネットワーク** から、または **特定の IP アドレス** (IP フィルター規則) からのアクセスが許可されている場合のみです。 これらの機能を使用して名前空間へのアクセスを制限することを望まず、Event Hubs 名前空間に接続しているクライアントの IP アドレスを追跡するために引き続き仮想ネットワーク ログを取得したい場合は、次の回避策を使用できます。 [IP フィルター処理を有効](../event-hubs-ip-filtering.md)にし、アドレス指定可能な IPv4 の範囲の合計 (1.0.0.0/1 - 255.0.0.0/1) を追加します。 Event Hubs の IP フィルターでは IPv6 範囲はサポートされていません。 ログには、IPv6 形式のプライベート エンドポイント アドレスが表示される場合があります。 

#### <a name="example"></a>例

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="customer-managed-key-user-logs-schema"></a>カスタマー マネージド キーのユーザー ログ スキーマ
カスタマー マネージド キーのユーザー ログの JSON には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ---- | ----------- | 
| `Category` | メッセージのカテゴリの種類。 **error** と **info** のどちらかの値です。 たとえば、キー コンテナーのキーが無効になっている場合は、情報カテゴリになります。または、キーをラップ解除できない場合は、エラーが発生する可能性があります。|
| `ResourceId` | 内部リソース ID。Azure サブスクリプション ID と名前空間名が含まれます |
| `KeyVault` | Key Vault リソースの名前 |
| `Key` | Event Hubs 名前空間の暗号化に使用される Azure Key Vault キー名。 |
| `Version` | Azure Key Vault キーのバージョン。|
| `Operation` | キー コンテナー内のキーに対して実行される操作。 たとえば、キーの無効化/有効化、ラップ、またはラップ解除。 |
| `Code` | 操作に関連付けられたコード。 例:エラー コード 404 は、キーが見つからなかったことを示します。 |
| `Message` | メッセージ。エラーまたは情報メッセージに関する詳細情報を提供します |

カスタマー マネージド キーのログの例を次に示します。

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

BYOK 暗号化が有効になっている場合に調べる一般的なエラー コードを次に示します。

| アクション | エラー コード | 結果のデータの状態 |
| ------ | ---------- | ----------------------- | 
| キー コンテナーからラップ/ラップ解除のアクセス許可を削除する | 403 |    Inaccessible |
| ラップ/ラップ解除のアクセス許可を付与された AAD プリンシパルから AAD ロールのメンバーシップを削除する | 403 |  Inaccessible |
| キー コンテナーから暗号化キーを削除する | 404 | Inaccessible |
| キー コンテナーを削除する | 404 | アクセス不可 (必須の設定である、論理的な削除が有効になっていることが前提) |
| 暗号化キーの有効期限を既に経過した期限に変更する | 403 |   Inaccessible  |
| キー暗号化キーがアクティブでなくなるように NBF (期間の開始時刻) を変更する | 403 | Inaccessible  |
| キー コンテナー ファイアウォールに対して **[Allow MSFT Services]\(MSFT サービスを許可する\)** オプションを選択するか、暗号化キーを使用しているキー コンテナーへのネットワーク アクセスをブロックする | 403 | Inaccessible |
| キーコンテナーを別のテナントに移動する | 404 | Inaccessible |  
| 断続的なネットワークの問題または DNS/AAD/MSI の停止 |  | キャッシュされたデータ暗号化キーを使用してアクセス可能 |

