---
title: Azure Data Explorer を使用したクエリ
titleSuffix: Azure Digital Twins
description: Azure Data Explorer 用のAzure Digital Twins クエリ プラグインについて理解します
author: baanders
ms.author: baanders
ms.date: 5/19/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8b0c6558be0022d8cb72ede5b665023f2b3f138d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438712"
---
# <a name="azure-digital-twins-query-plugin-for-azure-data-explorer"></a>Azure Data Explorer 用のAzure Digital Twins クエリ プラグイン

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) 用の Azure Digital Twins プラグインを使用すると、Azure Digital Twins グラフと Azure Data Explorer 時系列のデータベースのデータにアクセスして結合する Azure Data Explorer クエリを実行できます。 デジタル ツインとそれらの関係を推論して、モデル化された環境の動作に関する分析情報を得ることによって、異種の時系列データを＠コンテキスト化するには、このプラグインを使用します。

たとえば、このプラグインを使用すると、以下の処理を行う Kusto クエリを記述できます。
1. Azure Digital Twins クエリ プラグインを使用して目的のデジタル ツインを選択し、
2. それらのツインを Azure Data Explorer の各時系列に結合してから、 
3. それらのツインに対して高度な時系列分析を実行する。  

Azure Digital Twins のツイン グラフのデータと Azure Data Explorer の時系列データを組み合わせると、ソリューションのさまざまな部分の運用動作を理解するために役立ちます。 

## <a name="using-the-plugin"></a>プラグイン を使用する

次のコマンドを使用すると、Kusto クエリでプラグインを呼び出すことができます。 2 つのプレースホルダー (`<Azure-Digital-Twins-endpoint>` と `<Azure-Digital-Twins-query>`) があります。これらは、Azure Digital Twins インスタンスのエンドポイントと Azure Digital Twins クエリをそれぞれ表す文字列です。 

```kusto
evaluate azure_digital_twins_query_request(<Azure-Digital-Twins-endpoint>, <Azure-Digital-Twins-query>) 
```

このプラグインは、[Azure Digital Twins クエリ API](/rest/api/digital-twins/dataplane/query) を呼び出すことによって機能し、[クエリ言語構造](concepts-query-language.md)はこの API を使用する場合と同じですが、次の 2 つの例外があります。 
* `SELECT` 句の `*` ワイルドカードはサポートされていません。 このプラグインを使用して実行される Azure Digital Twin クエリには、代わりに `SELECT` 句で別名を使用する必要があります。

    たとえば、API を使用して実行される以下の Azure Digital Twins クエリを考えてみましょう。
    
    ```SQL
    SELECT * FROM DIGITALTWINS
    ```
    
    プラグインを使用してこのクエリを実行するには、次のように書き換える必要があります。
    
    ```SQL
    SELECT T FROM DIGITALTWINS T
    ```
* プラグインから返される列名は、`$` で始まらない場合があります。 `SELECT` 句に別名を使用することで、このシナリオを回避することもできます。

    たとえば、API を使用して実行される以下の Azure Digital Twins クエリを考えてみましょう。
    
    ```SQL
    SELECT T.$dtId, T.Temperature FROM DIGITALTWINS T
    ```
    
    プラグインを使用してこのクエリを実行するには、次のように書き換える必要があります。
    
    ```SQL
    SELECT T.$dtId as tid, T.Temperature FROM DIGITALTWINS T
    ```


>[!IMPORTANT]
>ユーザーの Azure AD トークンを使用して認証が行われるため、プラグインのユーザーに **Azure Digital Twins データ リーダー** ロールまたは **Azure Digital Twins データ所有者** ロールが付与されている必要があります。 このロールを割り当てる方法については、[Azure Digital Twins ソリューションのセキュリティ](concepts-security.md#authorization-azure-roles-for-azure-digital-twins)に関するページを参照してください。

このプラグインの使用方法の詳細については、[azure_digital_twins_query_request プラグインの Kusto ドキュメント](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)を参照してください。

サンプル クエリを参照し、サンプル データを使用してチュートリアルを完了するには、GitHub の [Azure Data Explorer 用 Azure Digital Twins クエリ プラグイン: サンプル クエリとチュートリアル](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries)に関するページを参照してください。

## <a name="using-azure-data-explorer-iot-data-with-azure-digital-twins"></a>Azure Data Explorer の IoT データを Azure Digital Twins で使用する

IoT データを Azure Data Explorer に取り込むにはさまざまな方法があります。 ここでは、Azure Digital Twins で Azure Data Explorer を使用するときに使う可能性のある 2 つの方法について説明します。
* ツイン変更イベントを処理してツイン データを Azure Data Explorer に書き込む Azure 関数を使用して、デジタル ツインのプロパティ値を Azure Data Explorer に履歴化する。これは、[Azure Time Series Insights と統合する](how-to-integrate-time-series-insights.md)に関するページで使用されるプロセスと似ています。 このパスは、テレメトリ データを使用してデジタル ツインを利用するお客様に適しています。
* [IoT Hub または他のソースから Azure Data Explorer クラスターに IoT データを直接取り込みます](/azure/data-explorer/ingest-data-iot-hub)。 その後、共同の Azure Digital Twins または Azure Data Explorer のクエリを使用して時系列データをコンテキスト化するために、Azure Digital Twins グラフが使用されます。 このパスは、直接取り込みワークロードに適している場合があります。 

### <a name="mapping-data-across-azure-data-explorer-and-azure-digital-twins"></a>Azure Data Explorer と Azure Digital Twins 間のデータのマッピング

時系列データを Azure Data Explorer に直接取り込む場合は、この生の時系列データを共同の Azure Digital Twins または Azure Data Explorer のクエリに適したスキーマに変換する必要があります。

Azure Data Explorer の[更新ポリシー](/azure/data-explorer/kusto/management/updatepolicy) を使用すると、新しいデータがソース テーブルに挿入されるたびに、データを自動的に変換してターゲット テーブルに追加できます。 

更新ポリシーを使用すると、生の時系列データを Azure Digital Twins の対応する **ツイン ID** でエンリッチして、ターゲット テーブルに保持できます。 ツイン ID を使用すると、その後、Azure Digital Twins プラグインによって選択されたデジタル ツインにターゲット テーブルを結合できます。 

たとえば、Azure Data Explorer インスタンスに流入する生の時系列データを保持するために、次のテーブルを作成したとします。 

```kusto
.create-merge table rawData (Timestamp:datetime, someId:string, Value:string, ValueType:string)  
```

マッピング テーブルを作成すると、時系列 ID とツイン ID、およびその他の省略可能なフィールドを関連付けることができます。 

```kusto
.create-merge table mappingTable (someId:string, twinId:string, otherMetadata:string) 
```

次に、エンリッチされた時系列データを保持するターゲット テーブルを作成します。 

```kusto
.create-merge table timeseriesSilver (twinId:string, Timestamp:datetime, someId:string, otherMetadata:string, ValueNumeric:real, ValueString:string)  
```

次に、生データをマッピング テーブルと結合してエンリッチする `Update_rawData` 関数を作成します。 これにより、ツイン ID が結果のターゲット テーブルに追加されます。 

```kusto
.create-or-alter function with (folder = "Update", skipvalidation = "true") Update_rawData() { 
rawData 
| join kind=leftouter mappingTable on someId 
| project 
    Timestamp, ValueNumeric = toreal(Value), ValueString = Value, ... 
} 
```

最後に、この関数を呼び出してターゲット テーブルを更新する更新ポリシーを作成します。 

```kusto
.alter table timeseriesSilver policy update 
@'[{"IsEnabled": true, "Source": "rawData", "Query": "Update_rawData()", "IsTransactional": false, "PropagateIngestionProperties": false}]' 
```

ターゲット テーブルが作成されたら、Azure Digital Twins プラグインを使用して、目的のツインを選択し、ターゲット テーブル内の時系列データと結合できます。 

### <a name="example-schema"></a>スキーマの例

共有データを表すために使用できるスキーマの例を次に示します。

| timestamp | twinId | modelId | name | value | relationshipTarget | relationshipID |
| --- | --- | --- | --- | --- | --- | --- |
| 2021-02-01 17:24 | ConfRoomTempSensor | dtmi:com:example:TemperatureSensor;1 | 温度 | 301.0 |  |  |

デジタル ツインのプロパティは、キーと値のペア (`name, value`) として格納されます。 `name` と `value` は動的データ型として格納されます。 

スキーマでは、`relationshipTarget` および `relationshipID` フィールドごとのリレーションシップのプロパティの格納もサポートされます。 キーと値のスキーマにより、ツインのプロパティごとに列を作成する必要がなくなります。

### <a name="representing-properties-with-multiple-fields"></a>複数のフィールドを持つプロパティを表す 

複数のフィールドを持つスキーマにプロパティを格納する場合があります。 これらのプロパティは、JSON オブジェクトを `value` としてスキーマに格納することで表されます。

たとえば、roll、pitch、yaw の 3 つのフィールドを持つプロパティを表す場合、値オブジェクトは `{"roll": 20, "pitch": 15, "yaw": 45}` のようになります。

## <a name="next-steps"></a>次のステップ

* Azure Data Explorer での Kusto クエリ言語のプラグイン ドキュメントを確認する: [azure_digital_twins_query_request プラグイン](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)

* このプラグインを使用したサンプル クエリを確認する (サンプル シナリオでクエリを実行するチュートリアルを含む): [Azure Data Explorer 用の Azure Digital Twins クエリ プラグイン: サンプル クエリとチュートリアル](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries) 

* Azure Digital Twins の履歴データを分析するための別の方法を参照する: [Azure Time Series Insights と統合する](how-to-integrate-time-series-insights.md)
