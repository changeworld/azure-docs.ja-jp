---
title: マッピング データ フローでのデータ変換を解析する
description: 列の埋め込みドキュメントを解析します
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 05/10/2021
ms.openlocfilehash: 7f426c04793c754619cd40bfdfcb4a7b9fc7d904
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059912"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>マッピング データ フローでの変換を解析する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

変換解析を使用して、データ内のドキュメント形式の列を解析します。 現在サポートされている解析可能な埋め込みドキュメントの種類は、JSON、XML、区切りテキストです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>構成

変換解析の構成パネルで、最初に、インラインで解析する列に含まれているデータの種類を選択します。 変換解析には、次の構成設定も含まれています。

:::image type="content" source="media/data-flow/data-flow-parse-1.png" alt-text="解析の設定":::

### <a name="column"></a>列

派生列や集計と同様に、ここで、ドロップダウン ピッカーから選択して既存の列を変更できます。 または、ここに新しい列の名前を入力することもできます。 ADF により、解析されたソース データがこの列に格納されます。 ほとんどの場合は、受信する埋め込みドキュメント フィールドを解析する新しい列を定義します。

### <a name="expression"></a>Expression

式ビルダーを使用して、解析対象のソースを設定します。 これは、解析する自己完結型データが含まれたソース列を選択するだけで簡単に行うことができます。または、解析するための複雑な式を作成することもできます。

#### <a name="example-expressions"></a>式の例

* ソース文字列データ: ```chrome|steel|plastic```
  * 式: ```(desc1 as string, desc2 as string, desc3 as string)```

* ソース JSON データ: ```{"ts":1409318650332,"userId":"309","sessionId":1879,"page":"NextSong","auth":"Logged In","method":"PUT","status":200,"level":"free","itemInSession":2,"registration":1384448}```
  * 式: ```(level as string, registration as long)```

* ソース XML データ: ```<Customers><Customer>122</Customer><CompanyName>Great Lakes Food Market</CompanyName></Customers>```
  * 式: ```(Customers as (Customer as integer, CompanyName as string))```

### <a name="output-column-type"></a>出力列の種類

ここでは、1 つの列に書き込まれる、解析からのターゲット出力スキーマを構成します。

:::image type="content" source="media/data-flow/data-flow-parse-2.png" alt-text="解析例":::

この例では、受信フィールド "jsonString" の解析を定義しています。これはプレーンテキストですが、JSON 構造体として書式設定されています。 次のスキーマを使用して、解析結果を JSON として "json" という名前の新しい列に格納します。

`(trade as boolean, customers as string[])`

[検査] タブと [データのプレビュー] を参照して、出力が適切にマッピングされていることを確認します。

## <a name="examples"></a>使用例

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

### <a name="examples"></a>例

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json|XML|delimited',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>次のステップ

* [フラット化変換](data-flow-flatten.md)を使用して、行を列にピボットします。
* [派生列変換](data-flow-derived-column.md)を使用して、列を行にピボットします。
