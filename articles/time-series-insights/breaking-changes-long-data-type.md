---
title: Long データ型のサポートの追加 | Microsoft Docs
description: Long データ型のサポート
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: dpalled
ms.openlocfilehash: c31ca7fd3eca89159d583b8a51b59a7bd6b8ed67
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528056"
---
# <a name="adding-support-for-long-data-type"></a>Long データ型のサポートの追加

これらの変更は Gen2 環境にのみ適用されます。 Gen1 環境がある場合は、これらの変更を無視してもかまいません。

ここでは、ユーザーに影響を与える可能性のある Azure Time Series Insights Gen2 での数値データの格納とインデックス作成の方法を変更しています。 次のいずれかのケースの影響を受けている場合は、できるだけ早く必要な変更を行ってください。 データは、リージョンに応じて、2020 年 6 月 29 日から 7 月 30 日までの間に Long および Double としてのインデックス作成が開始されます。 この変更に関してご不明な点や懸念事項がある場合は、Azure portal を通じてサポート チケットを送信し、この情報を伝えてください。

この変更は、次のような場合に影響を及ぼします。

1. 現在タイム シリーズ モデル変数を使用していて、テレメトリ データで整数データ型のみを送信する場合。
1. 現在タイム シリーズ モデル変数を使用していて、テレメトリ データで整数データ型と非整数データ型の両方を送信する場合。
1. カテゴリ変数を使用して、整数値をカテゴリにマップする場合。
1. JavaScript SDK を使用して、カスタムのフロントエンド アプリケーションを構築する場合。
1. Warm ストア (WS) の 1,000 個のプロパティ名の制限に近づいていて、整数データと非整数データの両方を送信する場合は、[Azure portal](https://portal.azure.com/) でプロパティ数をメトリックとして表示できます。

上記のいずれかのケースに該当する場合は、この変更に対応するためにモデルに変更を加える必要があります。 Azure Time Series Insights Gen2 エクスプローラーと、推奨される変更を加えた API を使用するカスタム クライアントの両方で、変数定義の Time Series 式を更新してください。 詳細については、以下をご覧ください。

IoT ソリューションと制約によっては、Azure Time Series Insights Gen2 環境に送信されるデータを表示できない場合があります。 データが整数のみであるか、整数と非整数の両方であるかがわからない場合は、いくつかのオプションがあります。 この機能がリリースされるまで待ってから、エクスプローラー UI で未加工のイベントを調査して、2 つの別々の列に保存されているプロパティを把握することができます。 すべての数値タグに対して以下の変更を事前に加えることも、イベントのサブセットをストレージに一時的にルーティングして、スキーマについて十分に理解して調査することもできます。 イベントを保存するには、Event Hubs に対して[イベント キャプチャ](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)を有効にするか、IoT Hub から Azure Blob Storage に[ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage)します。 データは、[Event Hub Explorer](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer) を使用して、または[イベント プロセッサ ホスト](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)を使用して監視することもできます。 IoT Hub を使用する場合は、組み込みエンドポイントにアクセスする方法について、[こちら](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)のドキュメントを参照してください。

これらの変更の影響を受けていて、上記の日付までに変更できない場合は、中断が発生する可能性があることに注意してください。この場合、クエリ API または Time Series Insights Explorer を使用してアクセスされた、影響を受けるタイム シリーズ変数は、*null* を返します (つまり、エクスプローラーにデータが表示されません)。

## <a name="recommended-changes"></a>推奨される変更

ケース 1 および 2: **タイム シリーズ モデル変数を使用し、整数データ型のみを送信するか、またはテレメトリ データで整数型と非整数型の両方を送信します。**

現時点で整数のテレメトリ データを送信している場合、データは "propertyValue_double" と "propertyValue_long" の 2 つの列に分割されます。

変更が有効になり、"propertyValue_double" 内の以前に取り込まれた (および将来取り込まれる) 数値データがコピーされない場合は、整数データが "propertyValue_long" に書き込まれます。

"PropertyValue" プロパティに対してこれら 2 つの列のデータのクエリを実行する場合は、TSX で *coalesce()* スカラー関数を使用する必要があります。 この関数は、同じ DataType の引数を受け取り、引数リスト内の最初の null 以外の値を返します (詳細については、[こちら](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)を参照してください)。

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Time Series エクスプローラーでの変数の定義 - 数値

*前の変数の定義:*

[![前の変数の定義](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*新しい変数の定義:*

[![新しい変数の定義](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

*“coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))”* を、カスタムの [Time Series 式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)として使用することもできます。

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Time Series Query API を使用したインライン変数の定義 - 数値

*前の変数の定義:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*新しい変数の定義:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*“coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))”* を、カスタムの [Time Series 式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)として使用することもできます。

> [!NOTE]
> これらの変数は、使用される可能性のあるすべての場所 (タイム シリーズ モデル、保存済みのクエリ、Power BI コネクタのクエリ) で更新することをお勧めします。

ケース 3: **カテゴリ変数を使用した整数値のカテゴリへのマッピング**

現時点で整数値をカテゴリにマップするカテゴリ変数を使用している場合は、toLong 関数を使用して Double 型から Long 型にデータを変換している可能性があります。 上記の場合と同様に、Double と Long の DataType の列を結合する必要があります。

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Time Series エクスプローラーでの変数の定義 - カテゴリ

*前の変数の定義:*

[![前の変数の定義](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*新しい変数の定義:*

[![新しい変数の定義](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

*“coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))”* を、カスタムの [Time Series 式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)として使用することもできます。

カテゴリ変数では、値を整数型にする必要があります。 coalesce() 内のすべての引数の DataType は、カスタムの [Time Series 式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)で Long 型である必要があります。

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Time Series Query API を使用したインライン変数の定義 - カテゴリ

*前の変数の定義:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*新しい変数の定義:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

カテゴリ変数では、値を整数型にする必要があります。 coalesce() 内のすべての引数の DataType は、カスタムの [Time Series 式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)で Long 型である必要があります。

> [!NOTE]
> これらの変数は、使用される可能性のあるすべての場所 (タイム シリーズ モデル、保存済みのクエリ、Power BI コネクタのクエリ) で更新することをお勧めします。

ケース 4:**JavaScript SDK を使用したカスタム フロントエンド アプリケーションの構築**

上記のケース 1 ～ 3 の影響を受けていて、カスタム アプリケーションを構築する場合は、上記の例に示すように、*coalesce()* 関数を使用するようにクエリを更新する必要があります。

ケース 5: **Warm ストアの 1,000 個のプロパティの上限に近づいている**

多数のプロパティがある Warm ストアのユーザーで、この変更によって環境が 1,000 個の WS プロパティ名の制限を超える可能性がある場合は、Azure portal からサポート チケットを送信し、この通信を伝えてください。

## <a name="next-steps"></a>次のステップ

* サポートされているデータ型の完全な一覧については、[サポートされているデータ型](concepts-supported-data-types.md)を参照してください。
