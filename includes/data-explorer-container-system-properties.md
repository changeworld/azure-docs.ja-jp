---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128753"
---
### <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

> [!Note]
> * システム プロパティは、単一レコードのイベントに対してサポートされています。
> * `csv` マッピングの場合、レコードの先頭にプロパティが追加されます。 `json` マッピングの場合、ドロップダウン リストに表示される名前に従ってプロパティが追加されます。

テーブルの **[データ ソース]** セクションで **[イベント システムのプロパティ]** を選択した場合は、テーブルのスキーマとマッピングに次のプロパティを含める必要があります。

**テーブル スキーマの例**

データに 3 つの列 (`Timespan`、`Metric`、および `Value`) が含まれており、含めるプロパティが `x-opt-enqueued-time` および `x-opt-offset` の場合は、次のコマンドを使用してテーブル スキーマを作成または変更します。

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**CSV マッピングの例**

次のコマンドを実行して、レコードの先頭にデータを追加します。 序数値に注意してください。

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**JSON マッピングの例**

データは、システム プロパティ名が **[データ接続]** ブレードの **[イベント システムのプロパティ]** 一覧に表示されるときに、システム プロパティを使用して追加されます。 次の各コマンドを実行します。

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
