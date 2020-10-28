---
title: ツイン グラフにクエリを実行する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins ツイン グラフに対してクエリを実行する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea12b3eb72ce05f2672f6ca0912cc67345413c3c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461279"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital Twins ツイン グラフに対してクエリを実行する

この記事では、[Azure Digital Twins クエリ言語](concepts-query-language.md)を使用して[ツイン グラフ](concepts-twins-graph.md)に対してクエリを実行する方法の例を示し、詳細に説明します。 グラフに対してクエリを実行するには、Azure Digital Twins の [**Query API**](/rest/api/digital-twins/dataplane/query) を使用します。

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

この記事の残りの部分では、これらの操作を使用する方法の例を示します。

## <a name="query-syntax"></a>クエリ構文

このセクションのサンプル クエリでは、クエリ言語の構造を示し、可能なクエリ操作を[デジタル ツイン](concepts-twins-graph.md)で実行します。

### <a name="show-all-existing-digital-twins"></a>すべての既存のデジタル ツインを表示する

次に示すのは、インスタンス内のすべてのデジタル ツインの一覧を返す基本的なクエリです。

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>上位の項目を選択する

`Select TOP` 句を使用して、1 つのクエリで複数の "上位" 項目を選択できます。

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>項目をカウントする

`Select COUNT` 句を使用して、結果セット内の項目の数をカウントできます。

```sql
SELECT COUNT() 
FROM DIGITALTWINS
``` 

`WHERE` 句を追加することで、特定の条件に一致する項目の数をカウントします。 ツイン モデルの種類に基づいて適用されたフィルターを使用してカウントする例をいくつか次に示します (この構文の詳細については、後述する [ *「モデルでクエリを実行する」*](#query-by-model)を参照してください)。

```sql
SELECT COUNT() 
FROM DIGITALTWINS 
WHERE IS_OF_MODEL('dtmi:sample:Room;1') 
SELECT COUNT() 
FROM DIGITALTWINS c 
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

また、`COUNT` を `JOIN` 句と共に使用することもできます。 次に、ルーム 1 と 2 のライト パネルに含まれるすべての電球をカウントするクエリを示します。

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="specify-return-set-with-projections"></a>プロジェクションを使用して戻り値のセットを指定する

プロジェクションを使用すると、クエリによって返される列を選択できます。 

>[!NOTE]
>現時点では、複合プロパティはサポートされていません。 プロジェクションのプロパティが有効であることを確認するには、`IS_PRIMITIVE` チェックでプロジェクションを結合します。 

プロジェクションを使用してツインとリレーションシップを返すクエリの例を次に示します。 次のクエリでは、ID が " *ABC* " の " *Factory* " が " *Factory.customer* " のリレーションシップを介して " *Consumer* " と関連付けられており、そのリレーションシップが " *Edge* " としてプロジェクションされるシナリオから、" *Consumer* "、" *Factory* "、" *Edge* " がプロジェクションされています。

```sql
SELECT Consumer, Factory, Edge 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
```

また、プロジェクションを使用してツインのプロパティを返すこともできます。 以下のクエリでは、" *Factory.customer* " のリレーションシップを介して、ID が " *ABC* " の " *Factory* " に関連付けられている " *Consumers* " の " *Name* " プロパティがプロジェクションされています。 

```sql
SELECT Consumer.name 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Consumer.name)
```

また、プロジェクションを使用してリレーションシップのプロパティを返すこともできます。 前述の例と同様に、以下のクエリでは、" *Factory.customer* " のリレーションシップを介して、ID が " *ABC* " の " *Factory* " に関連付けられている " *Consumers* " の " *Name* " プロパティがプロジェクションされていますが、今回は、そのリレーションシップの 2 つのプロパティである *prop1* および *prop2* も返されます。 これは、リレーションシップに " *Edge* " という名前を付け、そのプロパティを収集することで行います。  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

また、別名を使用して、プロジェクションによるクエリを簡略化することもできます。

次のクエリを実行すると前述の例と同じ操作が行われますが、プロパティ名には別名の `consumerName`、`first`、`second`、および `factoryArea` が使用されています。 
 
```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)" 
```

これは、上記と同じセットに対してクエリを実行する類似のクエリですが、" *Consumer.name* " プロパティのみが `consumerName` としてプロジェクションされ、完全な " *Factory* " はツインとしてプロジェクションされます。 

```sql
SELECT Consumer.name AS consumerName, Factory 
FROM DIGITALTWINS Factory 
JOIN Consumer RELATED Factory.customer Edge 
WHERE Factory.$dtId = 'ABC' 
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) 
```

### <a name="query-by-property"></a>プロパティで照会

**プロパティ** (ID とメタデータを含む) を指定してデジタル ツインを取得します。
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> デジタル ツインの ID のクエリ実行には、メタデータ フィールド `$dtId` を使用します。

**特定のプロパティが定義されているかどうか** に基づいて Twins を取得することもできます。 次は、 *Location* プロパティが定義されているツインを取得するクエリです。

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

「 [デジタル ツインにタグを追加する](how-to-use-tags.md)」で説明されているように、" *タグ* " プロパティを使用してツインを取得する場合に役立ちます。 次は、 *red* のタグが付いているツインをすべて取得するクエリです。

```sql
select * from digitaltwins where is_defined(tags.red) 
```

**プロパティの型** に基づいてツインを取得することもできます。 次は、 *Temperature* プロパティが数字であるツインを取得するクエリです。

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>モデルでクエリを実行する

`IS_OF_MODEL` 演算子を使用すると、ツインの [**モデル**](concepts-models.md)に基づいてフィルター処理できます。 継承がサポートされており、いくつかのオーバーロード オプションがあります。

`IS_OF_MODEL` の最も簡単な使用法では、`IS_OF_MODEL(twinTypeName)` のように `twinTypeName` パラメーターのみを受け取ります。
このパラメーターで値を渡すクエリの例を次に示します。

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

(`JOIN` が使用されている場合のように) 複数のツイン コレクションがある場合に検索対象を指定するには、`IS_OF_MODEL(twinCollection, twinTypeName)` のように `twinCollection` パラメーターを追加します。
このパラメーターに値を追加するクエリの例を次に示します。

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

完全一致を行うには、`IS_OF_MODEL(twinTypeName, exact)` のように `exact` パラメーターを追加します。
このパラメーターに値を追加するクエリの例を次に示します。

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

また、`IS_OF_MODEL(twinCollection, twinTypeName, exact)` のように 3 つの引数すべてを渡すこともできます。
3 つのパラメーターすべての値を指定するクエリの例を次に示します。

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>リレーションシップに基づくクエリ

デジタル ツインの **リレーションシップ** に基づいてクエリを実行する場合、Azure Digital Twins クエリ言語には特殊な構文があります。

リレーションシップは `FROM` 句のクエリ スコープにプルされます。 "従来の" SQL 型言語との重要な違いは、この `FROM` 句の各式がテーブルではないことです。そうではなく、`FROM` 句はエンティティ間のリレーションシップのトラバーサルを表し、`JOIN` の Azure Digital Twins バージョンを使用して記述されます。 

Azure Digital Twins の[モデル](concepts-models.md)機能では、ツインと無関係のリレーションシップは存在しないことを思い出してください。 つまり、Azure Digital Twins ストア言語の `JOIN` は、一般的な SQL の `JOIN` とは少し異なります。リレーションシップのクエリを無関係に実行することはできず、ツインに関連付ける必要があります。
この違いを組み込むために、キーワード `RELATED` を `JOIN` 句に使用して、ツインのリレーションシップのセットを参照します。 

次のセクションでは、このような例をいくつか示します。

> [!TIP]
> 概念的に、この機能は、ドキュメント内の子オブジェクトに対して `JOIN` を実行できるという CosmosDB のドキュメント中心の機能を模倣しています。 CosmosDB では、`IN` キーワードを使用して、`JOIN` が現在のコンテキスト ドキュメント内の配列要素を反復処理する意図を示します。

#### <a name="relationship-based-query-examples"></a>リレーションシップベースのクエリの例

リレーションシップを含むデータセットを取得するには、1 つの `FROM` ステートメントに続けて N 個の `JOIN` ステートメントを使用します。ここで、`JOIN` ステートメントは前の `FROM` または `JOIN` ステートメントの結果に関するリレーションシップを表します。

リレーションシップベースのクエリの例を次に示します。 このコード スニペットを実行すると、 *ID* プロパティが 'ABC' であるすべてのデジタル ツインと、 *contains* リレーションシップを介してこれらのデジタル ツインに関連するすべてのデジタル ツインが選択されます。 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> 開発者は、この `JOIN` を `WHERE` 句のキー値と関連付ける (または `JOIN` 定義を使用してインラインでキー値を指定する) 必要はありません。 リレーションシップのプロパティ自体でターゲット エンティティが識別されるため、この相関関係はシステムによって自動的に計算されます。

#### <a name="query-the-properties-of-a-relationship"></a>リレーションシップのプロパティのクエリを実行する

デジタル ツインに DTDL を介して記述されるプロパティが存在するのと同様に、リレーションシップにプロパティを含めることもできます。 **リレーションシップのプロパティに基づいて** ツインにクエリを実行できます。
Azure Digital Twins ストア言語を使用すると、`JOIN` 句内のリレーションシップにエイリアスを割り当てることで、リレーションシップのフィルター処理とプロジェクションを行うことができます。 

例として、 *reportedCondition* プロパティがある *servicedBy* リレーションシップを考えてみます。 次のクエリでは、プロパティを参照するために、このリレーションシップには 'R' という別名が与えられています。

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

上記の例では、 *reportedCondition* が *servicedBy* リレーションシップ自体のプロパティであることに注意してください ( *servicedBy* リレーションシップを持つ何らかのデジタル ツインではありません)。

### <a name="query-with-multiple-joins"></a>複数の JOIN を使用したクエリ

現在、プレビューの段階では、1 つのクエリで最大 5 つの `JOIN` がサポートされています。 これにより、一度に複数のレベルのリレーションシップを走査することができます。

次に、複数結合のクエリの例を示します。このクエリでは、ルーム 1 と 2 のライトパネルに含まれるすべての電球が取得されます。

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1') 
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1') 
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="other-compound-query-examples"></a>その他の複合クエリの例

結合演算子を使用して上記のクエリ タイプを **組み合わせ** 、1 つのクエリに含める詳細を増やすことができます。 次に、複数のツイン記述子タイプに対して一度にクエリを実行する複合クエリの追加例をいくつか挙げます。

| 説明 | クエリ |
| --- | --- |
| *Room 123* に与えられているデバイスの中から、Operator の役割を担う MxChip デバイスが返されます | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| ID が *id1* の別のツインとの間に *Contains* という名前のリレーションシップがあるツインを取得します | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| *floor11* によって包含されるこの部屋モデルのすべての部屋を取得します | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>リファレンス: 式と条件

このセクションには、Azure Digital Twins のクエリを記述するときに使用できる演算子と関数のリファレンスが含まれています。

### <a name="operators"></a>オペレーター

次の演算子がサポートされています。

| ファミリ | オペレーター |
| --- | --- |
| 論理 |AND、OR、NOT |
| 比較 |=、!=、<、>、<=、>= |
| 内容 | IN、NIN |

### <a name="functions"></a>関数

次の型チェックとキャスト関数がサポートされます。

| 機能 | 説明 |
| -------- | ----------- |
| IS_DEFINED | プロパティに値が代入されているかどうかを示すブール値を返します。 これは、値がプリミティブ型である場合にのみサポートされます。 プリミティブ型には、文字列、ブール値、数値、または `null` が含まれます。 DateTime、オブジェクト型、配列はサポートされていません。 |
| IS_OF_MODEL | 指定したツインが指定したモデルの種類と一致するかどうかを示すブール値を返します。 |
| IS_BOOL | 指定した式の型がブール値であるかどうかを示すブール値を返します。 |
| IS_NUMBER | 指定した式の型が数値であるかどうかを示すブール値を返します。 |
| IS_STRING | 指定した式の型が文字列であるかどうかを示すブール値を返します。 |
| IS_NULL | 指定した式の型が null であるかどうかを示すブール値を返します。 |
| IS_PRIMITIVE | 指定した式の型がプリミティブ (文字列、ブール値、数値、または `null`) であるかどうかを示すブール値を返します。 |
| IS_OBJECT | 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。 |

次の文字列関数がサポートされます。

| 機能 | 説明 |
| -------- | ----------- |
| STARTSWITH(x, y) | 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。 |
| ENDSWITH(x, y) | 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。 |

## <a name="run-queries-with-an-api-call"></a>API 呼び出しを使用してクエリを実行する

クエリ文字列を決定したら、 **Query API** を呼び出して実行します。
次のコード スニペットは、クライアント アプリからのこの呼び出しを示しています。

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

この呼び出しからは、QueryResult オブジェクトの形式でクエリ結果が返されます。 

クエリ呼び出しはページングをサポートしています。 エラー処理とページングを含む完全な例を次に示します。

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>クエリの制限事項

インスタンスの変更がクエリに反映されるまでに最大 10 秒の遅延が発生する場合があります。 たとえば、DigitalTwins API を使用してツインの作成や削除などの操作が完了した場合、その結果が Query API 要求にすぐに反映されないことがあります。 解決するには、少しの間待つだけです。

プレビュー段階の `JOIN` の使用にはその他にも制限があります。
* `FROM` ステートメント内ではサブクエリはサポートされていません。
* `OUTER JOIN` セマンティクスはサポートされていません。つまり、リレーションシップのランクがゼロの場合、"行" 全体が出力結果セットから削除されます。
* プレビュー段階では、グラフ トラバーサルの深さがクエリごとに 5 `JOIN` レベルに制限されます。
* `JOIN` 操作のソースは制限されています。クエリでは、クエリが開始されるツインを宣言する必要があります。

## <a name="query-best-practices"></a>クエリのベスト プラクティス

Azure Digital Twins でクエリを実行する際のヒントを次に示します。

* モデル設計段階でクエリ パターンを検討します。 1 つのクエリで回答する必要があるリレーションシップが、単一レベルのリレーションシップとしてモデル化されていることを確認します。
* グラフのトラバーサルから大きな結果セットが生成されない方法でプロパティを設計します。
* 必要なクエリ数を大幅に減らすには、ツインの配列を構築し、`IN` 演算子を使用してクエリを実行します。 たとえば、" *Buildings* "(建物) に " *Floors* " (フロア) が含まれ、" *Floors* " に " *Rooms* " (部屋) が含まれるシナリオを考えてみます。 建物内の暑い部屋を見つけるには、次の方法があります。

    1. `contains` のリレーションシップに基づいて建物のフロアを見つけます
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. 部屋を見つけるには、フロアを 1 つずつ検討し、`JOIN` クエリを実行してそれぞれの部屋を見つけるのではなく、建物内のフロアのコレクション (以下のクエリでは *Floor* という名前) を使用してクエリを実行することができます。

        クライアント アプリ:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        クエリ:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* プロパティの名前と値では大文字と小文字が区別されるため、モデルで定義されている正確な名前を使用するよう注意してください。 プロパティ名のスペルが間違っているか、大文字と小文字が正しくない場合、結果セットは空になり、エラーは返されません。

## <a name="next-steps"></a>次のステップ

この記事のクエリの実行に使用されるクエリ API を含め、詳細については、[Azure Digital Twins API および SDK](how-to-use-apis-sdks.md) に関するページを参照してください。
