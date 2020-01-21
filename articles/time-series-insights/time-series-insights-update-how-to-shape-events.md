---
title: イベントの調整 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Insights プレビューでのクエリ用イベントの調整のベスト プラクティスと調整方法について説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: 567770c00c645aeb79e1efb0e9119b9ac829f3fe
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861661"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューでのイベントの調整

この記事では、インジェストおよび Azure Time Series Insights プレビュー クエリの効率の最大化のために JSON ファイルを調整する方法について説明します。

## <a name="best-practices"></a>ベスト プラクティス

Azure Time Series Insights プレビューにイベントを送信する方法を検討します。 具体的には、常に、以下を考慮する必要があります。

* ネットワーク上でできるだけ効率的にデータを送信する。
* 自分のシナリオにとってより適切に集計できる方法でデータを格納する。

最適なクエリ パフォーマンスを得るには、次を行います。

* 不要なプロパティを送信しない。 Time Series Insights プレビューは、従量課金制です。 クエリを実行するデータを格納して処理することをお勧めします。
* 静的データにインスタンス フィールドを使用する。 この手法によって、ネットワーク上で静的なデータを送信しないようにできます。 インスタンス フィールドはタイム シリーズ モデルのコンポーネントであり、一般に利用できる Time Series Insights サービスで参照データのように動作します。 インスタンス フィールドの詳細については、[タイム シリーズ モデル](./time-series-insights-update-tsm.md)に関する記事を参照してください。
* 2 つ以上のイベント間でディメンション プロパティを共有します。 この手法によって、ネットワーク経由でより効率的にデータを送信できます。
* 深い入れ子の配列を使用しない。 Time Series Insights プレビューでは、オブジェクトを含む最大 2 つのレベルの入れ子配列がサポートされます。 Time Series Insights プレビューでは、メッセージ内の配列は、プロパティ値のペアを使用する複数のイベントにフラット化されます。
* すべて、またはほとんどのイベントに対して数個のメジャーのみが存在する場合は、これらのメジャーを同じオブジェクト内の個別のプロパティとして送信することをお勧めします。 個別に送信することでイベントの数が減少し、処理する必要があるイベントが少なくなるため、クエリのパフォーマンスが向上する可能性があります。

インジェスト中、列名が区切り文字により単一の値となるよう、入れ子を含むペイロードはフラット化されます。 Time Series Insights プレビューでは、区切り文字にアンダースコアが使用されています。 これは、期間を使用した製品の GA バージョンからの変更であることに注意してください。 プレビュー期間中は、次の 2 番目の例に示すように、フラット化に関する注意事項があります。

## <a name="examples"></a>例

次の例では、2 つ以上のデバイスが、メジャーや信号を送信するシナリオを使用しています。 メジャーまたは信号には、*フロー レート*、*エンジン オイル圧*、*温度*、または*湿度*を使用できます。

この例では Azure IoT Hub メッセージが 1 つあり、外側の配列に共通のディメンション値の共有部分が含まれています。 外側の配列は、Time Series Instance データを使用してメッセージの効率を改善します。 

Time Series Instance には、デバイス メタデータが含まれています。 このメタデータは、イベントごとには変更されませんが、データ解析に役立つプロパティを提供します。 ネットワーク経由で送信されるバイト数を削減し、メッセージをより効率的にするには、共通のディメンション値のバッチ処理と、Time Series Instance メタデータの使用を検討してください。

### <a name="example-1"></a>例 1:

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

### <a name="time-series-instance"></a>Time Series Instance 

> [!NOTE]
> タイム シリーズ ID は*deviceId* です。

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights プレビューは、クエリ時に (フラット化の後に) テーブルを結合します。 このテーブルには、**Type** などの追加の列が含まれます。 次の例で、テレメトリ データの[調整](./time-series-insights-send-events.md#supported-json-shapes)方法を示します。

| deviceId  | 種類 | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

前の例では、次の点に注意してください。

* 静的プロパティは、ネットワーク経由で送信されるデータを最適化するために、Time Series Insights プレビューに格納されます。
* Time Series Insights プレビュー データは、インスタンスに定義されている Time Series ID を介して、クエリ時に結合されます。
* 2 層の入れ子が使用されています。 この数は、Time Series Insights プレビューでサポートされている最も高い値です。 深く入れ子された配列は使用しないことが重要です。
* メジャーはほとんどないため、同じオブジェクト内の個別のプロパティとして送信されます。 この例では、**series_Flow Rate psi**、**series_Engine Oil Pressure psi**、**series_Flow Rate ft3/s** が一意な列です。

>[!IMPORTANT]
> インスタンス フィールドは、テレメトリと共には格納されません。 それらは、メタデータと共にタイム シリーズ モデルに格納されます。
> 上記の表は、クエリ ビューを表しています。

### <a name="example-2"></a>例 2:

次の JSON を検討してみましょう。

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```
上の例では、フラット化された `data_flow` プロパティで、`data_flow` プロパティとの名前の競合が発生します。 この場合、*最新の*プロパティ値によって、以前の値が上書きされます。 この動作によってビジネス シナリオに課題が生じる場合は、TSI チームにお問い合わせください。

> [!WARNING] 
> フラット化または別のメカニズムによって同じイベント ペイロードに重複するプロパティが存在することになった場合、最新のプロパティ値が保存され、以前の値が上書きされます。


## <a name="next-steps"></a>次のステップ

- これらのガイドラインを実践するには、[Azure Time Series Insights プレビューのクエリ構文](./time-series-insights-query-data-csharp.md)に関する記事を参照してください。 Time Series Insights プレビューのデータ アクセス用 REST API のクエリ構文の詳細について説明します。
- サポートされている JSON の構造については、[サポートされている JSON 構造](./time-series-insights-send-events.md#supported-json-shapes)に関する記事を参照してください。
