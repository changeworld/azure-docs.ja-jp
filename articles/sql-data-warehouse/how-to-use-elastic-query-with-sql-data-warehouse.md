---
title: "Azure SQL Data Warehouse のエラスティック クエリの概念 | Microsoft Docs"
description: "Azure SQL Data Warehouse のエラスティック クエリの概念"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 295cc59fdb23105534b4e7431902eaa720643330
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>SQL Data Warehouse のエラスティック クエリの使用方法



Azure SQL Data Warehouse のエラスティック クエリを使用すると、外部テーブルを使用して Azure SQL Data Warehouse インスタンスにリモートで送信される SQL Database の Transact-SQL を記述できます。 シナリオによっては、この機能を使用してコストを節約し、より高パフォーマンスなアーキテクチャを実現することができます。

この機能は 2 つの主なシナリオを可能にします。

1. ドメインの分離
2. リモート クエリ実行

### <a name="domain-isolation"></a>ドメインの分離

ドメインの分離とは、従来のデータ マートのシナリオを指します。 一部のシナリオでは、たとえば次のようなさまざまな理由により、データ ウェアハウスの他の部分から分離された論理データ ドメインをダウンストリーム ユーザーに提供したい場合があります。

1. リソースの分離 - SQL データベースは大規模な同時ユーザーにサービスを提供するように最適化されており、データ ウェアハウスが対象とする大規模な分析クエリとは若干異なるワークロードを処理します。 分離により、適切なツールで適切なワークロードが処理されるようになります。
2. セキュリティの分離 - 特定のスキーマを使用して選択的に、承認されたデータ サブセットを分離します。
3. サンドボックス - データのサンプル セットを、実稼働クエリなどを探索するための "プレイグラウンド" として提供します。

エラスティック クエリを使用すると、簡単に SQL Data Warehouse データのサブセットを選択して SQL Database インスタンスに移動できます。 さらに、この分離はリモート クエリ実行を有効にする機能を妨害しないため、より興味深い "キャッシュ" のシナリオが可能になります。

### <a name="remote-query-execution"></a>リモート クエリ実行

エラスティック クエリにより、SQL Data Warehouse インスタンスに対するリモート クエリ実行が可能になります。 ホット データとコールド データを SQL Database と SQL Data Warehouse の間で分離することで、2 つのデータベースのそれぞれの長所を利用できます。 ユーザーは、最新のデータを、レポートおよび多数の一般的なビジネス ユーザーにサービスを提供できる SQL Database に保持できます。 しかし、より多くのデータや計算が必要なときには、はるかに高速かつ効率的に大規模な集計を処理できる SQL Data Warehouse インスタンスにクエリの一部をオフロードできます。



## <a name="elastic-query-overview"></a>エラスティック クエリの概要

エラスティック クエリを使用すると、SQL Data Warehouse にあるデータを SQL Database インスタンスで利用できるようになります。 エラスティック クエリにより、SQL Database からのクエリがリモート SQL Data Warehouse インスタンス内のテーブルを参照できます。 

最初の手順として、SQL Data Warehouse インスタンスを参照する外部データ ソース定義を作成します。これは SQL Data Warehouse 内の既存のユーザー資格情報を使用します。 リモート SQL Data Warehouse インスタンスに変更を加える必要はありません。 

> [!IMPORTANT] 
> 
> ユーザーは、ALTER ANY EXTERNAL DATA SOURCE アクセス許可を所有している必要があります。 このアクセス許可は、ALTER DATABASE アクセス許可に含まれています。 ALTER ANY EXTERNAL DATA SOURCE アクセス許可は、リモート データ ソースを参照するために必要です。

次に、SQL Database インスタンスに、SQL Data Warehouse 内のリモート テーブルをポイントするリモート外部テーブル定義を作成します。 外部テーブルを利用するクエリを使用すると、クエリの外部テーブルを参照する部分が SQL Data Warehouse インスタンスに送信されて処理されます。 クエリが完了すると、呼び出し元の SQL Database インスタンスに結果セットが返されます。 SQL Database と SQL Data Warehouse 間のエラスティック クエリを設定する簡単なチュートリアルについては、「[SQL Data Warehouse のエラスティック クエリの構成][Configure Elastic Query with SQL Data Warehouse]」をご覧ください。

SQL Database のエラスティック クエリの詳細については、「[Azure SQL Database のエラスティック クエリの概要][Azure SQL Database elastic query overview]」をご覧ください。



## <a name="best-practices"></a>ベスト プラクティス

### <a name="general"></a>全般

