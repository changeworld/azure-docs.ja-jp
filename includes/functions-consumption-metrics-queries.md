---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168114"
---
#### <a name="determine-memory-usage"></a>メモリの使用量を確認する 

**[Monitoring]\(監視\)** の **[ログ (Analytics)]** を選択した後、次のテレメトリ クエリをコピーしてクエリ ウィンドウに貼り付け、 **[実行]** を選択します。 このクエリでは、サンプリングされた各時刻におけるメモリ使用量の合計が返されます。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

結果は次の例のようになります。

| タイムスタンプ \[UTC\]          | name          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 AM | Private Bytes | 209,932,288 |
| 9/12/2019, 1:06:14\.994 AM | Private Bytes | 212,189,184 |
| 9/12/2019, 1:06:30\.010 AM | Private Bytes | 231,714,816 |
| 9/12/2019, 1:07:15\.040 AM | Private Bytes | 210,591,744 |
| 9/12/2019, 1:12:16\.285 AM | Private Bytes | 216,285,184 |
| 9/12/2019, 1:12:31\.376 AM | Private Bytes | 235,806,720 |

#### <a name="determine-duration"></a>継続時間を確認する 

Azure Monitor ではリソース レベルでメトリックが追跡され、Functions の場合は関数アプリです。 Application Insights の統合では、関数ごとにメトリックが出力されます。 関数の平均継続時間を取得するための分析クエリの例を次に示します。

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |
