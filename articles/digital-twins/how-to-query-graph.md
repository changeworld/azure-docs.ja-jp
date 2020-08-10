---
title: ツイン グラフにクエリを実行する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins ツイン グラフに対してクエリを実行する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 05bcbf8df695ba308a6eaff5e7401f0a6d638747
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337604"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital Twins ツイン グラフに対してクエリを実行する

この記事では、[Azure Digital Twins クエリ ストア言語](concepts-query-language.md)を使用して[ツイン グラフ](concepts-twins-graph.md)に対してクエリを実行する方法の例を示し、詳細に説明します。 グラフに対してクエリを実行するには、Azure Digital Twins の [**Query API**](how-to-use-apis-sdks.md) を使用します。

## <a name="query-syntax"></a>クエリ構文

ここでは、いくつかのサンプル クエリを使ってクエリ言語の構造を示し、考えられるクエリ操作を実行します。

プロパティ (ID とメタデータを含む) を指定して[デジタル ツイン](concepts-twins-graph.md)を取得します。
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

[モデル](concepts-models.md)を指定してデジタル ツインを取得します
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> デジタル ツインの ID のクエリ実行には、メタデータ フィールド `$dtId` を使用します。

### <a name="query-based-on-relationships"></a>リレーションシップに基づくクエリ

デジタル ツインのリレーションシップに基づいてクエリを実行する場合、Azure Digital Twins クエリ ストア言語には特殊な構文があります。

リレーションシップは `FROM` 句のクエリ スコープにプルされます。 "従来の" SQL 型言語との重要な違いは、この `FROM` 句の各式がテーブルではないことです。そうではなく、`FROM` 句はエンティティ間のリレーションシップのトラバーサルを表し、`JOIN` の Azure Digital Twins バージョンを使用して記述されます。 

Azure Digital Twins の[モデル](concepts-models.md)機能では、ツインと無関係のリレーションシップは存在しないことを思い出してください。 つまり、Azure Digital Twins クエリ ストア言語の `JOIN` は、一般的な SQL の `JOIN` とは少し異なります。リレーションシップのクエリを無関係に実行することはできず、ツインに関連付ける必要があるためです。
この違いを組み込むために、キーワード `RELATED` を `JOIN` 句に使用して、ツインのリレーションシップのセットを参照します。 

次のセクションでは、このような例をいくつか示します。

> [!TIP]
> 概念的に、この機能は、ドキュメント内の子オブジェクトに対して `JOIN` を実行できるという CosmosDB のドキュメント中心の機能を模倣しています。 CosmosDB では、`IN` キーワードを使用して、`JOIN` が現在のコンテキスト ドキュメント内の配列要素を反復処理する意図を示します。

#### <a name="relationship-based-query-examples"></a>リレーションシップベースのクエリの例

リレーションシップを含むデータセットを取得するには、1 つの `FROM` ステートメントに続けて N 個の `JOIN` ステートメントを使用します。ここで、`JOIN` ステートメントは前の `FROM` または `JOIN` ステートメントの結果に関するリレーションシップを表します。

リレーションシップベースのクエリの例を次に示します。 このコード スニペットを実行すると、*ID* プロパティが 'ABC' であるすべてのデジタル ツインと、*contains* リレーションシップを介してこれらのデジタル ツインに関連するすべてのデジタル ツインが選択されます。 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> 開発者は、この `JOIN` を `WHERE` 句のキー値と関連付ける (または `JOIN` 定義を使用してインラインでキー値を指定する) 必要はありません。 リレーションシップのプロパティ自体でターゲット エンティティが識別されるため、この相関関係はシステムによって自動的に計算されます。

#### <a name="query-the-properties-of-a-relationship"></a>リレーションシップのプロパティのクエリを実行する

デジタル ツインに DTDL を介して記述されるプロパティが存在するのと同様に、リレーションシップにプロパティを含めることもできます。 Azure Digital Twins クエリ ストア言語を使用すると、`JOIN` 句内のリレーションシップにエイリアスを割り当てることで、リレーションシップのフィルター処理とプロジェクションを行うことができます。 

例として、*reportedCondition* プロパティがある *servicedBy* リレーションシップを考えてみます。 次のクエリでは、プロパティを参照するために、このリレーションシップには 'R' という別名が与えられています。

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

上記の例では、*reportedCondition* が *servicedBy* リレーションシップ自体のプロパティであることに注意してください (*servicedBy* リレーションシップを持つ何らかのデジタル ツインではありません)。

## <a name="run-queries-with-an-api-call"></a>API 呼び出しを使用してクエリを実行する

クエリ文字列を決定したら、**Query API** を呼び出して実行します。
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
* パブリック プレビュー段階では、グラフのトラバーサルの深さが制限されます。クエリごとに使用できる `JOIN` は 1 つだけです。
* `JOIN` 操作のソースは制限されています。クエリでは、クエリが開始されるツインを宣言する必要があります。

## <a name="query-best-practices"></a>クエリのベスト プラクティス

Azure Digital Twins でクエリを実行する際のヒントを次に示します。

* モデル設計段階でクエリ パターンを検討します。 1 つのクエリで回答する必要があるリレーションシップが、単一レベルのリレーションシップとしてモデル化されていることを確認します。
* グラフのトラバーサルから大きな結果セットが生成されない方法でプロパティを設計します。
* 必要なクエリ数を大幅に減らすには、ツインの配列を構築し、`IN` 演算子を使用してクエリを実行します。 たとえば、"*Buildings*"(建物) に "*Floors*" (フロア) が含まれ、"*Floors*" に "*Rooms*" (部屋) が含まれるシナリオを考えてみます。 建物内の暑い部屋を見つけるには、次の方法があります。

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
