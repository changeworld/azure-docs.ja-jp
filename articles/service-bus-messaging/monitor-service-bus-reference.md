---
title: Azure Service Bus の監視データのリファレンス
description: Azure Service Bus を監視する際に必要となる重要なリファレンス資料。
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 05/18/2021
ms.openlocfilehash: a32972b6400831a4045082fa5d0255b06ed66210
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061292"
---
# <a name="monitoring-azure-service-bus-data-reference"></a>Azure Service Bus の監視データのリファレンス
Azure Service Bus の監視データの収集と分析の詳細については、「[Azure Service Bus の監視](monitor-service-bus.md)」を参照してください。

## <a name="metrics"></a>メトリック
このセクションでは、Azure Service Bus 用に自動的に収集されるすべてのプラットフォーム メトリックを示します。 これらのメトリックのリソース プロバイダーは **Microsoft.ServiceBus/namespaces** です。

### <a name="request-metrics"></a>要求のメトリック
データおよび管理操作要求の数をカウントします。

| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| 受信要求| はい | Count | 合計 | 指定された期間にわたって Service Bus サービスに対して実行された要求の数。 | EntityName | 
|成功した要求| いいえ | Count | 合計 | 指定された期間にわたって Service Bus サービスに対して実行された成功した要求の数。 | エンティティ名<br/>OperationResult|
|サーバー エラー| いいえ | Count | 合計 | 指定された期間にわたって Service Bus サービスでエラーのために処理されなかった要求の数。 | エンティティ名<br/>OperationResult|
|User Errors (ユーザー エラー) | いいえ | Count | 合計 | 指定された期間にわたってユーザー エラーのために処理されなかった要求の数。 | エンティティ名|
|調整された要求数| いいえ | Count | 合計 | 使用量を超えたため調整された要求の数。 |  エンティティ名|

次の 2 種類のエラーは、**ユーザー エラー** として分類されます。

1. クライアント側のエラー (HTTP では 400 エラー)。
2. [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) など、メッセージの処理中に発生するエラー。


### <a name="message-metrics"></a>メッセージのメトリック

| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|受信メッセージ| はい | Count | 合計 | 指定された期間にわたって Service Bus に送信されたイベントまたはメッセージの数。 このメトリックには、自動的に転送されるメッセージは含まれません。 | エンティティ名|
|送信メッセージ| はい | Count | 合計 | 指定された期間にわたって Service Bus から受信されたイベントまたはメッセージの数。 | エンティティ名|
| メッセージ| いいえ | Count | Average | キュー/トピック内のメッセージの数。 | エンティティ名 |
| アクティブなメッセージ| いいえ | Count | Average | キュー/トピック内のアクティブなメッセージの数。 | エンティティ名 |
| 配信不能メッセージ| いいえ | Count | Average | キュー/トピック内の配信不能メッセージの数。  | エンティティ名 |
| スケジュール設定されたメッセージ| いいえ | Count | Average | キュー/トピック内のスケジュール済みメッセージの数。 | エンティティ名 |
| サイズ | いいえ | バイト | Average | エンティティ (キューまたはトピック) のサイズ (バイト単位)。 | エンティティ名 | 

> [!NOTE]
> アクティブ、配信不能、スケジュール済み、完了、破棄済みのメッセージの値は、特定の時点の値です。 その時点の直後に使用された受信メッセージは、これらのメトリックに反映されない場合があります。 

### <a name="connection-metrics"></a>接続のメトリック

| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|アクティブな接続| いいえ | Count | 合計 | 名前空間および名前空間内のエンティティでのアクティブな接続の数。 このメトリックの値は、特定の時点の値です。 その時点の直後にアクティブになった接続は、メトリックに反映されない場合があります。 | |
|開かれている接続数 | いいえ | Count | Average | 開かれている接続の数。 | エンティティ名|
|切断された接続数 | いいえ | Count | Average | 閉じられている接続の数。 | エンティティ名|

### <a name="resource-usage-metrics"></a>リソース使用状況のメトリック

> [!NOTE] 
> 次のメトリックは **プレミアム** 層でのみ使用可能です。 
> 
> Premium レベルの名前空間の停止を監視するための重要なメトリックは次のとおりです。**名前空間ごとの CPU 使用率** と **名前空間 1 つあたりのメモリ サイズ**。 Azure Monitor を使用して、これらのメトリックの[アラートを設定](../azure-monitor/alerts/alerts-metric.md)します。
> 
> 監視できるもう 1 つのメトリックは、**スロットルされた要求** です。 これは、名前空間がメモリ、CPU、およびブローカー接続の制限内にある限り、問題にはなりません。 詳細については、「[Azure Service Bus Premium レベルでのスロットル](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)」を参照してください。

| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|名前空間あたりの CPU 使用率| いいえ | CPU | Percent | 名前空間の CPU 使用率 (%)。 | [レプリカ] |
|名前空間あたりのメモリ サイズの使用量| いいえ | メモリ使用量 | Percent | 名前空間のメモリ使用率 (%)。 | [レプリカ] |

### <a name="error-metrics"></a>エラー メトリック
| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|サーバー エラー| いいえ | Count | 合計 | 指定された期間にわたって Service Bus サービスでエラーのために処理されなかった要求の数。 | エンティティ名<br/><br/>操作の結果 |
|User Errors (ユーザー エラー) | いいえ | Count | 合計 | 指定された期間にわたってユーザー エラーのために処理されなかった要求の数。 | エンティティ名<br/><br/>操作の結果|

## <a name="metric-dimensions"></a>メトリック ディメンション

Azure Service Bus は、Azure Monitor でのメトリックの次のディメンションをサポートします。 メトリックへのディメンションの追加は省略可能です。 ディメンションを追加しない場合、メトリックは名前空間レベルで指定されます。 

|ディメンション名|説明|
| ------------------- | ----------------- |
|エンティティ名| Service Bus は、名前空間の下のメッセージング エンティティをサポートします。|

## <a name="resource-logs"></a>リソース ログ
このセクションでは、Azure Service Bus 用に収集できるリソース ログの種類を示します。

- 操作ログ
- 仮想ネットワークと IP フィルターのログ

### <a name="operational-logs"></a>操作ログ
操作ログ エントリには、次の表に示す要素が含まれます。

| Name | 説明 |
| ------- | ------- |
| ActivityId | 指定されたアクティビティを識別するために使用される内部 ID |
| EventName | 操作の名前 |
| ResourceId | Azure Resource Manager リソース ID |
| SubscriptionId | サブスクリプション ID |
| EventTimeString | 操作時間 |
| EventProperties | 操作プロパティ |
| Status | 操作の状態 |
| Caller | 操作の呼び出し元 (Azure portal または管理クライアント) |
| カテゴリ | OperationalLogs |

操作ログの JSON 文字列の例を次に示します。

```json
{
  "ActivityId": "0000000000-0000-0000-0000-00000000000000",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/<AZURE SUBSCRPTION ID>/RESOURCEGROUPS/<RESOURCE GROUP NAME>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<SERVICE BUS NAMESPACE NAME>",
  "SubscriptionId": "0000000000-0000-0000-0000-00000000000000",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-00000000000000\",\"Namespace\":\"mynamespace\",\"Via\":\"https://mynamespace.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

### <a name="events-and-operations-captured-in-operational-logs"></a>操作ログでキャプチャされるイベントと操作
操作ログでは、Azure Service Bus 名前空間上で実行されるすべての管理操作がキャプチャされます。 Azure Service Bus 上では大量のデータ操作が実行されるため、データ操作は取り込まれません。

> [!NOTE]
> データ操作をより適切に追跡できるように、クライアント側のトレースを使用することをお勧めします。

操作ログでは、以下の管理操作がキャプチャされます。 

| Scope | Operation|
|-------| -------- |
| 名前空間 | <ul> <li> 名前空間の作成</li> <li> 名前空間の更新 </li> <li> 名前空間の削除 </li> <li> 名前空間 SharedAccess ポリシーの更新 </li> </ul> | 
| キュー | <ul> <li> キューの作成</li> <li> キューの更新</li> <li> キューの削除 </li> <li> キューの削除 (自動削除) </li> </ul> | 
| トピック | <ul> <li> トピックの作成 </li> <li> トピックの更新 </li> <li> トピックの削除 </li> <li> トピックの削除 (自動削除) </li> </ul> |
| サブスクリプション | <ul> <li> サブスクリプションの作成 </li> <li> サブスクリプションの更新 </li> <li> サブスクリプションの削除 </li> <li> サブスクリプションの削除 (自動削除) </li> </ul> |

> [!NOTE]
> 現在、"*読み取り*" 操作は操作ログで追跡されません。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル
Azure Service Bus では、Azure Monitor ログの Kusto テーブルが使用されます。 これらのテーブルに対して Log Analytics を使用してクエリを実行できます。 サービスで使用される Kusto テーブルの一覧については、[Azure Monitor ログ テーブルのリファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype#service-bus)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
- Azure Service Bus の監視の詳細については、「[Azure Service Bus の監視](monitor-service-bus.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
