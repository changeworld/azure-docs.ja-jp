---
title: Azure IoT Central での REST API を使用してデバイスに対しクエリを実行する
description: IoT Central REST API を使用してアプリケーションでデバイスに対しクエリを実行する方法
author: dominicbetts
ms.author: dobett
ms.date: 10/12/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b287bb463519d4b6ef76997dfb778ac4827014c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092574"
---
# <a name="how-to-use-the-iot-central-rest-api-to-query-devices"></a>IoT Central REST API を使用してデバイスに対しクエリを実行する方法

IoT Central REST API を使用して、IoT Central アプリケーションと統合するクライアント アプリケーションを開発できます。 REST API を使用して、IoT Central アプリケーションでデバイスに対しクエリを実行できます。 クエリ REST API を使用する方法の例を次に示します。

- デバイスによって報告された過去 10 回分のテレメトリ値を取得します。
- 同じルームにあるデバイスから過去 24 時間のデータを取得します。 ルームとは、デバイスまたはクラウド プロパティのことです。
- エラー状態でファームウェアが古くなっているすべてのデバイスを見つけます。
- デバイスからのテレメトリの傾向 (平均は 10 分のウィンドウ)。
- すべてのサーモスタット デバイスの現在のファームウェア バージョンを取得します。

この記事では、`/query` API を使用してデバイスに対しクエリを実行する方法について説明します。

デバイスは、サポートしているプロパティ、テレメトリ、コマンドを、_コンポーネント_ と _モジュール_ にグループ化できます。

すべての IoT Central REST API 呼び出しに承認ヘッダーが必要です。 詳細については、「[IoT Central REST API 呼び出しを認証および承認する方法](howto-authorize-rest-api.md)」を参照してください。

IoT Central REST API のリファレンス ドキュメントについては、「[Azure IoT Central REST API リファレンス](/rest/api/iotcentral/)」をご覧ください。

## <a name="run-a-query"></a>クエリの実行

クエリを実行するには、次の要求を使用します。

```http
POST https://{your app subdomain}.azureiotcentral.com/api/query?api-version=1.1-preview
```

クエリは要求本文に含まれ、次の例のようになります。

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

`FROM` 句の `urn:modelDefinition:fupmoiu28b:ymju9efv9` 値は、*デバイス テンプレート ID* です。 デバイス テンプレート ID を見つけるには、IoT Central アプリケーションの **[デバイス]** ページに移動し、テンプレートを使用するデバイスをポイントします。 カードには、デバイス テンプレート ID が含まれています。

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="ページ URL でデバイス テンプレート ID を検索する方法を示すスクリーンショット。":::

この要求に対する応答は、次の例のようになります。

```json
{
  "results": [
    {
      "$id": "sample-003",
      "$ts": "2021-09-10T12:59:52.015Z",
      "temperature": 47.632160152311016,
      "humidity": 49.726422005390816
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:01:34.286Z",
      "temperature": 58.898120617808495,
      "humidity": 44.66125772328022
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:04:04.96Z",
      "temperature": 52.79601469228174,
      "humidity": 71.5067230188416
    },
    {
      "$id": "sample-002",
      "$ts": "2021-09-10T13:04:36.877Z",
      "temperature": 49.610062789623264,
      "humidity": 52.78538601804491
    }
  ]
}
```

## <a name="syntax"></a>構文

クエリ構文は SQL 構文に似ていて、次の句で構成されます。

- `SELECT` は必須であり、デバイスのテレメトリ値など、取得するデータを定義します。
- `FROM` は必須であり、クエリを実行しているデバイスの種類を識別します。 この句は、デバイス テンプレート ID を指定します。
- `WHERE` は省略可能であり、結果をフィルター処理できます。
- `ORDER BY` は省略可能であり、結果を並べ替えできます。
- `GROUP BY` は省略可能であり、結果を集計できます。

以下のセクションでは、これらの句について詳しく説明します。

## <a name="select-clause"></a>SELECT 句

`SELECT` 句は、クエリ出力に含めるデータ値を一覧表示し、次の項目を含めることができます。

