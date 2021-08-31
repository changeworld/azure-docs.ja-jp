---
title: マルチモデル機能
description: Microsoft Azure SQL では、同じデータベース内の複数のデータ モデルを使用できます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, urmilano
ms.date: 12/17/2018
ms.openlocfilehash: 93005f665f816f0f6dcde3a1c06748622f4bf271
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585827"
---
# <a name="multi-model-capabilities-of-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance のマルチモデル機能
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

マルチモデル データベースを使用すると、リレーショナル データ、グラフ、JSON または XML ドキュメント、空間データ、キーと値のペアなどの複数の形式で表されているデータを格納したり、処理したりすることができます。

[Azure SQL 製品群](azure-sql-iaas-vs-paas-what-is-overview.md)では、さまざまな汎用アプリケーションに対して最適なパフォーマンスを提供するリレーショナル モデルが使用されています。 ただし、Azure SQL Database や SQL Managed Instance などの Azure SQL 製品はリレーショナル データに限定されるわけではありません。 それらを使用すると、リレーショナル モデルに緊密に統合される非リレーショナル形式を使用できます。

以下の場合に、Azure SQL のマルチモデル機能の使用を検討します。

- NoSQL モデルに適した情報または構造はあるが、別個の NoSQL データベースを使用したくない。
- 大多数のデータがリレーショナル モデルに適しており、データの一部を NoSQL スタイルでモデル化する必要がある。
- Transact-SQL 言語を使用してリレーショナル データと NoSQL データの両方に対するクエリと分析を実行した後、そのデータを SQL 言語を使用できるツールやアプリケーションと統合したい。
- 分析のパフォーマンスや NoSQL データ構造の処理を向上させるために、[メモリ内テクノロジ](in-memory-oltp-overview.md)などのデータベース機能を適用したい。 [トランザクション レプリケーション](managed-instance/replication-transactional-overview.md)または[読み取り可能レプリカ](database/read-scale-out.md)を使用して、データのコピーを作成し、一部の分析ワークロードをプライマリ データベースからオフロードできます。

以下のセクションで、Azure SQL の最も重要なマルチモデル機能について説明します。

> [!Note]
> 同じ Transact-SQL クエリ内で JSONPath 式、XQuery/XPath 式、空間関数、およびグラフ クエリ式を使用して、データベースに格納したデータにアクセスできます。 Transact-SQL クエリを実行できるツールまたはプログラミング言語では、そのクエリ インターフェイスを使用してマルチモデル データにアクセスすることもできます。 これが、[Azure Cosmos DB](../cosmos-db/index.yml) などの、データ モデル用の特別な API が用意されているマルチモデル データベースとの重要な違いです。

## <a name="graph-features"></a>グラフ機能

Azure SQL 製品では、データベース内の多対多リレーションシップをモデル化するグラフ データベース機能が提供されています。 グラフは、ノード (頂点) とエッジ (リレーションシップ) のコレクションです。 ノードは、エンティティ (人や組織など) を表します。 エッジは、それによって接続される 2 つのノード間のリレーションシップ (例: 好意や友人など) を表します。 

グラフ データベースを特徴付けるいくつかの特性を次に示します。

- エッジは、グラフ データベース内のファーストクラスのエンティティです。 属性またはプロパティを関連付けることができます。
- 1 つのエッジで、グラフ データベース内の複数のノードを柔軟に接続できます。
- パターン マッチングとマルチホップ ナビゲーション クエリを簡単に表現できます。
- 推移閉包およびポリモーフィック クエリを簡単に表現できます。

[グラフ リレーションシップとグラフ クエリ機能](/sql/relational-databases/graphs/sql-graph-overview)は Transact-SQL に統合されており、基本的なデータベース管理システムとして SQL Server データベース エンジンを使用することから恩恵を受けます。 グラフ機能では、グラフの `MATCH` 演算子で拡張された標準の Transact-SQL クエリを使用して、グラフ データに対するクエリが実行されます。

リレーショナル データベースでは、グラフ データベースで可能なことをすべて実現できます。 しかし、グラフ データベースでは、特定のクエリをより簡単に表現することができます。 次の要因に基づいて、どちらを選択するかを決定できます。

- 1 つのノードで複数の親を持つことができる階層データをモデル化する必要があるため、[hierarchyId データ型](/sql/t-sql/data-types/hierarchyid-data-type-method-reference)を使用できない。
- アプリケーションに、複雑な多対多リレーションシップがある。 アプリケーションが進化するにつれて、新しいリレーションシップが追加されます。
- 相互接続されたデータとリレーションシップを分析する必要があります。
- グラフ固有の T-SQL 検索条件 ([SHORTEST_PATH](/sql/relational-databases/graphs/sql-graph-shortest-path) など) を使用したい。

