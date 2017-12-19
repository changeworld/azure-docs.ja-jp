---
title: "データ読み込みのガイダンス - Azure SQL Data Warehouse | Microsoft Docs"
description: "Azure SQL Data Warehouse でのデータの読み込みと ELT の実行に関する推奨事項。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータ読み込みのガイダンス
Azure SQL Data Warehouse へのデータの読み込みに関する推奨事項とパフォーマンスの最適化。 

- PolyBase と、ELT (抽出、読み込み、および変換) 処理の設計の詳細については、[SQL Data Warehouse の ELT の設計](design-elt-data-loading.md)に関するページを参照してください。
- 読み込みのチュートリアルについては、「[PolyBase を使用して Azure Blob Storage から Azure SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)」を参照してください。


## <a name="extract-source-data"></a>ソース データの抽出

SQL Server または Azure SQL Data Warehouse から ORC ファイル形式にデータをエクスポートする場合、テキストが多く使用されている列は、java のメモリ不足エラーにより、わずか 50 列に制限される可能性があります。 これを回避するには、列のサブセットのみをエクスポートします。


## <a name="land-data-to-azure"></a>Azure へのデータの配置
PolyBase には、100 万バイトを超えるデータを含む行を読み込むことができません。 Azure BLOB ストレージまたは Azure Data Lake Store のテキスト ファイルにデータを保存する場合、データは 100 万バイトよりも少なくする必要があります。 これは、テーブル スキーマが定義されていても同じです。

ストレージ層とデータ ウェアハウスを併置して、待ち時間を最小限に抑えます。

## <a name="data-preparation"></a>データの準備

すべてのファイル形式は、異なるパフォーマンス特性を持っています。 最速で読み込むには、圧縮されたテキスト区切りファイルを使用します。 UTF-8 と UTF-16 のパフォーマンスの違いはごくわずかです。

大きな圧縮ファイルは小さな圧縮ファイルに分割します。

## <a name="create-designated-loading-users"></a>指定読み込みユーザーの作成
適切なコンピューティング リソースで読み込みを実行するには、読み込みを実行するように指定された読み込みユーザーを作成します。 特定のリソース クラスに各読み込みユーザーを割り当てます。 読み込みを実行するには、いずれかの読み込みユーザーとしてログインし、読み込みを実行します。 読み込みは、ユーザーのリソース クラスで実行されます。  この方法は、現在のリソース クラスのニーズに合わせてユーザーのリソース クラスを変更しようとするより簡単です。

このコードは、staticrc20 リソース クラスの読み込みユーザーを作成します。 データベースに対する制御アクセス許可をユーザーに付与してから、そのユーザーを staticrc20 データベース ロールのメンバーとして追加します。 staticRC20 リソース クラスのリソースで読み込みを実行するには、単に LoaderRC20 としてログインし、読み込みを実行します。 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

動的リソース クラスではなく、静的リソース クラスで読み込みを実行します。 静的リソース クラスを使用すると、[サービス レベル](performance-tiers.md#service-levels)に関係なく、必ず同じリソースが使用されます。 動的リソース クラスを使用すると、サービス レベルによってリソースが変わります。 動的クラスの場合、サービス レベルが低いと、おそらく読み込みユーザー用により大きなリソース クラスを使用する必要があります。

### <a name="example-for-isolating-loading-users"></a>読み込みユーザーを分離する例

多くの場合、SQL DW にデータを読み込むことができる複数のユーザーが必要になります。 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] にはデータベースの CONTROL アクセス許可が必要であるため、すべてのスキーマに対する制御アクセス権を持つ複数のユーザーが存在することになります。 これを制限するために、DENY CONTROL ステートメントを使用できます。

たとえば、部門 A に schema_A、部門 B に schema_B というデータベース スキーマがあるとします。データベース ユーザーの user_A と user_B を、部門 A と B のそれぞれで読み込みを行う PolyBase のユーザーにします。 両方のユーザーには CONTROL データベースのアクセス許可が付与されています。 スキーマ A と B の作成者はここで、次のように DENY を使用してスキーマをロックダウンします。

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

user_A と user_B は、他の部門のスキーマからロックアウトされるようになりました。


## <a name="load-to-a-staging-table"></a>ステージング テーブルへの読み込み

最速で読み込むには、ラウンド ロビン式ヒープ ステージング テーブルに読み込みます。 これは、Azure Storage 層から SQl Data Warehouse にデータを移動するための最も効率的な方法になります。

大規模な読み込みジョブが予想される場合は、データ ウェアハウスをスケール アップします。

