---
title: Azure Cosmos DB インデックス作成ポリシー | Microsoft Docs
description: Azure Cosmos DB のインデックス作成のしくみを説明します。 インデックス作成の自動化とパフォーマンス向上のために、インデックス作成ポリシーを構成および変更する方法について説明します。
keywords: インデックス作成のしくみ, 自動インデックス作成, インデックス作成データベース
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: d7cbeebff42bddd93cac35a0205d031a90bb4715
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144637"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Azure Cosmos DB のデータ インデックス作成のしくみ

既定では、Azure Cosmos DB のすべてのデータに対してインデックスが作成されます。 ユーザーの多くは Azure Cosmos DB を使用して インデックス作成のすべての機能を自動的に処理していますが、Azure Cosmos DB で、コレクション用のカスタム *インデックス作成ポリシー*を作成時に指定することもできます。 Azure Cosmos DB のインデックス作成ポリシーは、他のデータベースプラットフォームで提供されているセカンダリ インデックスよりも柔軟で強力です。 Azure Cosmos DB では、スキーマの柔軟性を損なうことなく、インデックスの構造を設計およびカスタマイズできます。 

Azure Cosmos DB でのインデックス作成のしくみを知るには、インデックス作成ポリシーを管理するときに、インデックスのストレージ オーバーヘッド、書き込みとクエリのスループット、クエリの整合性のバランスをきめ細かく調整できることを理解することが重要です。  

次の動画では、Azure Cosmos DB プログラム マネージャー、Andrew Liu が Azure Cosmos DB の自動インデックス作成機能と、Azure Cosmos DB コンテナーでインデックス作成ポリシーを調整し、構成する方法を紹介しています。 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

この記事では、Azure Cosmos DB インデックス作成ポリシー、インデックス作成ポリシーのカスタマイズの方法、関連するトレードオフの詳細について説明します。 

この記事を読むと、次の質問に回答できるようになります。

* インデックス作成に含めるプロパティまたは除外するプロパティはどのようにオーバーライドすればよいか。
* 最終的な更新のインデックスはどのように構成すればよいか。
* ORDER BY クエリまたは範囲クエリを実行できるようにインデックス作成を構成するにはどうすればよいか。
* コレクションのインデックス作成ポリシーに変更を加えるにはどうすればよいか。
* さまざまなインデックス作成ポリシーのストレージとパフォーマンスを比較するにはどうすればよいか。

## <a id="Indexing"></a> Cosmos DB のインデックス作成

データベース インデックスの目的は、さまざまな形式のクエリを処理しながら、リソース (CPU、入力/出力など) の消費量を最小化し、スループットを高め、遅延時間を少なくすることです。 多くの場合、データベース クエリにおける適切なインデックスの選択には、入念な計画と長期間の試行錯誤が必要とされます。 データが厳密なスキーマに準拠せず、データ量も急速に増大するスキーマのないデータベースの場合、このアプローチは現実的ではありません。 

このため、マイクロソフトでは、以下の目標に従って Cosmos DB のインデックス サブシステムを設計しました。

* スキーマを必要としないインデックス ドキュメント: インデックス作成サブシステムは、スキーマ情報を一切必要とせず、ドキュメントのスキーマを想定しません。  

* 高度な階層化に対応した、効率的なリレーショナル クエリのサポート: インデックスは Cosmos DB クエリ言語を効率的にサポートします。これには、階層型かつリレーショナルなプロジェクションのサポートも含まれます。  

* 一定量かつ持続的に実行される書き込みに対する、一貫性のあるクエリのサポート: 一貫性のあるクエリで書き込みスループットの負荷が高くなる場合は、一定量の書き込みが持続的に実行されたとしても、インデックスは効率的かつ段階的にオンラインで更新されます。 ユーザーがドキュメント サービスを構成した際の一貫性レベルでクエリを処理するためには、インデックスの一貫した更新が欠かせません。  

* マルチテナントのサポート: テナント間のリソースは、予約ベースのモデルで制御されます。インデックスの更新は、これに従って、レプリカごとに割り当てられたシステム リソース (CPU、メモリ、および 1 秒あたりの入力/出力操作) 量の範囲内で実行されます。  

* ストレージの効率性: コスト効率性を実現するため、インデックスのディスク上のストレージ オーバーヘッドは、有限かつ予測可能なものにします。 これは、開発者がインデックスのオーバーヘッドとクエリのパフォーマンスを比較して、コストに基づくトレードオフを Cosmos DB で実施できるようになるためには必須です。  

## コレクション <a id="CustomizingIndexingPolicy"></a> のインデックス作成ポリシーをカスタマイズする  
Azure Cosmos DB コレクションで既定のインデックス作成ポリシーをオーバーライドすることで、ストレージ、書き込みやクエリのパフォーマンス、クエリの整合性の間のバランスをカスタマイズできます。 次の部分を構成できます。

* **インデックスにドキュメントとパスを含めるか、除外するか**。 コレクションにドキュメントを挿入するとき、または置き換えるときに、インデックスの特定のドキュメントを除外する、または含めることができます。 *パス*と呼ばれる特定の JSON プロパティを含めるか除外して、インデックスに含まれるドキュメント全体のインデックスを作成することもできます。 パスにはワイルドカード パターンが含まれます。
* **さまざまなインデックスの種類を構成する**。 含めるパスごとに、パスがコレクションに必要とするインデックスの種類を指定できます。 パスのデータ、予想されるクエリの作業負荷、および数値/文字列の "有効桁数" に基づいて、インデックスの種類を指定できます。
* **インデックスの更新モードを構成する**。 Azure Cosmos DB は、同期 (Consistent)、非同期 (Lazy)、なし (None) の 3 つのインデックス モードをサポートしています。 Azure Cosmos DB コレクションのインデックス作成ポリシーを使用してインデックス作成モードを構成できます。 

次の Microsoft .NET コード スニペットは、コレクションの作成時に、カスタムのインデックス作成ポリシーを設定する方法を示します。 この例では、最大有効桁数で文字列や数値の範囲のインデックスを持つポリシーを設定します。 このポリシーを使用して、文字列に対して ORDER BY クエリを実行することができます。

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> インデックス作成ポリシーの JSON スキーマは、REST API バージョン 2015-06-03 のリリースで変更されました。 このリリースで、インデックス作成ポリシーの JSON スキーマは文字列に対する範囲インデックスをサポートしています。 .NET SDK の 1.2.0、および Java、Python、Node.js SDK の 1.1.0 では新しいポリシー スキーマをサポートします。 以前のバージョンの SDK では、REST API バージョン 2015-04-08 が使用されています。 これらのバージョンは、インデックス作成ポリシーに以前のスキーマをサポートしています。
> 
> 既定では、Azure Cosmos DB はハッシュ インデックスに応じたドキュメント内のすべての文字列プロパティのインデックスを作成します。 ドキュメント内のすべての数値プロパティは、一貫して範囲インデックスを使用してインデックスが作成されます。  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>ポータルでインデックス作成ポリシーをカスタマイズする

Azure Portal でコレクションのインデックス作成ポリシーを変更できます。 

1. ポータルで Azure Cosmos DB アカウントに移動し、コレクションを選択します。 
2. 左側のナビゲーション メニューで **[設定]** を選択し、**[インデックス作成ポリシー]** を選択します。 
3. **[インデックス作成ポリシー]** で、インデックス作成ポリシーを変更し、**[OK]** を選択します。 

### データベース インデックス作成モード <a id="indexing-modes"></a>  
Azure Cosmos DB は、Azure Cosmos DB コレクションのインデックス作成ポリシーを使用して構成できる、同期 (Consistent)、非同期 (Lazy)、なし (None) の 3 つのインデックス作成モードをサポートしています。

