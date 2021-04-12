---
title: ストリーミング インジェスト イベント ソース - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 へのデータ ストリーミングについて説明します。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504398"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2 のイベント ソース

Azure Time Series Insights Gen2 環境には、最大 2 つのストリーミング イベント ソースを含めることができます。 次の 2 種類の Azure リソースが入力としてサポートされています。

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

イベントは、UTF-8 でエンコードされた JSON として送信する必要があります。

## <a name="create-or-edit-event-sources"></a>イベント ソースの作成または編集

イベント ソースは、ハブと Azure Time Series Insights Gen2 環境との間のリンクであり、種類が `Time Series Insights event source` である別のリソースがリソース グループに作成されます。 IoT Hub またはイベント ハブのリソースは、Azure Time Series Insights Gen2 環境または別のサブスクリプションと同じ Azure サブスクリプションに存在することができます。 ただし、Azure Time Series Insights 環境と IoT Hub またはイベント ハブは、同じ Azure リージョン内に格納することをお勧めします。

環境のイベント ソースの作成、編集、または削除は、[Azure portal](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment)、[Azure CLI](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source)、[Azure Resource Manager テンプレート](time-series-insights-manage-resources-using-azure-resource-manager-template.md)、および [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) を使用して行うことができます。

## <a name="start-options"></a>開始オプション

イベント ソースを作成する場合は、収集する既存データを指定することもできます。 この設定はオプションです。 次のオプションを使用できます。

| Name   |  説明  |  Azure Resource Manager テンプレートの例 |
|----------|-------------|------|
| EarliestAvailable | IoT またはイベント ハブ内に格納されている既存のデータをすべて取り込みます | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  イベント ソースが作成された後に到着するデータの取り込みを開始します。 イベント ソースを作成する前にストリームされていた既存データはすべて無視されます。 これは、Azure portal の既定の設定です   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | カスタムのエンキューされた時刻 (UTC) 以降、お使いの環境にデータが取り込まれます。 カスタムのエンキューされた時刻またはそれ以降に IoT またはイベント ハブにエンキューされたすべてのイベントは、取り込まれて格納されます。 カスタムのエンキューされた時刻より前に到着したすべてのイベントは無視されます。 "エンキューされた時間" とは、IoT またはイベント ハブにイベントが到着した時刻 (UTC) を指します。 これは、イベントの本文に含まれるカスタムの[タイムスタンプ プロパティ](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)とは異なります。 |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - EarliestAvailable を選択し、既存のデータが多数ある場合は、Azure Time Series Insights Gen2 環境がすべてのデータを処理するため、初期の待機時間が長くなる可能性があります。
> - この長い待機時間は、データのインデックスが作成されると最終的に短くなります。 長い待機時間が継続する場合は、Azure portal からサポート チケットを送信してください。

* EarliestAvailable