最適な読み込みパフォーマンスを得るために、一度に 1 つだけの読み込みジョブを実行します。

### <a name="optimize-columnstore-index-loads"></a>列ストア インデックスの読み込みを最適化する

列ストア インデックスは、高品質の行グループにデータを圧縮するために多くのメモリを必要とします。 最適な圧縮とインデックスの効率を得るには、列ストア インデックスで各行グループに 1,048,576 行を圧縮する必要があります。 これは、行グループごとの最大行数です。 メモリが不足気味である場合、列ストア インデックスは最大圧縮率を達成できないことがあります。 これが、クエリのパフォーマンスに影響します。 詳細については、[列ストア メモリの最適化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)に関するページを参照してください。

- 最大圧縮率を達成するための十分なメモリを読み込みユーザーが確実に持つようにするには、中規模または大規模なリソース クラスのメンバーである読み込みユーザーを使用します。 
- 新しい行グループを完全に埋められるように、十分な行を読み込みます。 一括読み込みでは、1,048,576 行ごとに、列ストアに直接移動されます。 102,400 行未満の読み込みでは、行がデルタストアに送信されます。デルタストアは、圧縮のための十分な量になるまで、行をクラスター化インデックスに保持します。 読み込みの行が少なすぎる場合は、すべての行がデルタストアに移動され、すぐには列ストア形式に圧縮されません。


### <a name="handling-loading-failures"></a>読み込みエラーを処理する

外部テーブルを使用した読み込みが、"*クエリは中止されました。外部ソースの読み取り中に最大拒否しきい値に達しました*" というエラーで失敗する場合があります。 これは、外部データに *ダーティ* なレコードが含まれていることを示します。 データ レコードは、列の実際のデータの種類/数値が外部テーブルの列定義と一致しない場合、またはデータが指定された外部ファイルの形式に従っていない場合に「ダーティ」であるとみなされます。 

これを修正するには、外部テーブルと外部ファイルの形式の定義が正しいこと、および外部データがこれらの定義に従っていることを確認します。 外部データ レコードのサブセットがダーティである場合は、CREATE EXTERNAL TABLE DDL の中で拒否オプションを使用することで、クエリでこれらのレコードを拒否することを選択できます。



## <a name="insert-data-into-production-table"></a>運用テーブルへのデータの挿入
以下は、運用テーブルに行を挿入する場合の推奨事項です。


### <a name="batch-insert-statements"></a>INSERT ステートメントをバッチ化する
[INSERT ステートメント](/sql/t-sql/statements/insert-transact-sql.md)で小さなテーブルに 1 回だけ読み込む場合や、検索を定期的に再読み込みする場合は、`INSERT INTO MyLookup VALUES (1, 'Type 1')` などのステートメントで、ニーズに十分応えることができます。  単一挿入は、一括読み込みを実行するほど効率的ではありません。 

一日に何千もの単一の挿入がある場合は、一括読み込みができるように、挿入をバッチ化します。  単一の挿入をファイルに追加する処理を開発し、定期的にファイルを読み込む別の処理を作成します。

### <a name="create-statistics-after-the-load"></a>読み込み後に統計を作成する

クエリ パフォーマンスを向上させるには、最初に読み込んだ後またはデータに大きな変更が加えられた後に、すべてのテーブルのすべての列で統計を作成することが重要です。  統計の詳細については、[統計][Statistics] に関する記事を参照してください。 次の例では、Customer_Speed テーブルの 5 つの列に関する統計を作成します。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>ストレージ キーの切り替え
セキュリティのために BLOB ストレージへのアクセス キーを定期的に変更することをお勧めします。 BLOB ストレージ アカウントには 2 つのストレージ キーがあります。 これは、キーを切り替えられるようにするためです。

Azure Storage のアカウント キーを切り替えるには:

1. セカンダリ ストレージ アクセス キーに基づいたセカンダリ データベース スコープの資格情報を作成します。
2. この新しい資格情報に基づいて 2 つ目の外部データ ソースを作成します。
3. 新しい外部データ ソースを参照するように、外部テーブルを削除して、作成します。 

外部テーブルを新しいデータ ソースに移行したら、以下のクリーンアップ作業を実行できます。

1. 1 つ目の外部データ ソースを削除します。
2. プライマリ ストレージ アクセス キーに基づいた 1 つ目のデータベース スコープ資格情報を削除します。
3. Azure にログインし、次回の切り替えに備えてプライマリ アクセス キーを再生成します。


## <a name="next-steps"></a>次のステップ
読み込み処理の監視については、「[DMV を利用してワークロードを監視する](sql-data-warehouse-manage-monitor.md)」を参照してください。