**同期 (Consistent)**: Azure Cosmos DB コレクションのポリシーが同期 (Consistent) の場合、特定の Azure Cosmos DB コレクションに対するクエリは、そのポイントの読み取りに指定された同じ整合性レベル (strong、bounded-staleness、session、eventual) に従います。 インデックスは、ドキュメントの更新 (Azure Cosmos DB コレクション内のドキュメントの挿入、置換、更新、削除など) の一部として、同期的に更新されます。

同期 (Consistent) インデックス作成では、書き込みスループットの短縮によって、整合性のあるクエリがサポートされます。 この短縮は、インデックスを作成する必要がある一意のパスの機能と "整合性レベル" によるものです。 同期 (Consistent) インデックス作成モードは、"すばやく書き込み、即座にクエリ" するワークロード向けです。

**非同期 (Lazy)**: このインデックスは、Azure Cosmos DB コレクションが休止 (コレクションのスループット容量がユーザー要求の処理に完全に利用されていない) 状態の間は非同期的に更新されます。  データの取り込みとインデックス作成が遅いため、整合性のない結果が得られる可能性があることに注意してください。 これは、COUNT 個のクエリまたは特定のクエリ結果が、特定の時点で整合性または反復性がない可能性があることを意味します。 

通常、取り込んだデータのインデックスはキャッチアップ モードです。 非同期 (Lazy) インデックスを使用すると、TTL (Time to Live) の変更によってインデックスが削除され、再作成されます。 これにより、一定期間、COUNT とクエリ結果が一致しなくなります。 ほとんどの Azure Cosmos DB アカウントでは、同期 (Consistent) インデックス モードを使用する必要があります。

**なし (None)**: なし (None) インデックス モードを含むコンテナー レジストリには、インデックスが関連付けられていません。 これは、キー値のストレージとして Azure Cosmos DB が使用され、ドキュメントがそれぞれの ID プロパティによってのみアクセスされる場合によく使用されます。 

> [!NOTE]
> インデックス作成ポリシーをなし (None) に設定すると、既存のインデックスが削除されるいう影響があります。 アクセス パターンに ID または自己リンクのみが必要な場合は、このオプションを使用します。
> 
> 

次の表は、コレクション用に構成されたインデックス作成モード (同期 (Consistent) と非同期 (Lazy)) に基づいたクエリの一貫性と、クエリ要求に指定された一貫性レベルを示します。 これは、任意のインターフェイス (REST API、SDK) を使用して作成されたクエリや、ストアド プロシージャやトリガーから作成されたクエリに適用されます。 

|整合性|インデックス作成モード: 同期 (Consistent)|インデックス作成モード: 非同期 (Lazy)|
|---|---|---|
|Strong|Strong|Eventual|
|Bounded staleness|Bounded staleness|Eventual|
|Session|Session|Eventual|
|Eventual|Eventual|Eventual|

Azure Cosmos DB は、インデックス作成モードがなし (None) に設定されたコレクションにクエリが実行されるとエラーを返します。 REST API の明示的な **x-ms-documentdb-enable-scan** ヘッダー、または .NET SDK で **EnableScanInQuery** 要求オプションを使用して、スキャンとしてクエリを実行することもできます。 ORDER BY などの一部のクエリ機能は、**EnableScanInQuery** を使用したスキャンとしてサポートされていません。

次の表は、**EnableScanInQuery** を指定した場合のインデックス作成モード (同期 (Consistent)、非同期 (Lazy)、なし (None)) に基づいたクエリの整合性を示します

|整合性|インデックス作成モード: 同期 (Consistent)|インデックス作成モード: 非同期 (Lazy)|インデックス作成モード: なし (None)|
|---|---|---|---|
|Strong|Strong|Eventual|Strong|
|Bounded staleness|Bounded staleness|Eventual|Bounded staleness|
|Session|Session|Eventual|Session|
|Eventual|Eventual|Eventual|Eventual|

