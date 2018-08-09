---
title: Azure Time Series Insights の問題を診断して解決する | Microsoft Docs
description: この記事では、Azure Time Series Insights 環境内で発生する可能性がある一般的な問題を診断、トラブルシューティング、および解決する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: b05b824d8d35351030ca466566f14e4249d4b99d
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626622"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Time Series Insights 環境の問題を診断して解決する

## <a name="problem-1-no-data-is-shown"></a>問題 1: データが表示されない
[Azure Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)でデータが表示されない一般的な理由はいくつかあります。

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>考えられる原因 A: イベント ソース　データが JSON 形式でない
Azure Time Series Insights は JSON データのみをサポートしています。 JSON のサンプルについては、「[サポートされている JSON 構造](time-series-insights-send-events.md#supported-json-shapes)」を参照してください。

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>考えられる原因 B: イベント ソース キーに必要なアクセス許可がありません。
* IoT Hub の場合は、**サービス接続**アクセス許可を備えたキーを指定する必要があります。

   ![IoT Hub のサービス接続アクセス許可](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   上の図に示すように、ポリシー **iothubowner** と **service** は両方とも**サービス接続**アクセス許可が設定されているため、どちらも使用できます。
   
* イベント ハブの場合は、**リッスン** アクセス許可を持つキーを指定する必要があります。

   ![イベント ハブのリッスン アクセス許可](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   上の図に示すように、ポリシー **read** と **manage** は両方とも**リッスン** アクセス許可が設定されているため、どちらも使用できます。

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>考えられる原因 C: 指定したコンシューマー グループが Time Series Insights 専用でない
IoT Hub またはイベント ハブの登録時に、データを読み取るために使用する必要があるコンシューマー グループを指定します。 このコンシューマー グループは共有**できません**。 コンシューマー グループを共有すると、基盤となるイベント ハブによってリーダーのいずれかがランダムに自動的に切断されます。 Time Series Insights が読み取る一意のコンシューマー グループを指定します。

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>問題 2: 表示されるデータと表示されないデータがある
データを部分的に表示できるが、タイム ラグがある場合、いくつかの可能性が考えられます。

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>考えられる原因 A: 環境が調整されている
これは、データを持つイベント ソースの作成後に環境がプロビジョニングされたときによくある問題です。  Azure IoT Hub と Event Hubs は、データを最大 7 日間格納します。  TSI はイベント ソース内で常に最も古いイベントから開始します (FIFO)。  したがって、単一ユニットの TSI 環境である S1 に接続したときにイベント ソースに 500 万イベントが存在した場合、TSI は 1 日あたり約 100 万イベントを読み取ります。  これは一見すると、TSI に 5 日間の待機時間が発生しているように見えることがあります。  実際に起こっているのは、環境が調整されているということです。  イベント ソースに古いイベントがある場合、2 つのうちいずれかの方法を使用できます。

- イベント ソースのリテンション期間を変更して、TSI に表示したくない古いイベントが除去されやすくなるようにします
- プロビジョニングする環境サイズを大きくして (ユニット数の観点で)、古いイベントのスループットを増やします。  上記の例を使用して、同じ S1 環境を 1 日あたり 5 ユニットに増やした場合、環境はその日のうちに現時点までの遅れを取り戻すはずです。  安定状態でのイベント生成が 1 日あたり 1 M 以下の場合、遅れを取り戻した後でイベントの容量を 1 ユニットに戻すことができます。  

環境の SKU のタイプと容量に基づいて調整制限が適用されます。 環境内のすべてのイベント ソースで、この容量が共有されます。 IoT Hub またはイベント ハブのイベント ソースが適用される制限を超えるデータをプッシュしている場合は、調整が行われ、タイム ラグが発生します。

次の図は、SKU が S1 で容量が 3 の Time Series Insights 環境を示しています。 この環境は、1 日あたり 300 万イベントを受信できます。

![環境の SKU の現在の容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

たとえば、この環境はイベント ハブからメッセージを取り込んでいるとします。 次の図に示す受信レートを観察します。

![イベント ハブの受信レートの例](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

図に示すように、1 日の受信レートは約 67,000 メッセージです。 このレートは毎分およそ 46 メッセージに相当します。 各イベント ハブ メッセージが 1 つの Time Series Insights イベントにフラット化されている場合、この環境では調整は行われません。 各イベント ハブ メッセージが 100 個の Time Series Insights イベントにフラット化されている場合は、毎分 4,600 イベントを受信することになります。 容量 3 の S1 SKU 環境は、毎分 2,100 イベントしか受信できません (100 万イベント/日 = 700 イベント/分 × 3 ユニット = 2,100 イベント/分)。 そのため、調整によるタイム ラグが発生します。 

フラット化のロジックのしくみについては、「[サポートされている JSON 構造](time-series-insights-send-events.md#supported-json-shapes)」を参照してください。

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>調整が過剰な場合の推奨される解決手順
タイム ラグを解消するには、環境の SKU 容量を増やします。 詳細については、「[Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)」を参照してください。

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>考えられる原因 B: 履歴データの初期の取り込みで受信の遅延が発生している
既存のイベント ソースを接続している場合は、IoT Hub またはイベント ハブに既にデータがある可能性があります。 環境はイベント ソース メッセージのリテンション期間の始めからデータをプルし始めます。

この動作は既定の動作であり、オーバーライドできません。 調整を適用できますが、履歴データの受信の遅れを取り戻すのに時間がかかることがあります。

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>大規模な初期の取り込みの推奨される解決方法の手順
タイム ラグを解消するには、次の手順を実行します。
1. SKU 容量を許可される最大値 (この場合は 10) まで増やします。 容量を増やすと、より短い時間で受信プロセスが追いつくようになります。 どれくらいの時間で追いつくかを、[Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)の可用性グラフで視覚化できます。 なお、容量の増加分には料金が発生します。
2. タイム ラグが解消したら、SKU 容量を通常の受信レートに戻します。

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題 3: イベント ソースの*タイムスタンプ プロパティ名*設定が機能しない
名前と値が次の規則に準拠していることを確認してください。
* タイムスタンプ プロパティ名は_大文字と小文字が区別されます_。
* イベント ソースから JSON 文字列として取得されるタイムスタンプ プロパティ値は、_yyyy-MM-ddTHH:mm:ss.FFFFFFFK_ の形式である必要があります。 例: 2008-04-12T12:53Z

*タイムスタンプ プロパティ名*がキャプチャされて正しく動作していることを確認する最も簡単な方法は、TSI エクスプローラーを使用することです。  TSI エクスプローラー内でグラフを使用して、*タイムスタンプのプロパティ名*を指定した後で期間を選択します。  選択内容を右クリックして、*[explore events (イベントの探索)]* オプションを選択します。  最初の列見出しは*タイムスタンプのプロパティ名*であり、*Timestamp* という単語の横に *($ts)* が記載されている必要があり、次のようになっていないはずです。
- *(abc)* の場合は、TSI がデータ値を文字列として読み取っていることを示しています
- *予定表アイコン*の場合は、TSI がデータ値を*日時*として読み取っていることを示しています
- *#* の場合は、TSI がデータ値を整数として読み取っていることを示しています


## <a name="next-steps"></a>次の手順
- 追加の支援については、[MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights)または [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights) でメッセージの交換を始めてください。 
- サポート オプションについては、[Azure サポート](https://azure.microsoft.com/support/options/)を利用することもできます。
