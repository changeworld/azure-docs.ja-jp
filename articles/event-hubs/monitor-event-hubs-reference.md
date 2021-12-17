---
title: Azure Event Hubs データの監視のリファレンス
description: Azure Event Hubs を監視する際に必要となる重要なリファレンス資料。
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 06/11/2021
ms.openlocfilehash: 4dc40cef074a223a2cc34e3a87c9acae840f8dbf
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992047"
---
# <a name="monitoring-azure-event-hubs-data-reference"></a>Azure Event Hubs データの監視のリファレンス
Azure Event Hubs の監視データの収集と分析の詳細については、[Azure Event Hubs の監視](monitor-event-hubs.md)に関するページを参照してください。

## <a name="metrics"></a>メトリック
このセクションには、Azure Event Hubs 用に自動的に収集されたすべてのプラットフォーム メトリックが一覧表示されています。 これらのメトリックのリソース プロバイダーは、**Microsoft.EventHub/clusters** または **Microsoft.EventHub/clusters** です。

### <a name="request-metrics"></a>要求のメトリック
データおよび管理操作要求の数をカウントします。

| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| 受信要求| はい | Count | 合計 | 指定された期間にわたって Event Hubs サービスに対して実行された要求の数。 | エンティティ名| 
| 成功した要求| いいえ | Count | 合計 | 指定された期間にわたって Event Hubs サービスに対して実行されて成功した要求の数。 |  エンティティ名<br/><br/>操作の結果 | 
| 調整された要求数| いいえ | Count | 合計 |  使用量を超えたため調整された要求の数。 | エンティティ名<br/><br/>操作の結果 |

次の 2 種類のエラーは、**ユーザー エラー** に分類されます。

1. クライアント側のエラー (HTTP では 400 エラー)。
2. メッセージの処理中に発生するエラー。


### <a name="message-metrics"></a>メッセージのメトリック
| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|受信メッセージ|  はい | Count | 合計 | 指定された期間にわたって Event Hubs に送信されたイベントまたはメッセージの数。 | エンティティ名|
|送信メッセージ| はい | Count | 合計 | 指定された期間にわたって Event Hubs から受信されたイベントまたはメッセージの数。 | エンティティ名 | 
| キャプチャされたメッセージ数| いいえ | Count| 合計 | キャプチャされたメッセージの数。  |  エンティティ名 | 
|着信バイト数 | はい |  バイト | 合計 | 指定した期間にわたるイベント ハブの受信バイト数。  | エンティティ名| 
|発信バイト数 | はい |  バイト | 合計 |指定した期間にわたるイベント ハブの送信バイト数。  | エンティティ名 | 
| サイズ | いいえ |  バイト | Average |  イベント ハブのサイズ (バイト単位)。|エンティティ名 |


> [!NOTE]
> これらの値は、特定の時点の値です。 その時点の直後に使用された受信メッセージは、これらのメトリックに反映されない場合があります。 

### <a name="capture-metrics"></a>キャプチャ メトリック
| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| キャプチャされたメッセージ数| いいえ | Count| 合計 | キャプチャされたメッセージの数。  | エンティティ名 |
| キャプチャされたバイト数 | いいえ | バイト | 合計 | イベント ハブのキャプチャされたバイト数 | エンティティ名 | 
| バックログのキャプチャ | いいえ | Count| 合計 | イベント ハブのキャプチャ バックログ | エンティティ名 | 


### <a name="connection-metrics"></a>接続のメトリック
| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|アクティブな接続| いいえ | Count | Average | 名前空間および名前空間内のエンティティ (イベント ハブ) でのアクティブな接続の数。 このメトリックの値は、特定の時点の値です。 その時点の直後にアクティブになった接続は、メトリックに反映されない場合があります。| エンティティ名 | 
|開かれている接続数 | いいえ | Count | Average |  開かれている接続の数。 | エンティティ名 | 
|切断された接続数 | いいえ | Count | Average|  閉じられている接続の数。 | エンティティ名 | 

### <a name="error-metrics"></a>エラー メトリック
| メトリックの名前 |  診断設定を使用したエクスポートが可能 | ユニット | 集計の種類 |  説明 | Dimensions |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|サーバー エラー| いいえ | Count | 合計 | 指定された期間にわたって Event Hubs サービスでエラーのために処理されなかった要求の数。 | エンティティ名<br/><br/>操作の結果 |
|User Errors (ユーザー エラー) | いいえ | Count | 合計 | 指定された期間にわたってユーザー エラーのために処理されなかった要求の数。 | エンティティ名<br/><br/>操作の結果|
|クォータ超過エラー数 | いいえ |Count | 合計 | 指定した期間にわたってクォータ超過によって発生したエラーの数。 | エンティティ名<br/><br/>操作の結果|

> [!NOTE]
> Logic Apps では、エポック受信者が作成され、サービスの負荷に応じて受信者が 1 つのノードから別のノードに移動される場合があります。 これらの移動時に、`ReceiverDisconnection` 例外が発生する可能性があります。 これらは、Event Hubs のサービス側ではユーザー エラーとしてカウントされます。 Logic Apps で Event Hubs クライアントからエラーを収集して、ユーザー ログでそれらを確認できるようにすることができます。

## <a name="metric-dimensions"></a>メトリック ディメンション

Azure Event Hubs は、Azure Monitor でのメトリックの次のディメンションをサポートします。 メトリックへのディメンションの追加は省略可能です。 ディメンションを追加しない場合、メトリックは名前空間レベルで指定されます。 

|ディメンション名|説明|
| ------------------- | ----------------- |
|エンティティ名| イベント ハブの名前。|

## <a name="resource-logs"></a>リソース ログ
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]



## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル
Azure Event Hubs では、Azure Monitor ログの Kusto テーブルが使用されます。 これらのテーブルに対して Log Analytics を使用してクエリを実行できます。 サービスで使用される Kusto テーブルの一覧については、[Azure Monitor ログ テーブルのリファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
- Azure Event Hubs の監視の詳細については、[Azure Event Hubs の監視](monitor-event-hubs.md)に関するページを参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