## <a name="json-features"></a>JSON 機能

Azure SQL 製品では、[JavaScript Object Notation (JSON)](https://www.json.org/) 形式で表されているデータの解析とクエリ、およびリレーショナル データの JSON テキストとしてのエクスポートが可能です。 [JSON](/sql/relational-databases/json/json-data-sql-server) は SQL Server データベース エンジンの中核となる機能です。

JSON 機能を使用すると、JSON ドキュメントのテーブルへの格納、リレーショナル データの JSON ドキュメントへの変換、および JSON ドキュメントのリレーショナル データへの変換を実行できます。 ドキュメントを解析するために、JSON 機能で拡張された標準の Transact-SQL 言語を使用できます。 非クラスター化インデックス、列ストア インデックス、またはメモリ最適化テーブルを使用して、クエリを最適化することもできます。

JSON は、最新の Web とモバイル アプリケーションでデータ交換を行うために使用される一般的なデータ形式です。 JSON は、ログ ファイルや NoSQL データベースに半構造化データを格納するためにも使用されます。 REST Web サービスの多くは結果を JSON テキスト形式で返し、データを JSON 形式で受け取ります。 

ほとんどの Azure サービスには、JSON を返すか使用する REST エンドポイントがあります。 これらのサービスには、[Azure Cognitive Search](https://azure.microsoft.com/services/search/)、[Azure Storage](https://azure.microsoft.com/services/storage/)、および [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) が含まれます。

JSON テキストがある場合、組み込み関数 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql)、[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)、および [ISJSON](/sql/t-sql/functions/isjson-transact-sql) を使用して、JSON からデータを抽出したり、JSON が適切に書式設定されていることを確認したりすることができます。 その他の関数は次のとおりです。

- [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql): JSON テキスト内の値を更新できます。 
- [OPENJSON](/sql/t-sql/functions/openjson-transact-sql): 高度なクエリと分析のために、JSON オブジェクトの配列を行セットに変換できます。 すべての SQL クエリを返された結果セットで実行することができます。 
- [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server): リレーショナル テーブルに格納されるデータを JSON テキストとしてフォーマットできます。

![JSON 関数を示す図。](./media/multi-model-features/image_1.png)

詳細については、[JSON データを処理する方法](database/json-features.md)に関するページを参照してください。 

次の特定のシナリオでは、リレーショナル モデルではなくドキュメント モデルを使用できます。

- オブジェクトのすべてのフィールドに一度にアクセスするため、スキーマの高度な正規化を行っても大きなメリットが得られない、またはオブジェクトの正規化された部分を更新することがない。 ただし、正規化されたモデルでは、データを取得するために結合する必要があるテーブルの数が多いため、クエリの複雑さが増します。
- 通信やデータ モデルで JSON ドキュメントをネイティブに使用するアプリケーションを使用しているが、リレーショナル データから JSON へ、またはその逆の変換を行うレイヤーをさらに導入したくない。
- 子テーブルまたはエンティティ/オブジェクト/値パターンの正規化を解除することで、データ モデルを簡略化する必要がある。
- データを解析する追加のツールなしで、JSON 形式で格納されているデータを読み込んだりエクスポートしたり必要がある。

## <a name="xml-features"></a>XML 機能

XML 機能では、データベースに XML データを格納してインデックスを作成し、ネイティブの XQuery/XPath 演算を使用して XML データを処理することができます。 Azure SQL 製品には、XML データを処理する特別な XML データ型とクエリ関数が組み込まれています。

SQL Server データベース エンジンには、半構造化データを管理するアプリケーションを開発するための強力なプラットフォームが用意されています。 [XML のサポート](/sql/relational-databases/xml/xml-data-sql-server)は、データベース エンジンのすべてのコンポーネントに統合されており、以下が含まれます。

- XML 値を XML データ型列にネイティブに格納する機能。XML スキーマのコレクションに従って型指定することも、型指定なしのままにすることもできます。 XML 列のインデックスを作成できます。
- XML 型の列と変数に格納されている XML データに対する XQuery クエリを指定する機能。 データベースで使用するデータ モデルにアクセスするすべての Transact-SQL クエリで XQuery 機能を使用できます。
- [プライマリ XML インデックス](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)を使用した XML ドキュメント内のすべての要素の自動インデックス作成。 または、[セカンダリ XML インデックス](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)を使用して、インデックスを作成する必要がある正確なパスを指定できます。
- `OPENROWSET`。XML データを一括読み込みできます。
- リレーショナル データを XML 形式に変換する機能。

次の特定のシナリオでは、リレーショナル モデルではなくドキュメント モデルを使用できます。

- オブジェクトのすべてのフィールドに一度にアクセスするため、スキーマの高度な正規化を行っても大きなメリットが得られない、またはオブジェクトの正規化された部分を更新することがない。 ただし、正規化されたモデルでは、データを取得するために結合する必要があるテーブルの数が多いため、クエリの複雑さが増します。
- 通信やデータ モデルで XML ドキュメントをネイティブに使用するアプリケーションを使用しているが、リレーショナル データから JSON へ、またはその逆の変換を行うレイヤーをさらに導入したくない。
- 子テーブルまたはエンティティ/オブジェクト/値パターンの正規化を解除することで、データ モデルを簡略化する必要がある。
- データを解析する追加のツールなしで、XML 形式で格納されているデータを読み込んだりエクスポートしたり必要がある。

## <a name="spatial-features"></a>空間機能

空間データは、オブジェクトの物理的な場所と形状に関する情報を表します。 これらのオブジェクトは、ポイントの場所や、国/地域、道路、湖などのより複雑なオブジェクトである可能性があります。

Azure SQL では、2 つの空間データ型がサポートされています。 

- geometry 型では、ユークリッド (平面) 座標系でデータを表します。
- geography 型では、球状の地球座標系でデータを表します。

Azure SQL の空間機能を使用すると、幾何学的データと地理的データを格納できます。 Azure SQL の空間オブジェクトを使用して、JSON 形式で表されているデータの解析とクエリ、およびリレーショナル データの JSON テキストとしてのエクスポートを実行できます。 これらの空間オブジェクトには、[Point](/sql/relational-databases/spatial/point)、[LineString](/sql/relational-databases/spatial/linestring)、および [Polygon](/sql/relational-databases/spatial/polygon) があります。 Azure SQL には、空間クエリのパフォーマンスを向上させるために使用できる特別な[空間インデックス](/sql/relational-databases/spatial/spatial-indexes-overview)も用意されています。

[空間サポート](/sql/relational-databases/spatial/spatial-data-sql-server)は、SQL Server データベース エンジンの中核となる機能です。

## <a name="key-value-pairs"></a>キーと値のペア

Azure SQL 製品では、キー/値の構造を標準のリレーショナル テーブルとしてネイティブに表現できるため、キーと値のペアをサポートする特別な型も構造体もありません。

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

このキー/値の構造体は、何の制約も受けることなく、ニーズに合わせてカスタマイズできます。 たとえば、値を `nvarchar(max)` 型ではなく XML ドキュメントにすることができます。 値が JSON ドキュメントの場合は、JSON コンテンツの有効性を検証する `CHECK` 制約を使用できます。 追加の列に、1 つのキーに関連する任意の数の値を設定できます。 次に例を示します。

- 計算列とインデックスを追加して、データ アクセスを簡略化および最適化します。
- テーブルをメモリ最適化のスキーマ専用テーブルとして定義して、パフォーマンスを向上させます。

実際にリレーショナル モデルをキーと値のペア ソリューションとして効果的に使用する方法の例については、「[bwin で SQL Server 2016 In-Memory OLTP を使用して前例のないパフォーマンスとスケーリングを実現する方法](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale)」を参照してください。 このケース スタディでは、bwin で ASP.NET キャッシュ ソリューション用のリレーショナル モデルを使用して、1 秒あたり 120 万バッチを達成しています。

## <a name="next-steps"></a>次のステップ

マルチモデル機能は、Azure SQL 製品で共有される SQL Server データベース エンジンの中核となる機能です。 これらの機能の詳細については、次の記事を参照してください。

- [SQL Server と Azure SQL Database でのグラフ処理](/sql/relational-databases/graphs/sql-graph-overview)
- [SQL Server の JSON データ](/sql/relational-databases/json/json-data-sql-server)
- [SQL Server の空間データ](/sql/relational-databases/spatial/spatial-data-sql-server)
- [SQL Server における XML データ](/sql/relational-databases/xml/xml-data-sql-server)
- [Azure SQL Database のキーと値のストアのパフォーマンス](https://devblogs.microsoft.com/azure-sql/azure-sql-database-as-a-key-value-store/)