- リモート クエリ実行を使用するときは、必要な列のみを選択し、適切なフィルターを適用するようにします。 この操作により、必要な計算が増えるだけでなく結果セットのサイズも大きくなるため、2 つのインスタンス間で移動する必要があるデータの量も増加します。
- 分析のパフォーマンスのために、分析目的のデータを SQL Data Warehouse と SQL Database の両方でクラスター化列ストアに格納します。
- クエリとデータ移動のために、ソース テーブルをパーティション分割します。
- より詳細な更新と容易な管理を可能にするために、キャッシュとして使用される SQL Database インスタンスをパーティション分割します。 
- 理想的には、PremiumRS データベースを使用します。PremiumRS データベースは IO 集中型のワークロードに焦点を合わせて Premium データベースからの割引価格で提供され、クラスター化列ストア インデックス データベースの分析のメリットを提供するからです。
- 読み込み後は、キャッシュとソースの整合性を維持するために、読み込みまたは有効日 ID の列を SQL Database インスタンスへの Upsert に使用します。 
- 外部データ ソースで定義された SQL Database のリモート ログイン資格情報用に、SQL Data Warehouse インスタンスで別のログインとユーザーを作成します。 

### <a name="elastic-querying"></a>エラスティック クエリの実行

- 外部テーブルと内部的にキャッシュされたテーブルは、SQL Database インスタンスの別のオブジェクトとして存在します。 キャッシュされた部分のテーブルと外部テーブルの上に、両方のテーブルを結合して各テーブルの境界点にフィルターを適用したビューを作成することを検討してください。

  たとえば、最も新しい年のデータを SQL Database インスタンスに保持する場合について考えてみましょう。 データ ウェアハウスの注文テーブルを参照する **ext.Orders** と、SQL Database インスタンス内の最も新しい年に相当するデータを表す **dbo.Orders** という 2 つのテーブルがあるとします。 どちらのテーブルに対してクエリを実行するかをユーザーに判断してもらう代わりに、両方のテーブルの最も新しい年のパーティション ポイントの上にビューを作成します。

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  このような方法で生成されたビューにより、クエリ コンパイラは、ユーザーのクエリに応答するためにデータ ウェアハウス インスタンスを使用する必要があるかどうかを判断できます。 

  データ ウェアハウス インスタンスに対する各エラスティック クエリでは、送信、コンパイル、実行、および関連データの移動のオーバーヘッドが発生します。 各エラスティック クエリが同時実行スロットにカウントされ、リソースを使用することに注意してください。  


- データ ウェアハウス インスタンスからの結果セットをさらにドリルダウンする場合は、パフォーマンスを確保し不要なリソースの使用を回避するために、結果セットを SQL Database の一時テーブルで具体化することを検討してください。

### <a name="moving-data"></a>データの移動 

- 可能な場合は、追加専用のソース テーブルによってデータ管理を容易にし、データ ウェアハウス インスタンスとデータベース インスタンス間で更新を簡単に管理できるようにします。
- フラッシュとフィルのセマンティクスを使用してパーティション レベルでデータを移動して、データ ウェアハウス レベルでのクエリのコストと、データベース インスタンスを常に最新の状態に保つために移動されるデータの量を最小限に抑えます。 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Azure Analysis Services を選択する場合と SQL Database を選択する場合

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- 大量の小規模なクエリを送信する BI ツールでキャッシュを使用する場合。
- 待機時間を秒未満にする必要がある場合。
- Analysis Services の管理/開発モデルに習熟している場合。 

#### <a name="sql-database"></a>SQL Database

- SQL を使用してキャッシュ データのクエリを実行する場合。
- 一部のクエリをリモート実行する必要がある場合。
- 大規模なキャッシュの要件がある場合。



## <a name="faq"></a>FAQ

Q: Elastic Database プール内のデータベースをエラスティック クエリに使用できますか?

A: はい。 エラスティック プール内の SQL Database にエラスティック クエリを使用できます。 

Q: エラスティック クエリに使用できるデータベースの数に上限はありますか?

A: 論理サーバーには、お客様の偶発的な使いすぎを防ぐために DTU の上限が設けられています。 SQL Data Warehouse インスタンスと共に多数のデータベースを有効にした場合、予期せずにこの上限に達することがあります。 そのような場合には、論理サーバーの DTU の上限を引き上げる要求を送信してください。 [サポート チケットを作成](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)し、要求の種類として *[クォータ]* を選択することでクォータを引き上げることができます。

Q: エラスティック クエリに行レベルのセキュリティや動的データ マスクを使用することはできますか?

A: SQL Database の高度なセキュリティ機能を使用するには、最初にデータを SQL Database に移動して格納していただく必要があります。 現時点では、外部テーブルを通じてクエリされるデータに行レベルのセキュリティや DDM を適用することはできません。 

Q: SQL Database インスタンスからデータ ウェアハウス インスタンスへの書き込みはできますか?

A: 現時点では、そのような機能はサポートされていません。 将来にそのような機能を希望される場合は、[フィードバック ページ][Feedback page]でこの機能に関するフィードバックを作成していただくか、フィードバックにご投票ください。 

Q: geometry や geography などの空間型は使用できますか?

A: 空間型は、varbinary(max) 値として SQL Data Warehouse に格納できます。 エラスティック クエリを使用してこれらの列のクエリを実行する場合は、実行時に適切な型に変換できます。

![空間型](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->