- テレメトリ。 デバイス テンプレートのテレメトリ名を使用します。
- 報告されるプロパティ。 デバイス テンプレートのプロパティ名を使用します。
- クラウドのプロパティ。 デバイス テンプレートのクラウド プロパティ名を使用します。
- `$id`. デバイス ID。
- `$provisioned`. デバイスがまだプロビジョニングされている場合に示すブール値。
- `$simulated`. デバイスがシミュレートされたデバイスである場合に示すブール値。
- `$ts`. テレメトリ値に関連付けられているタイムスタンプ。

デバイス テンプレートで **デバイス情報** コンポーネントなどのコンポーネントを使用する場合は、コンポーネントで定義されているテレメトリまたはプロパティを次のように参照します。

```json
{
  "query": "SELECT deviceInformation.model, deviceInformation.swVersion FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

コンポーネント名は、デバイス テンプレートで確認できます。

:::image type="content" source="media/howto-query-with-rest-api/show-component-name.png" alt-text="コンポーネント名を検索する方法を示すスクリーンショット。":::

`SELECT` 句には、次の制限が適用されます。

- ワイルドカード演算子は使用できません。
- 選択リストには 15 項目を超える項目を含められません。
- 1 つのクエリではテレメトリまたはプロパティを選択し、両方は選択しません。 プロパティ クエリには、報告されるプロパティとクラウド プロパティの両方を含めることができます。

### <a name="aliases"></a>Aliases

`SELECT` 句内の項目の別名を定義するには、`AS` キーワードを使用します。 別名はクエリ出力で使用されます。 クエリ内の他の場所でも使用できます。 次に例を示します。

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature as t, pressure as p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50"
}
```

> [!TIP]
> 選択リスト内の別の項目を別名として使用することはできません。 たとえば、次は許可されません`SELECT id, temp AS id...`。

結果は次の出力のようになります。

```json
{
  "results": [
    {
      "ID": "sample-002",
      "timestamp": "2021-09-10T11:40:29.188Z",
      "t": 40.20355053736378,
      "p": 79.26806508746755
    },
    {
      "ID": "sample-001",
      "timestamp": "2021-09-10T11:43:42.61Z",
      "t": 68.03536237975348,
      "p": 58.33517075380311
    }
  ]
}
```

### <a name="top"></a>TOP

クエリから返される結果の数を制限するには、 `TOP`を使用します。 たとえば、次のクエリでは最初の 10 件の結果が返されます。

