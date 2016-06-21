<properties
   pageTitle="Azure SQL Data Warehouse のトラブルシューティング | Microsoft Azure"
   description="Azure SQL Data Warehouse のトラブルシューティングを行います。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/15/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Azure SQL Data Warehouse のトラブルシューティング
このトピックでは、Azure SQL Data Warehouse で発生するいくつかの一般的な問題を示します。

## 接続
一般的な接続に関する問題は次のとおりです。

- ファイアウォール ルールが設定されていない
- サポートされていないツール/プロトコルを使用している

### ファイアウォール ルール
Azure SQL Database は、既知の IP アドレスのみがデータベースにアクセスできるように、サーバーとデータベース レベルのファイアウォールによって保護されています。ファイアウォールは、既定でセキュリティ保護されています。つまり、接続する前に、IP アドレスまたはアドレス範囲を明示的に有効にする必要があります。アクセスできるようにファイアウォールを構成するには、[プロビジョニング手順][]に関するページの、[クライアント IP に対するサーバー ファイアウォール アクセスの構成][]に関するトピックの手順に従ってください。

### サポートされていないツール/プロトコルを使用している
SQL Data Warehouse では、[Visual Studio 2013 または 2015][] を使用してデータのクエリを実行することをお勧めします。クライアント接続については、[SQL Server Native Client 10/11 (ODBC)][] をお勧めします。SQL Server Management Studio (SSMS) はまだサポートされていません。部分的には動作しますが、オブジェクト エクスプ ローラー ツリーは SQL Data Warehouse では機能しません。クエリは、エラー メッセージをいくつか無視すると機能することがあります。

## クエリのパフォーマンス

SQL Data Warehouse で最適なクエリ パフォーマンスを実現するために、データベース設計で実行できることは複数あります。クエリを実行する方法を把握するには、まず、[クエリを監視する方法][]に関する記事をご覧ください。[SQL Data Warehouse をスケーリング][]して、コンピューティング能力をクエリに追加するだけで、クエリをより高速に実行できる場合があります。こうした最適化の方法の多くは、[SQL Data Warehouse のベスト プラクティス][]に関する記事で確認することができます。

クエリのパフォーマンスに関する問題の原因として最も一般的なものをいくつか次に示します。

### 統計

テーブルの[統計][]には、データベースの列または列の組み合わせの値の、範囲と頻度に関する情報が含まれます。クエリ エンジンは、これらの統計情報を使用してクエリの実行を最適化し、クエリのパフォーマンスを向上させます。SQL Server や SQL Database とは異なり、SQL Data Warehouse では、統計情報が自動的には作成または更新されません。このため、どのテーブルも、統計情報を手動で管理する必要があります。統計情報を管理し、統計情報を必要とするテーブルを特定する方法については、「[SQL Data Warehouse での統計の管理][]」をご覧ください。

### テーブル設計

SQL Data Warehouse では、[テーブルに適したハッシュ分散キー][]と[テーブル設計][]を選択することが重要です。作業を開始した後、SQL Data Warehouse のパフォーマンスを高めていくには、こうした記事で説明されている概念を理解するようにしてください。

### クラスター化列ストア セグメントの質

クラスター化列ストア テーブルに対するクエリのパフォーマンスを最適化するには、クラスター化列ストア セグメントの質が重要になります。セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。以下のクエリは、列ストア インデックス セグメントの正常性という点で問題があるテーブルを特定し、そのテーブルに列ストア インデックスを再構築するための T-SQL を生成するものです。このクエリの結果の最初の列では、各インデックスを再構築するための T-SQL を確認できます。2 番目の列では、圧縮を最適化するうえで最低限使用が推奨されるリソース クラスがわかります。
 
**手順 1:** 各 SQL Data Warehouse データベースにこのクエリを実行し、クラスターの列ストア インデックスに最適な状態でないものがあるかどうかを確認します。行が返されなかった場合には、この作業による影響は特にありません。このため、以降の操作は必要ありません。

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**手順 2:** このテーブルにインデックスを再構築するアクセス許可を持っているユーザーのリソース クラスを、上位のものに変更します。リソース クラスは、先ほどのクエリの 2 列目にあるものを使用してください。

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  上の "LoadUser" には、ALTER INDEX ステートメントを実行するために作成した有効なユーザー名を指定する必要があります。db\_owner ユーザーのリソース クラスは変更できません。リソース クラスと新しいユーザーの作成方法について詳しくは、下記リンクをご覧ください。

 
**手順 3:** 手順 2 でリソース クラスを上位に変更したユーザー (たとえば "LoadUser") としてログオンし、手順 1 に示したクエリで生成された ALTER INDEX ステートメントを実行します。このユーザーは、手順 1 のクエリで特定されたテーブルに対して ALTER 権限を持っている必要がありますのでご注意ください。
 
**手順 4:** 手順 1 のクエリをもう一度実行します。効率的なインデックスが構築された場合には、このクエリで行が返されなくなります。行が返されなければ、作業は終わりです。SQL DW データベースが複数ある場合には、各データベースについてこれまでのプロセスを繰り返します。行が返された場合は、手順 5 に進みます。
 
**手順 5:** 手順 1 のクエリをもう一度実行したときに行が返される場合には、行のサイズが非常に大きなテーブルが存在し、クラスター化列ストア インデックスを最適な形で構築する際に大量のメモリが必要になっていることが考えられます。この場合、xlargerc クラスを使ったうえで各テーブルにこのプロセスをもう一度やり直してみてください。リソース クラスを変更するには、手順 2 で xlargerc を選択します。そのうえで、依然としてインデックスが最適でないテーブルについて、手順 3 をもう一度実行します。DW100 ～ DW300 を使用しており、かつ、既に xlargerc を使用している場合には、インデックスをそのままにするか、一時的に DWU を増やしてこの操作に使用できるメモリを増やしてみてください。
 
**最後の手順:** 上に示したリソース クラスは、構築する列ストア インデックスの質を最大限に高めるために最低限推奨されるものです。このため、データを読み込むユーザーに対してはこの設定を維持することをお勧めします。ただし、手順 2 で変更した内容を元に戻すこともできます。その場合、以下のコマンドを使用します。

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

CCI テーブルに対する読み込みで最低限推奨されるリソース クラスは、DW100 ～ DW300 であれば xlargerc、DW400 ～ DW600 であれば largerc、DW1000 以上であれば mediumrc です。ほとんどのワークロードでは、このガイダンスに従えば問題ありません。その目的は、各インデックス構築処理に 400 MB 以上のメモリを割り当てることにあります。ただし、1 つのサイズすべてに対応できるとは限りません。列ストア インデックスの最適化に必要なメモリは、読み込むデータに左右されます。そして、読み込むデータに影響を及ぼすのは、主に行のサイズです。このため、行のサイズが小さなテーブルであれば、必要なメモリが少なくなります。これに対して、行のサイズが大きければ、必要なメモリが多くなります。メモリの割り当てを少なくしても最適な列ストア インデックスが得られるかどうかを確認する場合には、手順 1 のクエリを使用します。行グループ 1 つにつき、少なくとも平均 100,000 行以上は必要になります。500,000 行あればさらに良いでしょう。最大では、行グループあたり 100 万行を確認します。リソース クラスと同時実行を管理する方法について詳しくは、以下のリンクをご覧ください。


## 次のステップ
SQL Data Warehouse ソリューションを最適化する方法の詳細については、「[SQL Data Warehouse のベスト プラクティス][]」をご覧ください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse をスケーリング]: ./sql-data-warehouse-manage-compute-overview.md
[テーブル設計]: ./sql-data-warehouse-develop-table-design.md
[テーブルに適したハッシュ分散キー]: ./sql-data-warehouse-develop-hash-distribution-key
[development overview]: ./sql-data-warehouse-overview-develop.md
[クエリを監視する方法]: ./sql-data-warehouse-manage-monitor.md
[SQL Data Warehouse での統計の管理]: ./sql-data-warehouse-develop-statistics.md
[プロビジョニング手順]: ./sql-data-warehouse-get-started-provision.md
[クライアント IP に対するサーバー ファイアウォール アクセスの構成]: ./sql-data-warehouse-get-started-provision.md/#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 または 2015]: ./sql-data-warehouse-get-started-connect.md
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[統計]: ./sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0608_2016-->