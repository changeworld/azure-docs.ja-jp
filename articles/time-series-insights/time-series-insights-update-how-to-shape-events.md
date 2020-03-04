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
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650925"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューでのイベントの調整

この記事では、Azure Time Series Insights でのインジェストのために JSON ペイロードを形成し、プレビュー クエリの効率を最大化するためのベスト プラクティスを定義します。

## <a name="best-practices"></a>ベスト プラクティス

Time Series Insights プレビュー環境にイベントを送信する方法を慎重に検討することをお勧めします。 

一般的なベスト プラクティスは次のとおりです。

* ネットワーク上でできるだけ効率的にデータを送信する。
* 自分のシナリオにとってより適切に集計できる方法でデータを格納する。

最適なクエリ パフォーマンスを得るには、次のルールに従うことをお勧めします。

* 不要なプロパティを送信しない。 Time Series Insights プレビューは、従量課金制です。 クエリを実行するデータを格納して処理することをお勧めします。
* 静的データにインスタンス フィールドを使用する。 この手法によって、ネットワーク上で静的なデータを送信しないようにできます。 インスタンス フィールドはタイム シリーズ モデルのコンポーネントであり、一般に利用できる Time Series Insights サービスで参照データのように動作します。 インスタンス フィールドの詳細については、[タイム シリーズ モデル](./time-series-insights-update-tsm.md)に関する記事を参照してください。
* 2 つ以上のイベント間でディメンション プロパティを共有します。 この手法によって、ネットワーク経由でより効率的にデータを送信できます。
* 深い入れ子の配列を使用しない。 Time Series Insights プレビューでは、オブジェクトを含む最大 2 つのレベルの入れ子配列がサポートされます。 Time Series Insights プレビューでは、メッセージ内の配列は、プロパティ値のペアを使用する複数のイベントにフラット化されます。
* すべて、またはほとんどのイベントに対して数個のメジャーのみが存在する場合は、これらのメジャーを同じオブジェクト内の個別のプロパティとして送信することをお勧めします。 個別に送信することでイベントの数が減少し、処理する必要があるイベントが少なくなるため、クエリのパフォーマンスが向上する可能性があります。

## <a name="column-flattening"></a>列のフラット化

インジェスト中、列名が区切り文字により単一の値となるよう、入れ子にしたオブジェクトを含むペイロードはフラット化されます。

* たとえば、次の入れ子になった JSON があるとします。

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   フラット化すると `data_flow` になります。

> [!IMPORTANT]
> * Azure Time Series Insights プレビューでは、列輪郭にアンダースコア (`_`) がされています。
> * 代わりにピリオド (`.`) を使用する一般公開との違いにご注意ください。

より複雑なシナリオは以下のとおりです。

#### <a name="example-1"></a>例 1:

次のシナリオには、測定 (信号) を送信するデバイスが 2 つ (またはそれ以上) あります。*フロー レート*、 *エンジン オイル圧*, *温度*、 *湿度*を使用できます。

送信される Azure IoT Hub メッセージが 1 つあり、外側の配列に共通のディメンション値の共有部分が含まれます（メッセージに含まれる 2 つのデバイスエントリにご注意ください）。

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

**重要なポイント:**

* JSON の例には、[時系列インスタンス](./time-series-insights-update-tsm.md#time-series-model-instances) データを使用してメッセージの効率を向上させる外部配列があります。 時系列インスタンスでは、デバイス メタデータが変更される可能性はありませんが、多くの場合、データ分析に便利なプロパティが提供されます。

* JSON は、2 つ以上のメッセージ (各デバイスから 1 つ) を結合して、時間の経過と共に帯域幅に対して 1 つのペイロードを保存します。

* 各デバイスの個々の系列データ ポイントは、1 つの **系列** 属性に結合され、各デバイスの更新プログラムを継続的にストリーミングする必要が少なくなります。

> [!TIP]
> データを送信し、テレメトリの効率を高めるために必要なメッセージの数を減らすには、共通のディメンション値と時系列インスタンス メタデータを 1 つの JSON ペイロードにバッチ処理することをご検討ください。

#### <a name="time-series-instance"></a>Time Series Instance 

[時系列インスタンス](./time-series-insights-update-tsm.md#time-series-model-instances) を使用して、JSON をより最適に整形する方法について詳しく見ていきましょう。 

> [!NOTE]
> 次の[時系列 ID](./time-series-insights-update-how-to-id.md) は、 *deviceIds*です。

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

Time Series Insights プレビューは、クエリ時に (フラット化の後に) テーブルを結合します。 このテーブルには、**Type** などの追加の列が含まれます。

| deviceId  | Type | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  上の表は、[Time Series Preview Explorer](./time-series-insights-update-explorer.md)のクエリ ビューを表しています。

**重要なポイント:**

* 前の例では、静的プロパティは、ネットワーク経由で送信されるデータを最適化するために、Time Series Insights プレビューに格納されます。
* Time Series Insights プレビュー データは、インスタンスに定義されている Time Series ID を介して、クエリ時に結合されます。
* 2 層の入れ子が使用されています。 この数は、Time Series Insights プレビューでサポートされている最も高い値です。 深く入れ子された配列は使用しないことが重要です。
* メジャーはほとんどないため、同じオブジェクト内の個別のプロパティとして送信されます。 この例では、**series_Flow Rate psi**、**series_Engine Oil Pressure psi**、**series_Flow Rate ft3/s** が一意な列です。

>[!IMPORTANT]
> インスタンス フィールドは、テレメトリと共には格納されません。 それらは、メタデータと共にタイム シリーズ モデルに格納されます。

#### <a name="example-2"></a>例 2:

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

上の例では、フラット化された `data["flow"]` プロパティで、`data_flow` プロパティとの名前の競合が発生します。

この場合、*最新の*プロパティ値によって、以前の値が上書きされます。 

> [!TIP]
> 詳細については、Time Series Insights チームにお問い合わせください！

> [!WARNING] 
> * フラット化または別のメカニズムによって同じ (単数) イベント ペイロードに重複するプロパティが存在する場合、最新の > プロパティ値が保存され、以前の値が上書きされます。
> * 一連の結合イベントは、相互に上書きされません。

## <a name="next-steps"></a>次のステップ

* これらのガイドラインを実践するには、[Azure Time Series Insights プレビューのクエリ構文](./time-series-insights-query-data-csharp.md)に関する記事を参照してください。 データ アクセスのための Time Series Insights [Preview REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) のクエリ構文の詳細について説明します。

* JSON のベスト プラクティスと [時系列モデル](./time-series-insights-update-how-to-tsm.md)を組み合わせる方法について説明します。
