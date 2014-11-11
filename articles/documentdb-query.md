<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Azure DocumentDB を使用したクエリ

## 目的

DocumentDB の SQL クエリ言語では、ANSI SQL 文法のサブセットをサポートし、入れ子になったデータ構造、配列、およびオブジェクト構造の形式でドキュメント指向のサポートを追加します。クエリは、DocumentDB のインデックスを介して効率的に提供されます。インデックスはドキュメント内のすべてのプロパティに対して自動的に最新の状態になり、インデックス管理のオーバーヘッドはありません。また、開発者は、ユーザー定義関数 (UDF) を介してクエリ内部の JavaScript の能力を最大限活用することもできます。

## なぜ SQL クエリか

DocumentDB では、SQL により、データのクエリ実行に対する非常にシンプルで親しみやすいインターフェイスを提供できます。言語としての SQL は構造的にシンプルでありながらも強力で、アプリケーション開発者によって広範に使用されています。DocumentDB は、オープンなプラットフォームおよび標準 (HTTP、JSON、JavaScript、SQL など) を重視しています。
SQL クエリは、REST API、.NET、Node.js、JavaScript クライアント SDK を介してサポートされています。SQL クエリは、サーバー側 JavaScript API のストアド プロシージャおよびトリガーでもサポートされています。SQL クエリに加えて、DocumentDB は .NET SDK の LINQ を介したクエリもサポートしています。

# データ型

DocumentDB では、ドキュメントおよびメタデータのデータ形式は JavaScript Object Notation (JSON) で表現されます。JavaScript アプリケーションが成長し、プラットフォームに依存しないデータ交換形式にまで発展したため、JSON は広範に使用されています。JSON は自己記述型であるため、スキーマフリー データベースにも適しています。

DocumentDB クエリでサポートされているプリミティブ データ型は、JSON と同じです。JSON には、以下で構成されたシンプルな型システムがあります。

-   文字列
-   数値 (IEEE754 倍精度)
-   ブール値 – true と false
-   Null

さらに複雑なデータ型は、入れ子になったオブジェクトを { } 演算子で作成し、配列を [ ] 演算子で作成することにより、JSON と DocumentDB の両方で表現できます。

# クエリ文法

DocumentDB SQL 言語を使用すると、ユーザーは **SELECT** ステートメントを使用して JSON ドキュメントからデータを取得できます。ソーシャル ネットワークでの投稿を含むドキュメント コレクションに対するサンプル SQL ステートメントを、次に示します。

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

クエリの出力は、JSON ドキュメント フラグメントの配列です。指定された制約に一致する各ドキュメントに対して 1 つのフラグメントが含まれます。

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

SQL クエリ言語の正式な文法 (BNF) の全体は、このドキュメントの最後に含まれています。

# FROM 句

**FROM** 句は、一般的に、クエリが実行される現在のコレクションの名前です。たとえば、"posts" という名前のコレクション内のすべてのドキュメントを選択します。名前が正しくない場合は、エラーがユーザーに返されます。

    SELECT * 
    FROM posts

名前でコレクションを参照する代わりに **ROOT** キーワードを使用できます。SDK または REST API を介して、特定のコレクションの範囲を対象にクエリを実行できます。

    SELECT * 
    FROM ROOT

FROM の出力プロパティはエイリアス化できます。これは、**JOIN** を使用して複数の FROM ソースを使用するときに便利です。これについては後で説明します。

    SELECT P.* 
    FROM posts P

入れ子になったプロパティからクエリを実行することもできます。たとえば、投稿に関するドキュメントに投稿を作成したユーザーのサブドキュメントが含まれる場合は、次の構文を使用してクエリを実行できます。

    SELECT U.* 
    FROM posts.user U

FROM ステートメントのソースは、スカラー配列 (順序付けされたセット) にすることもできます。

    SELECT E
    FROM ["documentdb", "json", "sql"] E

スカラー式を評価するときは、FROM ステートメントを省略することもできます。

`SELECT "documentdb"`

# WHERE 句 (フィルター処理)

DocumentDB SQL 言語では、オプションの **WHERE** 句をサポートし、ドキュメントが満たす必要のある指定された条件に基づいた結果セットをフィルター処理できます。フィルター式には、ドキュメント内の任意のプロパティまたは入れ子になったパスに対する論理比較を含めることができます。

たとえば、クエリで message のような任意のプロパティを使用できます。フィルター処理は、DocumentDB の自動的なインデックスを使用して効率的に実行されます。

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

入れ子になったプロパティは、ドット形式の表記を使用してフィルター処理に使用できます。パスは、ドキュメントのスキーマに応じて任意の長さにできます。

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

配列内の各値は、配列の展開演算子を使用して参照できます。**IN** キーワードを使用して配列のアンワインドがサポートされます (後で説明します)。

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

範囲の比較 (\>、\<、\>=、\<=、!=) は数値に対してサポートされます。次に示すように、算術式の結果に対して数値を比較することもできます。

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