次に示すコード サンプルは、すべてのドキュメントの挿入に整合性のあるインデックス作成モードで .NET SDK を使用して Azure Cosmos DB コレクションを作成する方法を示します。

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>インデックスのパス
Azure Cosmos DB は JSON ドキュメントとインデックスをツリーとしてモデル化します。 ツリー内のパスのポリシーに合わせて調整できます。 ドキュメント内では、インデックス作成に含めるか除外するパスを選択できます。 これにより、クエリのパターンが事前にわかっている場合に、書き込みパフォーマンスの向上とインデックス ストレージの削減が可能になります。

インデックスのパスはルート (/) で始まり、通常、 ? ワイルドカード演算子で終わります。 これは、プレフィックスに複数の可能な値があることを示します。 たとえば、SELECT * FROM Families F WHERE F.familyName = "Andersen" を処理するには、コレクションのインデックス ポリシーに /familyName/? の インデックスのパスを含める必要があります。

インデックスのパスは \* ワイルドカード演算子を使用して、プレフィックスの下のパスの動作を再帰的に指定することもできます。 たとえば、/payload/* を使用して、ペイロード プロパティの下に属するものをすべてインデックス作成から除外できます。

インデックスのパスを指定するための一般的なパターンは次のとおりです。

| Path                | 説明またはユース ケース                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | コレクションの既定のパス。 再帰的で、ドキュメント ツリー全体に適用されます。                                                                                                                                                                                                                                   |
| /prop/?             | 次のようなクエリを処理するために必要なインデックスのパス (ハッシュまたは範囲の各種類)。<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /"prop"/*             | 指定されたラベルの下のすべてのパスのインデックスのパス。 次のようなクエリで使用<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | ["a", "b", "c"] のようなスカラーの配列に対して、反復クエリと JOIN クエリを処理するために必要なインデックスのパス。<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | [{subprop: "a"}, {subprop: "b"}] のようなオブジェクトの配列に対して、反復クエリと JOIN クエリを処理するために必要なインデックスのパス。<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | 次のクエリを処理するために必要なインデックスのパス (ハッシュまたは範囲の各種類)。<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> カスタム インデックスのパスを設定するときに、特定のパス "/*" で表されるドキュメント ツリー全体の既定のインデックス作成ルールを指定する必要があります。 
> 
> 

次の例では、範囲インデックス作成と 20 バイトのカスタムの有効桁数を使用して、特定のパスを構成します。

```
    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);
```

インデックス付けのためにパスが追加されると、それらのパス内の数字と文字列の両方にインデックスが付けられます。 そのため、文字列のインデックス付けのみを定義していても、Azure Cosmos DB によって数字の既定の定義も追加されます。 言い換えると、Azure Cosmos DB には、インデント付けポリシーからパスを除外する機能がありますが、特定のパスから型を除外する機能はありません。 以下は一例です。両方のパス (Path = "/*" および Path = "/\"attr1\"/?") に対して 1 つのインデックスのみが指定されますが、Number データ型も結果に追加されます。

```
var indices = new[]{
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 }// <- note: only 1 index specified
                    },
                    Path =  "/*"
                },
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 } // <- note: only 1 index specified
                    },
                    Path =  "/\"attr1\"/?"
                }
            };...

            foreach (var index in indices)
            {
                documentCollection.IndexingPolicy.IncludedPaths.Add(index);
            }
```

インデックス作成の結果:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        },
        {
            "path": "/\"attr\"/?",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        }
    ],
}
```

### <a name="index-data-types-kinds-and-precisions"></a>インデックスのデータ型、種類、および有効桁数
パスのインデックス作成ポリシーを構成するときは、複数のオプションがあります。 すべてのパスに対して 1 つ以上のインデックス作成定義を指定することができます。

* **データ型**: String、Number、Point、Polygon、LineString (各パスのデータ型ごとにエントリを 1 つだけ含めることができます)。
* **インデックスの種類**: Hash (等値クエリ)、Range (等値、範囲、ORDER BY クエリ)、Spatial (空間クエリ)
* **有効桁数**: ハッシュ インデックスの場合、文字列と数値の両方で 1 から 8 の範囲です。 既定値は 3 です。 範囲インデックスの場合、この値に -1 (最大有効桁数) を指定できます。 文字列または数値の場合、1 から 100 (最大有効桁数) の範囲で変更できます。

#### <a name="index-kind"></a>インデックスの種類
Azure Cosmos DB は、String、Number、またはその両方のデータ型に対して構成できるすべてのパスに対して、ハッシュ インデックスと範囲インデックスの種類をサポートします。

* **Hash** は効率的な等値クエリと JOIN クエリをサポートします。 ほとんどのユース ケースでは、ハッシュ インデックスに既定値 3 バイトより高い有効桁数は不要です。 データ型には、String または Number を指定できます。
* **範囲**は効率的な等値クエリ、範囲クエリ (>、<、>=、<=、!= を使用)、ORDER BY クエリをサポートします。 既定では ORDER BY クエリには、インデックスの最大有効桁数 (-1) も必要です。 データ型には、String または Number を指定できます。

Azure Cosmos DB では、Point、Polygon、または LineString データ型に対して指定できる、すべてのパスに対するインデックスの種類 Spatial もサポートしています。 指定されたパスの値は、`{"type": "Point", "coordinates": [0.0, 10.0]}` のような有効な GeoJSON フラグメントである必要があります。

* **Spatial** では、効率的な空間クエリ (within と distance) をサポートしています。 データ型には、Point、Polygon、または LineString を指定できます。

> [!NOTE]
> Azure Cosmos DB は、Point、Polygon、LineString データ型の自動インデックス作成をサポートしています。
> 
> 

サポートされているインデックスの種類とそれに対して使用可能なクエリの例を示します。

| インデックスの種類 | 説明またはユース ケース                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash を /prop/? (または/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash を /props/[]/?  (または / または /props/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Range      | Range を /prop/? (または/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Range を /prop/? (または/) に使用して、以下のクエリを効率的に処理することができます。<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) -- ポイントに対するインデックス作成が有効になっている場合<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) -- ポリゴンに対するインデックス作成が有効になっている場合              |

既定では、範囲インデックス (有効桁数は任意) が存在しない場合、>= のような範囲演算子を使用したクエリに対してはエラーが返され、このクエリを処理するにはスキャンが必要であることが通知されます。 **x-ms-documentdb-enable-scan** ヘッダーを REST API で使用するか、または .NET SDK を使用した **EnableScanInQuery** 要求オプションで使用すれば、範囲インデックスがなくても、範囲クエリを実行することができます。 Azure Cosmos DB でインデックスを使用してフィルター処理できるその他のフィルターがクエリにない場合、エラーは返されません。

同じ規則が空間クエリにも適用されます。 既定では、空間インデックスがなく、インデックスからサービスを提供できる他のフィルターがない場合、空間クエリにエラーが返されます。 これらは、**x-ms-documentdb-enable-scan** または **EnableScanInQuery** を使用して、スキャンとして実行できます。

#### <a name="index-precision"></a>インデックスの有効桁数
インデックスの有効桁数を使用して、インデックスのストレージ オーバーヘッドとクエリのパフォーマンスをトレードオフできます。 数値の場合、既定の有効桁数の構成 -1 (最大) を使用することをお勧めします。 JSON では数値は 8 バイトであるため、これは、8 バイトの構成と同じです。 1 から 7 などの小さい値の有効桁数を選択すると、特定の範囲内の値が同じインデックス エントリにマップされることになります。 そのため、インデックスのストレージ領域は減りますが、クエリの実行で処理するドキュメントが増える可能性があります。 結果として、要求単位でより多くのスループットが消費されます。

インデックスの有効桁数の構成は、文字列の範囲を使用するとより実際に役立ちます。 文字列は任意の長さにできるため、インデックスの有効桁数を選択することで、文字列の範囲クエリのパフォーマンスに影響する可能性があります。 また、必要なインデックスのストレージ領域の量に影響する可能性もあります。 文字列の範囲インデックスは 1 から 100 または -1 (最大) で構成できます。 文字列のプロパティに ORDER BY クエリを実行する場合は、対応するパスに有効桁数 -1 を指定する必要があります。

空間インデックスでは、常にすべての型 (Point、LineString、Polygon) 用の既定のインデックスの有効桁数を使用します。 空間インデックスの場合、既定のインデックスの有効桁数はオーバーライドはできません。 

次の例では、.NET SDK を使用して、コレクション内の範囲インデックスの有効桁数を増やす方法を示します。 

**カスタム インデックスの有効桁数でコレクションを作成する**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> クエリが ORDER BY を使用しているのに、最大有効桁数でのクエリ パスに対して範囲インデックスが存在しない場合、Azure Cosmos DB はエラーを返します。 
> 
> 

同様に、パスをインデックス作成から完全に除外することもできます。 次の例では、\* ワイルドカード演算子を使用して、ドキュメントのセクション全体 (*サブツリー*) をインデックス作成から除外する方法を示します。

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>インデックス作成のオプトインとオプトアウト
コレクションがすべてのドキュメントのインデックスを自動的に作成するかどうかを選択できます。 既定では、すべてのドキュメントのインデックスが自動的に作成されますが、自動インデックス作成を無効にすることができます。 自動インデックス作成が無効になっている場合、自己リンクまたはドキュメント ID を使用したクエリでのみドキュメントにアクセスできます。

自動インデックス作成が無効になっている場合でも、特定のドキュメントだけを選択してインデックスに追加できます。 反対に、自動インデックス作成を有効にしたまま、特定のドキュメントを選択して除外できます。 インデックス作成の有効または無効の構成は、ドキュメントのサブセットだけにクエリを実行する必要がある場合に役立ちます。

次の例は、[SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) と [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) プロパティを使用して、明示的にドキュメントを含める方法を示しています。

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>コレクションのインデックス作成ポリシーを変更する
Azure Cosmos DB では、その場でコレクションのインデックス作成ポリシーを変更できます。 Azure Cosmos DB コレクションのインデックス作成ポリシーを変更すると、インデックスの構造が変わる可能性があります。 この変更は、インデックスを作成できるパス、その有効桁数、およびインデックス自体の整合性モデルに影響します。 インデックス作成ポリシーを変更するには、以前のインデックスを新しいインデックスに変換する必要があります。 

**オンラインのインデックスの変換**

![インデックス作成のしくみ - Azure Cosmos DB オンライン インデックス変換](./media/indexing-policies/index-transformations.png)

インデックスの変換はオンラインで行われます。 つまり、コレクションの*書き込み可用性またはプロビジョニング済みスループットに影響を与えずに*、古いポリシーごとにインデックスが作成されたドキュメントを効率的に変換できます。 インデックス変換中、SDK、REST API を使ったり、ストアド プロシージャやトリガー内から確立された読み取り操作と書き込み操作の整合性に影響はありません。 

インデックス作成ポリシーの変更は非同期プロセスであり、操作の完了までにかかる時間はドキュメントの数、プロビジョニングされた RU、およびドキュメントのサイズによって異なります。 インデックス再作成の進行中は、変更されているインデックスをクエリが使用する場合、一致するすべての結果がクエリから返されないことがあります。また、エラーもクエリから返されません。 インデックス再作成の進行中は、インデックス作成モードの構成が同期 (Consistent) か非同期 (Lazy) かに関係なく、最終的にクエリの一貫性は保たれます。 インデックス変換の完了後も、引き続き一貫性のある結果が表示されます。 これは、任意のインターフェイス (REST API、SDK) を使用して作成されたクエリや、ストアド プロシージャやトリガーから作成されたクエリにも適用されます。 非同期 (Lazy) インデックス作成と同様に、インデックスの変換は、特定のレプリカで利用可能なスペア リソースを使用して、バックグラウンドで非同期的に行われます。 

インデックスの変換も適切に行われます。 Azure Cosmos DB はインデックスのコピーを 2 つ保持せず、以前のインデックスを新しいインデックスに置き換えます。 これは、インデックス変換の実行中に追加のディスク領域が必要ないことを意味します。また、コレクションで使用されることもありません。

インデックス作成ポリシーを変更すると、インデックス作成モードの構成に基づいて、以前のインデックスから新しいインデックスに移行するための変更が適用されます。 インデックス作成モードの構成は、含めるパスや除外するパス、インデックスの種類、有効桁数などの他の値よりも大きな役割を果たします。 

以前のポリシーと新しいポリシーの両方で同期 (Consistent) インデックス作成を使用する場合、Azure Cosmos DB はオンラインでインデックス変換を実行します。 変換の進行中は、同期 (Consistent) インデックス作成モードで別のインデックス作成ポリシーの変更を適用することはできません。 ただし、変換の進行中に、非同期 (Lazy) や、なし (None) インデックス作成モードに移動することは可能です。 

* 非同期 (Lazy) に移行すると、インデックス作成ポリシーの変更はすぐに有効になります。 Azure Cosmos DB でインデックスの再作成が非同期に開始されます。 
* なし (None) に移行すると、インデックスはすぐに削除されます。 なし (None) への移行は、進行中の変換を中止して別のインデックス作成ポリシーを最初から開始する場合に便利です。 

次のコード スニペットは、コレクションのインデックス作成ポリシーを同期 (Consistent) から非同期 (Lazy) インデックス モードに変更する方法を示しています。 .NET SDK を使用している場合は、新しい **ReplaceDocumentCollectionAsync** メソッドを使用してインデックス作成ポリシーの変更を開始できます。

**インデックス作成ポリシーを同期 (Consistent) から非同期 (Lazy) に変更する**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**インデックス変換の進行状況の追跡**

**ReadDocumentCollectionAsync** 呼び出しの **IndexTransformationProgress** 応答プロパティを使用すると、同期 (Consistent) インデックスへのインデックス変換の進行率を追跡できます。 その他の SDK と REST API は、インデックス作成ポリシーを変更するための同等のプロパティとメソッドをサポートします。 インデックス変換の進行状況は、**ReadDocumentCollectionAsync** を呼び出すことで確認できます。 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * **IndexTransformationProgress** プロパティは、同期 (Consistent) インデックスに変換する場合にのみ適用できます。 非同期 (Lazy) インデックスへの変換を追跡するには、**ResourceResponse.LazyIndexingProgress** プロパティを使用します。
> * **IndexTransformationProgress** プロパティと **LazyIndexingProgress** プロパティは、パーティ ション分割されていないコレクション、つまりパーティ ション キーなしで作成されたコレクションに対してのみ設定されます。
>

コレクションのインデックスを削除するには、なし (None) インデックス作成モードに移動します。 これは、進行中の変換をキャンセルして、新しい変換をすぐに開始する場合に便利な運用ツールです。

**コレクションのインデックスの削除**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Azure Cosmos DB コレクションに対してインデックス作成ポリシーを変更するのはどのような場合でしょうか。 一般的な使用例を次に示します。

* 通常の操作では一貫した結果を使用し、データの一括インポート時には非同期 (Lazy) インデックス作成モードに戻す場合。
* 現在の Azure Cosmos DB コレクションで新しいインデックス作成機能を使い始める場合。 たとえば、空間インデックスの種類が必要な地理空間クエリや、文字列の範囲インデックスの種類が必要な ORDER BY/文字列の範囲クエリを使用できます。
* インデックスが作成されるプロパティを手動で選択し、時間の経過と共に変更する場合。
* インデックス作成時の有効桁数を調整してクエリのパフォーマンスを向上するか、消費されているストレージを減らす場合。

> [!NOTE]
> **ReplaceDocumentCollectionAsync** を使用してインデックス作成ポリシーを変更するには、バージョン 1.3.0 以降の.NET SDK を使用する必要があります。
> 
> インデックス変換を正常に完了するために、コレクションに十分な空きストレージ空間があることを確認してください。 コレクションがそのストレージ クォータに到達すると、インデックス変換が一時停止します。 ストレージ空間が利用可能になると、たとえば、ドキュメントをいくつか削除した場合、インデックス変換は自動的に再開します。
> 
> 

## <a name="performance-tuning"></a>パフォーマンスのチューニング
SQL API は使用されているインデックス ストレージ、すべての操作のスループット コスト (要求単位) などのパフォーマンス メトリックに関する情報を提供します。 この情報は、さまざまなインデックス作成ポリシーの比較やパフォーマンス チューニングに使用することができます。

コレクションの記憶域のクォータと使用状況を確認するには、**HEAD** 要求または **GET** 要求をコレクションのリソースに対して実行します。 次に、**x-ms-request-quota** および **x-ms-request-usage** ヘッダーを調べます。 .NET SDK では、[ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) の [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) プロパティと [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) プロパティに、これらの対応する値が含まれています。

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


書き込み操作 (作成、更新、削除) のたびにインデックス作成のオーバーヘッドを測定するには、**x-ms-request-charge** ヘッダー (または、.NET SDK の [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) の同等の [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) プロパティ) を調査して、これらの操作で使用される要求単位の数を測定します。

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>インデックス作成ポリシーの仕様の変更
インデックス作成ポリシーにおけるスキーマの変更は、2015 年 7 月 7 日に、REST API のバージョン 2015-06-03 により導入されました。 SDK バージョン内の該当するクラスには、そのスキーマに適合する新しい実装が含まれています。 

JSON 仕様に次の変更が実装されました。

* インデックス作成ポリシーでは文字列に対して範囲インデックスをサポートします。
* 各パスのインデックス定義は複数であってもかまいません。 各データ型に 1 つを定義できます。
* インデックス作成時の有効桁数は、数字に対する 1 から 8、文字列に対する 1 から 100、および -1 (最大有効桁数) に対応します。
* パス セグメントに、各パスをエスケープするための二重引用符は必要ありません。 たとえば、パス **/title/?** を追加する場合、 **/"title"/?** とする必要はありません。
* "すべてのパス" を表すルート パスは、(**/** に加えて) **/\*** で表現できます。

バージョン 1.1.0 以前の.NET SDK で作成されたカスタム インデックス作成ポリシーを使用してコレクションをプロビジョニングするコードがある場合、SDK バージョン 1.2.0 に移行するには、アプリケーション コードを変更して変更内容に対応する必要があります。 インデックス作成ポリシーを構成するコードがない場合、または以前の SDK バージョンを引き続き使用する場合、変更の必要はありません。

実用的な比較として、REST API バージョン 2015-06-03 を使用して作成したカスタム インデックス作成ポリシーの例と、以前の REST API バージョン 2015-04-08 を使用して作成した同じインデックス作成ポリシーを紹介します。

**現在のインデックス作成ポリシー JSON (REST API バージョン 2015-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**以前のインデックス作成ポリシー JSON (REST API バージョン 2015-04-08)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>次の手順
インデックス ポリシー管理のサンプルや Azure Cosmos DB のクエリ言語の詳細については、以下のリンクを参照してください。

* [SQL API .NET のインデックス管理のコード サンプル](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [SQL API REST コレクション操作](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [SQL を使用したクエリ](sql-api-sql-query.md)

