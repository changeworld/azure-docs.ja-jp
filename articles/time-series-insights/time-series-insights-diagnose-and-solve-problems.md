---
title: "問題の診断と解決 | Microsoft Docs"
description: "このチュートリアルでは、Time Series Insights 環境の問題を診断して解決する方法について説明します。"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>問題の診断と解決

## <a name="i-do-not-see-my-data"></a>データが表示されない
環境内のデータが [Time Series Insights ポータル](https://insights.timeseries.azure.com)に表示されない理由はいくつかあります。

### <a name="does-your-event-source-have-data-in-json-format"></a>イベント ソースに JSON 形式のデータがありますか?
Azure Time Series Insights は現時点で JSON データのみをサポートしています。 JSON のサンプルについては、[こちらのページ](time-series-insights-send-events.md#supported-json-shapes)の「*サポートされている JSON 構造*」セクションを参照してください。

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>イベント ソースを登録する際に、必要なアクセス許可を持つキーを指定しましたか?
1. IoTHub の場合は、"*サービス接続*" アクセス許可を持つキーを指定する必要があります。

   ![IotHub のサービス接続アクセス許可](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   上の図に示すように、ポリシー "iothubowner" と "service" は両方とも "サービス接続" アクセス許可が設定されているため、どちらも使用できます。
2. EventHub の場合は、"*リッスン*" アクセス許可を持つキーを指定する必要があります。

   ![イベント ハブのリッスン アクセス許可](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   上の図に示すように、ポリシー "read" と "manage" は両方とも "読み取り" アクセス許可が設定されているため、どちらも使用できます。

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>指定したコンシューマー グループは Time Series Insights 専用ですか?
IoTHub または EventHub では、登録時に、データを読み取るときに使用するコンシューマー グループを指定する必要があります。 このコンシューマー グループは共有できません。 共有すると、基盤となるイベント ハブによってリーダーのいずれかがランダムに選択されて切断されます。

## <a name="i-see-my-data-but-there-is-a-lag"></a>データは表示されるが、タイム ラグがある
[Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境内のデータが一部しか表示されない理由はいくつかあります。

### <a name="your-environment-might-be-getting-throttled"></a>環境が調整されている可能性がある
環境の SKU の種類と容量に基づいて調整制限が適用されます。 環境内のすべてのイベント ソースで、この容量が共有されます。 適用される制限を超えるデータをイベント ハブ/IoT Hub イベント ソースがプッシュしている場合は、調整が行われ、遅延が発生します。

次の図は、SKU が S1 で容量が 3 の Time Series Insights 環境を示しています。 この環境は、1 日あたり 300 万イベントを受信できます。

![環境の SKU の現在の容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

この環境が、次の図に示す受信レートでイベント ハブからメッセージを受信していたとします。

![環境の SKU の現在の容量](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

図に示すように、1 日の受信レートは約 67,000 メッセージです。 このレートは毎分およそ 46 メッセージに相当します。 各イベント ハブ メッセージが 1 つの Time Series Insights イベントにフラット化されている場合、この環境では調整は行われません。 各イベント ハブ メッセージが 100 個の Time Series Insights イベントにフラット化されている場合は、毎分 4,600 イベントを受信することになります。 容量が 3 である S1 SKU 環境が受信できるのは、毎分 2,100 イベントまでです  (100 万イベント/日 => 700 イベント/分、3 ユニット => 2,100 イベント/日)。 そのため、調整によるタイム ラグが発生します。 フラット化のロジックの概要については、[こちらのページ](time-series-insights-send-events.md#supported-json-shapes)の「*サポートされている JSON 構造*」セクションを参照してください。

#### <a name="recommended-steps"></a>推奨される手順
ラグを解消するには、環境の SKU 容量を増やします。 [Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>履歴データをプッシュしているため受信が遅くなっている可能性がある
既存のイベント ソースを接続している場合は、イベント ハブ/IoT Hub に既にデータがある可能性があります。 その場合、環境はイベント ソース メッセージのリテンション期間の始めからデータをプルし始めます。 この動作は既定の動作であり、無効にすることはできません。 調整が適用される可能性があり、履歴データの受信が追いつくのに時間がかかることがあります。

#### <a name="recommended-steps"></a>推奨される手順
ラグを解消するには、次の手順を実行します。
1. SKU 容量を許可される最大値 (この場合は 10) まで増やします。 容量が増えると、より短い時間で受信プロセスが追いつくようになります。 どれくらいの時間で追いつくかを、[Time Series Insights ポータル](https://insights.timeseries.azure.com)の可用性グラフで視覚化できます。 なお、容量の増加分には料金が発生します。
2. ラグが解消したら、SKU 容量は通常の受信レートまで低下します。

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>イベント ソースの "*タイムスタンプ プロパティ名*" 設定が機能しない
名前と値が次の規則に準拠していることを確認してください。
1. タイムスタンプ プロパティ名は__大文字と小文字が区別されます__。
2. イベント ソースから JSON 文字列として取得されるタイムスタンプ プロパティ値は、__yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ の形式である必要があります。 例: 2008-04-12T12:53Z
