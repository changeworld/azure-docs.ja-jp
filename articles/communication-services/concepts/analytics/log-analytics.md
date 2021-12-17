---
title: Azure Communication Services - Log Analytics (プレビュー)
titleSuffix: An Azure Communication Services concept document
description: 通話概要と通話診断のログに対する Log Analytics の使用について
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 42e7a6d972131125130e3dc1976b1061a32e07a9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716678"
---
# <a name="log-analytics-for-communications-services-preview"></a>Communications Services の Log Analytics (プレビュー)

## <a name="overview-and-access"></a>概要とアクセス

Communications Services のログに対して [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) を利用する前に、まず、[診断設定でのログの有効化](enable-logging.md)に関するページで説明されている手順に従う必要があります。 ログと [Log Analytics ワークスペース](../../../azure-monitor/logs/design-logs-deployment.md)を有効にすると、さまざまな[既定のクエリ パック](../../../azure-monitor/logs/query-packs.md#default-query-pack)にアクセスできるようになります。これらのパックは、ログから取得できるデータ (以下で説明) をすばやく視覚化して理解するために役立ちます。 さらに、Log Analytics を利用して、より多くの Communications Services インサイトに Azure Monitor ブックを介してアクセスし ([Communications Services のインサイト](https://dev.loganalytics.io/)に関するページを参照)、独自のクエリやブックを作成したり、あらゆるクエリに [REST API でアクセス](insights.md)したりできます。

### <a name="access"></a>Access
クエリにアクセスするには、まず、Communications Services のリソース ページで、左側のナビゲーションにある [監視] セクション内の [ログ] をクリックします。

:::image type="content" source="media\log-analytics\access-log-analytics.png" alt-text="Log Analytics のナビゲーション":::

Communications Services で使用できる[既定のクエリ パック](../../../azure-monitor/logs/query-packs.md#default-query-pack)のすべてがモーダル画面に表示されます。左側の一覧から、使用できるクエリ パックを選択できます。

:::image type="content" source="media\log-analytics\log-analytics-modal-resource.png" alt-text="Log Analytics クエリのモーダル" lightbox="media\log-analytics\log-analytics-modal-resource.png":::

モーダル画面を閉じた後でも、診断設定で有効にしたログとメトリックのスキーマに応じて、さまざまなクエリ パックにアクセスしたり、テーブル形式のデータに直接アクセスしたりできます。 ここでは、[KQL (Kusto)](/azure/data-explorer/kusto/query/) を使用してデータから独自のクエリを作成できます。 クエリの使用、編集、作成の詳細については、[Log Analytics クエリ](../../../azure-monitor/logs/queries.md)に関するページを参照してください。

:::image type="content" source="media\log-analytics\log-analytics-queries-resource.png" alt-text="リソース内の Log Analytics クエリ" lightbox="media\log-analytics\log-analytics-queries-resource.png":::

:::image type="content" source="media\log-analytics\log-analytics-tables-resource.png" alt-text="リソース内の Log Analytics テーブル" lightbox="media\log-analytics\log-analytics-tables-resource.png":::

## <a name="default-query-packs-for-call-summary-and-call-diagnostic-logs"></a>通話概要ログと通話診断ログ用の既定のクエリ パック
[通話概要ログと通話診断ログ](call-logs-azure-monitor.md)用の[既定のクエリ パック](../../../azure-monitor/logs/query-packs.md#default-query-pack)に含まれる個々のクエリについて、コード サンプルと、使用可能な各クエリの出力例を含めて、以下で説明します。
### <a name="call-overview-queries"></a>通話概要クエリ
#### <a name="number-of-participants-per-call"></a>通話あたりの参加者数

```
// Count number of calls and participants,
// and print average participants per call
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId
| summarize num_participants=count(), num_calls=dcount(CorrelationId)
| extend avg_participants = todecimal(num_participants) / todecimal(num_calls)
```

サンプル出力:

:::image type="content" source="media\log-analytics\call-overview-sample.png" alt-text="通話概要クエリ":::

#### <a name="number-of-participants-per-group-call"></a>グループ通話あたりの参加者数

```
// Count number of participants per group call
ACSCallSummary
| where CallType == 'Group'
| distinct CorrelationId, ParticipantId
| summarize num_participants=count() by CorrelationId
| summarize participant_counts=count() by num_participants
| order by num_participants asc 
| render columnchart with  (xcolumn = num_participants, title="Number of participants per group call")
```

サンプル出力:

:::image type="content" source="media\log-analytics\participants-group-sample.png" alt-text="グループ通話あたりの参加者数クエリ":::

#### <a name="ratio-of-call-types"></a>通話の種類の比率

```
// Ratio of call types
ACSCallSummary
| summarize call_types=dcount(CorrelationId) by CallType
| render piechart title="Call Type Ratio"

```

サンプル出力:

:::image type="content" source="media\log-analytics\ratio-call-types-sample.png" alt-text="通話の種類の比率クエリ":::

#### <a name="call-duration-distribution"></a>通話時間の分布

```
// Call duration histogram
ACSCallSummary
| distinct CorrelationId, CallDuration
|summarize duration_counts=count() by CallDuration
| order by CallDuration asc
| render columnchart with (xcolumn = CallDuration, title="Call duration histogram")
```

サンプル出力:

:::image type="content" source="media\log-analytics\call-duration-sample.png" alt-text="通話分布クエリ":::

#### <a name="call-duration-percentiles"></a>通話時間パーセンタイル

```
// Call duration percentiles
ACSCallSummary
| distinct CorrelationId, CallDuration
| summarize avg(CallDuration), percentiles(CallDuration, 50, 90, 99)
```

サンプル出力:

:::image type="content" source="media\log-analytics\call-duration-percentile-example.png" alt-text="通話時間パーセンタイル クエリ":::

### <a name="endpoint-information-queries"></a>エンドポイント情報クエリ

#### <a name="number-of-endpoints-per-call"></a>通話あたりのエンドポイント数

```
// Count number of calls and endpoints,
// and print average endpoints per call
ACSCallSummary
| distinct CorrelationId, EndpointId
| summarize num_endpoints=count(), num_calls=dcount(CorrelationId)
| extend avg_endpoints = todecimal(num_endpoints) / todecimal(num_calls)
```

サンプル出力:

:::image type="content" source="media\log-analytics\endpoints-per-call.png" alt-text="通話あたりのエンドポイント数クエリ":::

#### <a name="ratio-of-sdk-versions"></a>SDK バージョンの比率

```
// Ratio of SDK Versions
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, SdkVersion
| summarize sdk_counts=count() by SdkVersion
| order by SdkVersion asc
| render piechart title="SDK Version Ratio"
```

サンプル出力:

:::image type="content" source="media\log-analytics\sdk-ratio-sample.png" alt-text="SDK バージョンの比率を示す円グラフ。"::: :::image type="content" source="media\log-analytics\sdk-ratio-table-sample.png" alt-text="SDK バージョンを示すテーブル":::

#### <a name="ratio-of-os-versions-simplified-os-name"></a>OS バージョンの比率 (簡略化した OS 名)

```
// Ratio of OS Versions (simplified OS name)
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, OsVersion
| extend simple_os = case(  indexof(OsVersion, "Android") != -1, tostring(split(OsVersion, ";")[0]),
                            indexof(OsVersion, "Darwin") != -1, tostring(split(OsVersion, ":")[0]),
                            indexof(OsVersion, "Windows") != -1, tostring(split(OsVersion, ".")[0]),
                            OsVersion
                        )
| summarize os_counts=count() by simple_os
| order by simple_os asc
| render piechart title="OS Version Ratio"
```

サンプル出力:

:::image type="content" source="media\log-analytics\os-version-graph.png" alt-text="オペレーティング システムの比率を示す円グラフ":::
:::image type="content" source="media\log-analytics\os-version-table.png" alt-text="OS バージョンを示すテーブル":::

### <a name="media-stream-queries"></a>メディア ストリーム クエリ
#### <a name="streams-per-call"></a>通話あたりのストリーム数

```
// Count number of calls and streams,
// and print average streams per call
ACSCallDiagnostics
| summarize num_streams=count(), num_calls=dcount(CorrelationId)
| extend avg_streams = todecimal(num_streams) / todecimal(num_calls)
```
サンプル出力:

:::image type="content" source="media\log-analytics\streams-call-output.png" alt-text="通話あたりのストリーム数クエリ":::

#### <a name="streams-per-call-histogram"></a>通話あたりのストリーム数ヒストグラム

```
// Distribution of streams per call
ACSCallDiagnostics
| summarize streams_per_call=count() by CorrelationId
| summarize stream_counts=count() by streams_per_call
| order by streams_per_call asc
| render columnchart title="Streams per call histogram"
```

:::image type="content" source="media\log-analytics\streams-call-histogram.png" alt-text="通話あたりのストリーム数ヒストグラム":::

#### <a name="ratio-of-media-types"></a>メディアの種類の比率

```
// Ratio of media types by call
ACSCallDiagnostics
| summarize media_types=count() by MediaType
| render piechart title="Media Type Ratio"
```

:::image type="content" source="media\log-analytics\media-type-ratio.png" alt-text="メディアの種類の比率を示す円グラフ":::

### <a name="quality-metrics-queries"></a>品質指標クエリ

#### <a name="average-telemetry-values"></a>平均テレメトリ値

```
// Average telemetry values over all streams
ACSCallDiagnostics
| summarize Avg_JitterAvg=avg(JitterAvg),
            Avg_JitterMax=avg(JitterMax),
            Avg_RoundTripTimeAvg=avg(RoundTripTimeAvg),
            Avg_RoundTripTimeMax=avg(RoundTripTimeMax),
            Avg_PacketLossRateAvg=avg(PacketLossRateAvg),
            Avg_PacketLossRateMax=avg(PacketLossRateMax)
```

:::image type="content" source="media\log-analytics\avg-telemetry-values.png" alt-text="平均テレメトリ値":::

#### <a name="jitteravg-histogram"></a>JitterAvg ヒストグラム

```
// Jitter Average Histogram
ACSCallDiagnostics
| where isnotnull(JitterAvg)
| summarize JitterAvg_counts=count() by JitterAvg
| order by JitterAvg asc
| render columnchart with (xcolumn = JitterAvg, title="JitterAvg histogram")
```

:::image type="content" source="media\log-analytics\jitteravg-histogram.png" alt-text="平均ジッター ヒストグラム":::

#### <a name="jittermax-histogram"></a>JitterMax ヒストグラム

```
// Jitter Max Histogram
ACSCallDiagnostics
| where isnotnull(JitterMax)
|summarize JitterMax_counts=count() by JitterMax
| order by JitterMax asc
| render columnchart with (xcolumn = JitterMax, title="JitterMax histogram")
```

:::image type="content" source="media\log-analytics\jittermax-histogram.png" alt-text="最大ジッター ヒストグラム":::

#### <a name="packetlossrateavg-histogram"></a>PacketLossRateAvg ヒストグラム
```
// PacketLossRate Average Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateAvg)
|summarize PacketLossRateAvg_counts=count() by bin(PacketLossRateAvg, 0.01)
| order by PacketLossRateAvg asc
| render columnchart with (xcolumn = PacketLossRateAvg, title="PacketLossRateAvg histogram")
```

:::image type="content" source="media\log-analytics\packetloss-avg-histogram.png" alt-text="平均パケット損失ヒストグラム":::

#### <a name="packetlossratemax-histogram"></a>PacketLossRateMax ヒストグラム
```
// PacketLossRate Max Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateMax)
|summarize PacketLossRateMax_counts=count() by bin(PacketLossRateMax, 0.01)
| order by PacketLossRateMax asc
| render columnchart with (xcolumn = PacketLossRateMax, title="PacketLossRateMax histogram")
```

:::image type="content" source="media\log-analytics\packetloss-max-histogram.png" alt-text="最大パケット損失ヒストグラム":::

#### <a name="roundtriptimeavg-histogram"></a>RoundTripTimeAvg ヒストグラム
```
// RoundTripTime Average Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeAvg)
|summarize RoundTripTimeAvg_counts=count() by RoundTripTimeAvg
| order by RoundTripTimeAvg asc
| render columnchart with (xcolumn = RoundTripTimeAvg, title="RoundTripTimeAvg histogram")
```

:::image type="content" source="media\log-analytics\rtt-avg-histogram.png" alt-text="平均 RTT ヒストグラム":::

#### <a name="roundtriptimemax-histogram"></a>RoundTripTimeMax ヒストグラム
```
// RoundTripTime Max Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeMax)
|summarize RoundTripTimeMax_counts=count() by RoundTripTimeMax
| order by RoundTripTimeMax asc
| render columnchart with (xcolumn = RoundTripTimeMax, title="RoundTripTimeMax histogram")
```

:::image type="content" source="media\log-analytics\rtt-max-histogram.png" alt-text="最大 RTT ヒストグラム":::

#### <a name="poor-jitter-quality"></a>ジッター低品質
```
// Get proportion of calls with poor quality jitter
// (defined as jitter being higher than 30ms)
ACSCallDiagnostics
| extend JitterQuality = iff(JitterAvg > 30, "Poor", "Good")
| summarize count() by JitterQuality
| render piechart title="Jitter Quality"
```

:::image type="content" source="media\log-analytics\jitter-quality.png" alt-text="ジッター品質":::


#### <a name="packetlossrate-quality"></a>PacketLossRate 品質
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\packet-loss-quality.png" alt-text="パケット損失率品質":::

#### <a name="roundtriptime-quality"></a>RoundTripTime 品質
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\rtt-quality.png" alt-text="RTT 品質":::

### <a name="parameterizable-queries"></a>パラメーター化可能クエリ

#### <a name="daily-calls-in-the-last-week"></a>先週の 1 日あたり通話数
```
// Histogram of daily calls over the last week
ACSCallSummary
| where CallStartTime > now() - 7d
| distinct CorrelationId, CallStartTime
| extend hour  = floor(CallStartTime, 1d)
| summarize event_count=count() by day
| sort by day asc
| render columnchart title="Number of calls in last week"
```

:::image type="content" source="media\log-analytics\calls-per-week.png" alt-text="先週の 1 日あたり通話数":::

#### <a name="calls-per-hour-in-last-day"></a>最終日の 1 時間あたり通話数
```
// Histogram of calls per hour in the last day
ACSCallSummary
| where CallStartTime > now() - 1d
| distinct CorrelationId, CallStartTime
| extend hour = floor(CallStartTime, 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render columnchart title="Number of calls per hour in last day"
```

:::image type="content" source="media\log-analytics\calls-per-hour.png" alt-text="最終日の 1 時間あたり通話数":::