```json
{
    "query": "SELECT TOP 10 $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

`TOP` を使用しない場合、クエリは最大 10,000 件の結果を返します。

`TOP` で返される結果の数を制限する前に並べ替えを行うには、 [ORDER BY](#order-by-clause) を使用します。

## <a name="from-clause"></a>FROM 句

`FROM` 句には、デバイス テンプレート ID が含まれている必要があります。 `FROM` 句は、クエリを実行するデバイスの種類を指定します。

デバイス テンプレート ID を見つけるには、IoT Central アプリケーションの **[デバイス]** ページに移動し、テンプレートを使用するデバイスをポイントします。 カードには、デバイス テンプレート ID が含まれています。

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="ページ URL でデバイス テンプレート ID を検索する方法を示すスクリーンショット。":::

また、[Devices - Get](/rest/api/iotcentral/1.1-previewdataplane/devices/get) REST API 呼び出しを使用して、デバイスのデバイス テンプレート ID を取得することもできます。

## <a name="where-clause"></a>WHERE 句

`WHERE` 句を使用すると、値とタイム ウィンドウを使用して結果をフィルター処理できます。

### <a name="time-windows"></a>時間枠

指定した時間内にアプリケーションによって受信されたテレメトリを取得するには、`WHERE` 句の一部として `WITHIN_WINDOW` を使用します。 たとえば、過去 1 日の温度と湿度のテレメトリを取得するには、次のクエリを使用します。

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

タイム ウィンドウの値には、 [ISO 8601 期間形式](https://en.wikipedia.org/wiki/ISO_8601#Durations) が使用されます。 次の表に例を示します。

| 例 | 説明                |
| ------- | -------------------------- |
| PT10M   | 過去 10 分間           |
| P1D     | 過去 1 日                   |
| P2DT12H | 過去 2 日間と 12 時間 |
| P1W     | 過去 1 週間                  |
| PT5H    | 過去 5 時間            |
| '2021-06-13T13:00:00Z/2021-06-13T15:30:00Z' | 特定の時間範囲 |

> [!NOTE]
> テレメトリのクエリを実行する場合にのみ、time ウィンドウを使用できます。

### <a name="value-comparisons"></a>値の比較

テレメトリまたはプロパティの値は、特定の値に基づいて取得できます。 たとえば、次のクエリは、温度が 0 より高く、圧力が 50 を超え、デバイス ID が **sample-002** および **sample-003** のどちらかの、すべてのメッセージを返します。

```json
{
  "query": "SELECT $id, $ts, temperature AS t, pressure AS p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50 AND $id IN ['sample-002', 'sample-003']"
}
```

次の演算子がサポートされています。

- 論理演算子 `AND` と `OR`。
- 比較演算子 `=`、`!=`、`>`、`<`、`>=`、`<=`、`<>`、`IN`。

> [!NOTE]
> `IN` 演算子は、テレメトリ と `$id` でのみ動作します。

`WHERE` 句には、次の制限が適用されます。

- 1 つのクエリで最大 10 個の演算子を使用できます。
- テレメトリ クエリでは、`WHERE` 句に含められるのはテレメトリ フィルターとデバイス メタデータ フィルターのみです。
- プロパティ クエリでは、`WHERE` 句に含められるのは、報告されるプロパティ、クラウド プロパティ、およびデバイス メタデータ フィルターのみです。

## <a name="aggregations-and-group-by-clause"></a>集計と GROUP BY 句

集計関数を使用すると、期間内のテレメトリ データの平均値、最大値、最小値などの値を計算できます。 たとえば、次のクエリでは、10 分間のウィンドウで `sample-001` デバイスから平均温度と湿度が計算されます。

```json
{
  "query": "SELECT AVG(temperature), AVG(pressure) FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND $id='{{DEVICE_ID}}' GROUP BY WINDOW(PT10M)"
}
```

結果は次のようにな出力になります。

```json
{
    "results": [
        {
            "$ts": "2021-09-14T11:40:00Z",
            "avg_temperature": 49.212146114456104,
            "avg_pressure": 48.590304135023764
        },
        {
            "$ts": "2021-09-14T11:30:00Z",
            "avg_temperature": 52.44844454703927,
            "avg_pressure": 52.25973211022142
        },
        {
            "$ts": "2021-09-14T11:20:00Z",
            "avg_temperature": 50.14626272506926,
            "avg_pressure": 48.98400386898757
        }
    ]
}
```

サポートされている集計関数は`SUM`、`MAX`、`MIN`、`COUNT`、`AVG`、`FIRST`、および `LAST` です。

`GROUP BY WINDOW` を使用してウィンドウ サイズを指定します。 `GROUP BY WINDOW` を使用しない場合、クエリは過去 30 日間のテレメトリを集計します。

> [!NOTE]
> 集計できるのはテレメトリ値のみです。

## <a name="order-by-clause"></a>ORDER BY 句

`ORDER BY` 句を使用すると、テレメトリ値、タイムスタンプ、またはデバイス ID でクエリ結果を並べ替えられます。 昇順または降順で並べ替えを行えます。 たとえば、次のクエリでは、最初に最新の結果が返されます。

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 ORDER BY timestamp DESC"
}
```

> [!TIP]
> `ORDER BY` と `TOP` を組み合わせて、並べ替え後にクエリが返す結果の数を制限します。

## <a name="limits"></a>制限

クエリの現在の制限は次のとおりです。

- `SELECT` 句リスト内の項目は 15 以下です。
- `WHERE` 句の論理操作は 10 以下 です。
- クエリは、最大 10,000 件のレコードを返します。
- クエリ文字列の長さは最大で 350 文字です。
- `SELECT` 句リストでワイルドカード ( `*` ) を使用することはできません。

## <a name="next-steps"></a>次の手順

これで REST API を使用してデバイスに対してクエリを実行する方法を学習したので、次のステップとしては「[IoT Central REST API を使用してユーザートロールを管理する方法](howto-manage-users-roles-with-rest-api.md)」をお勧めします。