![EarliestAvailable の図](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![EventSourceCreationTime の図](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![CustomEnqueuedTime の図](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>ストリーミング インジェストのベスト プラクティス

- 常に Azure Time Series Insights Gen2 環境に固有のコンシューマー グループを常に作成して、イベント ソースからデータを消費します。 コンシューマー グループを再利用すると、ランダムに切断が発生し、データが失われる可能性があります。

- 同じ Azure リージョンで、Azure Time Series Insights Gen2 環境と、IoT ハブ、イベントハブ、またはその両方を構成します。 別のリージョンにイベント ソースを構成することもできますが、このシナリオはサポートされておらず、高可用性を保証できません。

- 環境の[スループット速度制限](./concepts-streaming-ingress-throughput-limits.md)またはパーティションごとの制限を超えないようにします。

- 環境でデータ処理の問題が発生した場合に通知されるように、遅延の[警告](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)を構成します。 推奨アラート条件については、下の「[運用ワークロード](./concepts-streaming-ingestion-event-sources.md#production-workloads)」を参照してください。

- ストリーミング インジェストは、準リアルタイム データおよび最近のデータにのみ使用します。履歴データのストリーミングはサポートされていません。

- プロパティをエスケープする方法と JSON [データをフラット化して格納する](./concepts-json-flattening-escaping-rules.md)方法を理解します。

- イベント ソース接続文字列を指定する場合は、最小限の特権の原則に従います。 Event Hubs の場合は、*send* 要求のみの共有アクセス ポリシーを構成し、IoT Hub の場合は、*service connect* アクセス許可のみを使用します。

> [!CAUTION]
> IoT Hub またはイベント ハブを削除し、同じ名前で新しいリソースを再作成する場合は、新しいイベント ソースを作成し、新しい IoT Hub またはイベント ハブをアタッチする必要があります。 この手順が完了するまで、データは取り込まれません。

## <a name="production-workloads"></a>運用ワークロード

上記のベスト プラクティスに加えて、ビジネスに不可欠なワークロードには次を実装することをお勧めします。

- IoT Hub またはイベント ハブ データの保有期間を最大の 7 日間に増やします。

- Azure portal で環境アラートを作成します。 プラットフォーム [メトリクス](./how-to-monitor-tsi-reference.md#metrics)に基づくアラートでは、エンドツーエンドのパイプライン動作を評価できます。 アラートを作成して管理する手順については、[こちら](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)をご覧ください。 推奨アラート条件:

  - IngressReceivedMessagesTimeLag が 5 分以上
  - IngressReceivedBytes が 0
- IoT Hub または Event Hub のパーティション間で取り込み負荷のバランスを維持します。

### <a name="historical-data-ingestion"></a>履歴データのインジェスト

Azure Time Series Insights Gen2 では、ストリーミング パイプラインを使用した履歴データのインポートは現在サポートされていません。 過去のデータをご使用の環境にインポートする必要がある場合は、次のガイドラインに従ってください。

- ライブ データと履歴データを並行してストリーミングしないでください。 順不同のデータを取り込むと、クエリのパフォーマンスが低下します。
- 最適なパフォーマンスを得るために、履歴データは時系列で取り込みます。
- インジェストのスループット率制限以下を維持します。
- データがウォーム ストアの保持期間よりも古い場合、ウォーム ストアを無効にします。

## <a name="event-source-timestamp"></a>イベント ソースのタイムスタンプ

イベント ソースを構成するときに、タイムスタンプ ID プロパティを入力するように求められます。 タイムスタンプ プロパティは、時間の経過と共にイベントを追跡するために使用されます。これは、[クエリ API](/rest/api/time-series-insights/dataaccessgen2/query/execute) でタイムスタンプ `$ts` として使用される時刻であり、Azure Time Series Insights Explorer で時系列をプロットするために使用されます。 作成時にプロパティが指定されない場合、またはイベントのタイムスタンプ プロパティがない場合は、イベントが IoT Hub または Events Hubs にエンキューした時刻が既定値として使用されます。 タイムスタンプ プロパティ値は UTC に格納されます。

通常、ユーザーは、既定のハブ エンキュー時刻を使用する代わりに、タイムスタンプ プロパティをカスタマイズして、センサーまたはタグが読み取りを生成した時刻を使用することを選択します。 これが特に必要になるのは、デバイスの接続が間欠的に失われ、遅延メッセージがまとめて Azure Time Series Insights Gen2 に転送されるときです。

カスタム タイムスタンプが、入れ子になった JSON オブジェクトまたは配列内にある場合は、[フラット化およびエスケープの名前付け規則](concepts-json-flattening-escaping-rules.md)に従って、正しいプロパティ名を指定する必要があります。 たとえば、[ここで](concepts-json-flattening-escaping-rules.md#example-a)示されている JSON ペイロードの イベント ソース タイムスタンプは、`"values.time"` と入力する必要があります。

### <a name="time-zone-offsets"></a>タイムゾーンのオフセット

タイムスタンプは ISO 8601 形式で送信される必要があります。UTC で格納されます。 タイムゾーンのオフセットが指定されている場合は、オフセットが適用され、その時刻が UTC 形式で格納されたり返されたりします。 オフセットの形式が正しくない場合は無視されます。 元のオフセットのコンテキストがソリューションに含まれない可能性がある場合は、追加の個別のイベント プロパティでオフセット データを送信して、それを保存し、アプリケーションがクエリ応答で参照できるようにします。

タイムゾーンのオフセットは、次のいずれかの形式にする必要があります。

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>次のステップ

- [JSON のフラット化とエスケープのルール](./concepts-json-flattening-escaping-rules.md)を読み、イベントの格納方法を理解する。

- 使用環境の[スループットに関する制限事項](./concepts-streaming-ingress-throughput-limits.md)を理解する。