通常の SQL のように、AND、OR、および NOT 演算子を使用して論理演算子を組み合わせることができます。これらは、指定したプロパティに対するインデックスの積集合を介して効率的に処理されます。

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## 不明なプロパティと Null プロパティの処理

NULL はキーワードとしてサポートされ、他のスカラーのようにプロパティを NULL に対して比較できます。

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

ドキュメントのスキーマが急に変わる場合や事前に不明な場合があるため、この言語は不明なプロパティをチェックする特殊な演算子もサポートします。JavaScript の **undefined** に似ている ISUNDEFINED 演算子が使用されます。

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> 注: JavaScript と同様に、= undefined は常に false を返します。

# SELECT 句 (プロジェクション)

**SELECT** 句を使用して、特定のプロパティをクエリから抽出できます。スター (\*) 演算子は、クエリに一致する完全なドキュメントを返します。

    SELECT * 
    FROM posts

指定されたフィールドは SELECT 句で抽出できます。フィールドには、アトミックなフィールド (文字列、数値、ブール値) と複合フィールド (配列、JSON オブジェクト) があります。次の例の "user" など SELECT 句の複雑な型は、プロパティの下の JSON オブジェクト全体を返します。SELECT 句のプロパティは一意の名前またはエイリアスを持つ必要があります。

    SELECT posts.id, posts.user
    FROM posts

> 注: JSON は順序付けがないため、ドキュメント内の各プロパティは特定の順序では返されません。

> 注: フィールド名は明示的に修飾する必要があります。ドキュメントに固定スキーマがないため、これはクエリ ランタイムで適切なバインドを実行するために必要です。

プロパティにアクセスする代替手段は、次に示すように辞書検索構文を使用することです。

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> 注: 辞書検索構文を使用して、前の例の FROM のような予約語を使用するプロパティ名をエスケープする必要があります。

## 式評価

式では、スカラー式を使用することもできます。指定された名前がない場合は、自動生成された名前はプレースホルダーとして使用されます ($1、$2、$3 など)。

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

次の式は、SQL クエリ式内で使用できます。SQL 同様、演算子は厳密に型指定されています。たとえば、等号 (=) は強力な等値です。つまり、5 != "5" を意味します。

<table>
<tr>
<td>算術演算子</td>	
<td>+、-、\*、/、% (剰余)</td>
</tr>
<tr>
<td>論理演算子</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>ビット演算子</td>	
<td>& (ビット演算 AND), | (ビット演算 OR), ^ (ビット演算 XOR)</td>
</tr>
<tr>
<td>文字列演算子</td>	
<td>|| (連結)</td>
</tr>
<tr>
<td>比較演算子</td>	
<td>=, !=, >, <, >=, <=</td>
</tr>
</table>


## JSON の変換

SQL クエリは完全なドキュメントだけでなく、JSON フラグメントを返すことができます。そのために **VALUE** キーワードを使用できます。たとえば、次のドキュメントは {"id": "2"} の代わりに "2" を返します。

    SELECT VALUE posts.id
    FROM posts

JavaScript 同様、{} および [] 演算子を使用して、より複雑な JSON 式を構築できます。これにより、結果セットの形式を変換する柔軟性がクエリに備わります。

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> 注: 値には任意の有効な式を指定できますが、ラベルはリテラル (文字列) にする必要があります。

# 結合と反復

ドキュメント データベースで、参照されたデータはサブドキュメントまたは内部配列として埋め込まれます。したがって、SQL 言語では、**JOIN** キーワードを使用してドキュメント間結合 (自己結合) をサポートします。

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> 注: 結合は正式にはクロス結合ですが、データが埋め込まれているため、内部結合として動作します。

この例では、**IN** 演算子は、ソースのすべての要素にわたる反復に使用されます。IN 句のソースは、配列またはオブジェクトにすることができます。オブジェクトで使用される場合、反復子は各プロパティを経由します。IN は、次のように直接使用することもできます。IN は、配列リテラルで使用することもできます。

    SELECT tag 
    FROM tag IN posts.tags

次に示すように、SQL ステートメント内で複数の結合を使用できます。

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# ユーザー定義関数 (UDF)

DocumentDB クエリは、JavaScript ユーザー定義関数の形式でプログラム可能な拡張機能をサポートします。各コレクションは、JSON フラグメントを変換する 1 つ以上の保存された **UDF** を含むことができ、これらをスクリプト内で直接名前で参照します。

たとえば、create ユーザー定義関数を使用して、"regex\_match" 関数を次のように定義できます。

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

この例のように、クエリ内で参照できます。

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> 注: UDF は、入力パラメーターにのみアクセスできます。UDF 内で、DocumentDB 格納操作 (読み取り、書き込み、クエリなど) はサポートされません。

> 注: 現在、クエリ内では UDF を 1 つのみ使用できます。

