---
title: サポートされているデータ型 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Preview でサポートされているデータ型について説明します。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049452"
---
# <a name="supported-data-types"></a>サポートされるデータ型

次の表に、Time Series Insights でサポートされているデータ型を示します。

| データ型 | 説明 | 例 | Parquet のプロパティ列名
|---|---|---|---|
| **bool** | 2 つの状態 (`true` または `false`) のいずれかを持つデータ型。 | "isQuestionable" : true | isQuestionable_bool
| **datetime** | 特定の時点を表します。通常、日時形式で表されます。 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 形式で表されます。 datetime プロパティは、常に UTC 形式で格納されます。 正しく書式設定されている場合は、タイム ゾーン オフセットが適用され、その後 UTC に格納される値が適用されます。 環境のタイムスタンプ プロパティと datetime オフセットの詳細については、[この](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)セクションを参照してください。 | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z" | eventProcessedLocalTime_datetime 
| **double** | 倍精度 64 ビット数  | "value": 31.0482941 | value_double
| **long** | 符号付き 64 ビット整数  | "value" :31 | value_long
| **string** | テキスト値。有効な UTF-8 で構成されている必要があります。 |  "site":"DIM_MLGGG" | site_string
| **dynamic** | 配列またはプロパティ バッグ (ディクショナリ) で構成される複合 (非プリミティブ) 型。 現時点では、プリミティブの文字列化された JSON 配列、または TS ID やタイムスタンプ プロパティを含まないオブジェクトの配列のみが、動的として格納されます。 どのようにオブジェクトがフラット化され、配列がアンロールされるかについては、この[記事](./concepts-json-flattening-escaping-rules.md)を参照してください。 |  "values": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * TSI 環境は厳密に型指定されています。 デバイスまたはタグから積分と非積分の両データが送信される場合、デバイスのプロパティ値は 2 つの double 列と long 列に分けて格納されます。API 呼び出しを行い、時系列モデル可変式を定義するときは、[coalesce() 関数](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)を使用する必要があります。

#### <a name="objects-and-arrays"></a>オブジェクトと配列

オブジェクトや配列などの複合型は、イベント ペイロードの一部として送信できます。 入れ子になったオブジェクトはフラット化され、配列は、環境構成と JSON 構造に応じて、`dynamic` として格納されるか、またはフラット化されて複数のイベントが生成されます。 [JSON のフラット化とエスケープの規則](./concepts-json-flattening-escaping-rules.md)の詳細については、こちらをご覧ください。

## <a name="next-steps"></a>次のステップ

* [JSON のフラット化とエスケープのルール](./concepts-json-flattening-escaping-rules.md)を読み、イベントの格納方法を理解する。 

* 使用環境の[スループットに関する制限事項](concepts-streaming-throughput-limitations.md)を理解する。

* ストリーミング データを取り込むための[イベント ソース](concepts-streaming-ingestion-event-sources.md)について学習する。