UDF は、リテラルだけでなく、JSON フラグメントを処理して返すことができます。たとえば、"array\_count" 関数を次のように定義できます。

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

次に、ドキュメント内の配列のサイズを知るためにクエリ内で使用されます。

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

UDF をクエリの SELECT 句または FROM 句で指定することもできます。次に例を示します。

    SELECT array_count(p.likes) AS count
    FROM posts p

UDF の作成および管理の詳細については、JavaScript のプログラミングに関するドキュメントを参照してください。

# ページング

各クエリ実行は、クライアントにより構成されたページ サイズに応じて結果のバッチを返します。クエリのすべての結果を読み取るために、アプリケーションでは、読み取るデータがなくなるまで各結果セットをページ単位で読み込む必要があります。たとえば、クライアントは 10 個以内のドキュメントを読み取る場合、ページ サイズを 10 に変更して、返されるドキュメントの最大数を制限することができます。クエリで返される結果がページ サイズより少ない場合や、クエリによっては結果が返されない場合があることに注意してください。クエリからすべての結果を読み取るために、クライアントでは、応答継続トークンを使用して、空になるまで次のバッチを取得する必要があります。

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

この機能は基盤となるクライアント SDK/REST API の継続トークンを介して既に利用可能であるため、TOP または LIMIT キーワードとして SQL 文法には実装されません。その他の詳細と例については、REST API に関するドキュメント内の GET documents の説明を参照するか、SDK の ReadFeed メソッドを参照してください。

# クエリの一貫性動作

DocumentDB は、開発者が調整できる 4 種類の一貫性レベル (Strong、Bounded Staleness、Session、Eventual) をサポートします。クエリは、一貫性レベルと同じ保証を提供します。既定では、クエリの結果がクライアントにより要求された一貫性レベルに一致することが保証されます。DocumentDB のインデックスはログ構造化されており、量に関係なく、常に最新の状態でデータとの一貫性が保たれるように設計されています。ドキュメントが挿入または更新されると、それらはクエリ結果で即座に利用可能になります。

結果的な一貫性を必要とするアプリケーションの場合は、必要に応じて、インデックス遅延作成を使用するようにインデックス作成ポリシーを構成できます。この場合、コレクションがアイドル状態になっているときは、常に、結果的な一貫性方式でインデックスが更新されます。クエリの一貫性動作とさまざまなデータベース アカウント構成を、次の表にまとめます。

| データの一貫性    | インデックス作成ポリシー | クエリ動作        |
|-------------------|--------------------------|-------------------|
| Strong            | 一貫性                   | Strong            |
| Bounded Staleness | 一貫性                   | Bounded Staleness |
| Session           | 一貫性                   | Session           |
| Eventual          | 一貫性                   | Eventual          |
| Strong            | 遅延                     | Eventual          |
| Bounded Staleness | 遅延                     | Eventual          |
| Session           | 遅延                     | Eventual          |
| Eventual          | 遅延                     | Eventual          |

詳細については、インデックス作成ポリシーと構成に関するドキュメントを参照してください。

# API と SDK

クエリは、REST API、クライアント SDK、および SQL 文法を使用するサーバー側 JavaScript API で実行できます。

## REST API を使用したクエリ

アプリケーションは、REST API を使用し、コレクションに対してクエリの POST を実行できます。クエリ要求に以下を含める必要があります。

-   ヘッダー x-ms-documentdb-isquery: これがクエリであることを示す場合は True
-   ヘッダー Content-Type: SQL 言語が使用されることを示す場合は application/sql
-   SELECT ステートメントを含む本文
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization:...
    x-ms-continuation:
    x-ms-activity-id:82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date:Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id:9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery:True
    Cache-Control:no-cache
    x-ms-version:2014-02-25
    User-Agent:Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type:application/sql
    Host:...
    Content-Length:59
    Expect:100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> 注: GET またはクエリ文字列を使用するクエリはサポートされません。

## .NET SDK を使用するクエリ

.NET SDK では、SQL クエリが文字列としてサポートされる CreateDocumentQuery メソッドによるクエリ実行をサポートします。クエリの出力は、クライアント側 LINQ を使用して処理できる IQueryable インターフェイスです。

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

詳細については、.NET SDK に関するドキュメントを参照してください。

## ストアド プロシージャおよびトリガー内のクエリ

ストアド プロシージャおよびトリガーのサーバー側 JavaScript API は、SQL を使用するクエリ実行をサポートします。

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

詳細については、JavaScript サーバー側 SDK に関するドキュメントを参照してください。

# 付録 A – SQL 構文

次のレールロード ダイアグラムは、DocumentDB クエリ言語の正式な SQL 文法を示しています。

![][0]
![][1]
![][2]
![][3]
![][4]
![][5]
![][6]
![][7]
![][8]
![][9]
![][10]
![][11]
![][12]
![][13]
![][14]
![][15]
![][16]

  [0]: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
