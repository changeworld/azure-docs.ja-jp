   <properties
   pageTitle="Azure SQL Data Warehouse へのデータの読み込み | Microsoft Azure"
   description="SQL Data Warehouse にデータを読み込むための一般的なシナリオについて説明します。これには PolyBase、Azure Blob Storage、フラット ファイル、およびディスク発送の使用が含まれます。サード パーティ製のツールを使用することもできます。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/17/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Azure SQL Data Warehouse へのデータの読み込み

データを SQL Data Warehouse に読み込むためのシナリオ オプションと推奨事項の概要。

データを読み込むとき、通常、最も大変なのは読み込み対象のデータを準備する作業です。Azure では、多くのサービスの共通データ ストアとして Azure Blob Storage を使用し、Azure Data Factory で Azure サービス間の通信とデータ移動を調整することで、この読み込み作業が簡単になっています。こうしたプロセスは、超並列処理 (MPP) によって Azure Blob Storage から SQL Data Warehouse へのデータ読み込みを並列処理する PolyBase テクノロジと統合されています。

サンプル データベースを読み込むためのチュートリアルについては、[サンプル データベースの読み込み][]に関するページをご覧ください。

## Azure Blob Storage からの読み込み
PolyBase を使用して Azure Blob Storage からデータを読み込むと、SQL Data Warehouse へのデータ インポートが最速になります。PolyBase では、SQL Data Warehouse の超並列処理 (MPP) 設計を使用して、Azure Blob Storage からのデータ読み込みを並列処理します。PolyBase を使用するには、T-SQL コマンドまたは Azure Data Factory パイプラインを使用します。

### 1\.PolyBase と T-SQL を使用する

読み込みプロセスの概要:

2. データを UTF-8 として書式設定します (現在 PolyBase では UTF-16 はサポートされていません)。
2. データを Azure Blob Storage に移動し、テキスト ファイルに格納します。
3. SQL Data Warehouse で外部オブジェクトを構成して、データの場所とデータ形式を定義します。
4. 新しいデータベース テーブルへのデータ読み込みを並列処理する T-SQL コマンドを実行します。

<!-- 5. Schedule and run a loading job. --> 

チュートリアルについては、「[Load data from Azure blob storage to SQL Data Warehouse (PolyBase) (Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (PolyBase))][]」をご覧ください。

### 2\.Azure Data Factory を使用する

PolyBase をさらに簡単に使用できるように、PolyBase を使用する Azure Data Factory パイプラインを作成して、Azure Blob Storage から SQL Data Warehouse にデータを読み込むことができます。この場合、T-SQL オブジェクトを定義する必要がないため、すばやく構成できます。外部データをインポートせずに、そのデータに対してクエリを実行する必要がある場合は、T-SQL を使用します。

読み込みプロセスの概要:

2. データを UTF-8 として書式設定します (現在 PolyBase では UTF-16 はサポートされていません)。
2. データを Azure Blob Storage に移動し、テキスト ファイルに格納します。
3. Azure Data Factory パイプラインを作成して、データを取り込みます。PolyBase オプションを使用します。
4. パイプラインのスケジュールを設定し、実行します。

チュートリアルについては、「[Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (Azure Data Factory)][]」をご覧ください。


## SQL Server からの読み込み
SQL Server から SQL Data Warehouse にデータを読み込むには、Integration Services (SSIS) を使用するか、フラット ファイルを転送するか、ディスクを Microsoft に送付します。他の読み込みプロセスの概要とチュートリアルへのリンクをご覧ください。

SQL Server から SQL Data Warehouse への完全なデータ移行を計画するには、[移行の概要][]に関するページをご覧ください。

### Integration Services (SSIS) の使用
Integration Services (SSIS) パッケージを使用して SQL Server に読み込む場合は、SQL Server を移行元として、また SQL Data Warehouse を移行先として使用するようにパッケージを更新します。この手軽で簡単な方法は、読み込みプロセスを移行せず、クラウドに既にあるデータを使用する場合に適しています。ただし、SSIS では読み込みの並列処理は実行されないため、PolyBase を使用するよりも読み込み速度が遅くなります。

読み込みプロセスの概要:

1. 移行元として SQL Server インスタンスを指定し、移行先として SQL Data Warehouse データベースを指定するように、Integration Services パッケージを変更します。
2. スキーマを SQL Data Warehouse に移行します (その場所にまだ存在しない場合)。
3. SQL Data Warehouse でサポートされているデータ型のみを使用するように、パッケージのマッピングを変更します。
3. パッケージのスケジュールを設定し、実行します。

チュートリアルについては、「[Load data from SQL Server to Azure SQL Data Warehouse (SSIS) (SQL Server から Azure SQL Data Warehouse へのデータの読み込み (SSIS))][]」をご覧ください。

### AZCopy の使用 (データが 10 TB 未満の場合に推奨)
データのサイズが 10 TB 未満の場合、SQL Server からフラット ファイルにデータをエクスポートし、ファイルを Azure Blob Storage にコピーして、PolyBase を使って SQL Data Warehouse に読み込むことができます。

読み込みプロセスの概要:

1. bcp コマンドライン ユーティリティを使用して、SQL Server からフラット ファイルにデータをエクスポートします。
2. AZCopy コマンドライン ユーティリティを使用して、フラット ファイルから Azure Blob Storage にデータをコピーします。
3. PolyBase を使用して SQL Data Warehouse に読み込みます。

チュートリアルについては、「[Load data from Azure blob storage to SQL Data Warehouse (PolyBase) (Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (PolyBase))][]」をご覧ください。

### bcp の使用
データ量が少ない場合は、bcp を使用して、Azure SQL Data Warehouse に直接読み込むことができます。

読み込みプロセスの概要:
1. bcp コマンドライン ユーティリティを使用して、SQL Server からフラット ファイルにデータをエクスポートします。
2. bcp を使用して、フラット ファイルから SQL Data Warehouse にデータを直接読み込みます。

チュートリアルについては、「[SQL Server から Azure SQL Data Warehouse へのデータの読み込み (フラット ファイル)][]」をご覧ください。


### Import/Export の使用 (データが 10 TB を超える場合に推奨)
10 TB を超えるデータを Azure に移動する場合は、Microsoft のディスク発送サービス [Import/Export][] を使用することをお勧めします。

読み込みプロセスの概要
2. bcp コマンド ライン ユーティリティを使用して、SQL Server から、転送可能なディスクのフラット ファイルにデータをエクスポートします。
3. ディスクを Microsoft に送付します。
4. Microsoft がデータを SQL Data Warehouse に読み込みます


## 推奨事項

パートナーの多くが読み込みソリューションを提供しています。詳細については、[ソリューション パートナー][]の一覧をご覧ください。


非リレーショナル ソースのデータを SQL Data Warehouse に読み込む場合は、事前にそのデータを行と列に変換しておく必要があります。変換したデータはデータベースに格納しなくても、テキスト ファイルに保存できます。

新しく読み込んだデータの統計を作成してください。Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。クエリで最高のパフォーマンスを得るには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。詳細については、[統計][]に関するページをご覧ください。


## 次のステップ
開発に関するその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase) (Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (PolyBase))]: sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (Azure Data Factory)]: sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS) (SQL Server から Azure SQL Data Warehouse へのデータの読み込み (SSIS))]: sql-data-warehouse-load-from-sql-server-with-integration-services.md
[SQL Server から Azure SQL Data Warehouse へのデータの読み込み (フラット ファイル)]: sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: sql-data-warehouse-load-from-sql-server-with-azcopy.md

[サンプル データベースの読み込み]: sql-data-warehouse-load-sample-databases.md
[移行の概要]: sql-data-warehouse-overview-migrate.md
[ソリューション パートナー]: sql-data-warehouse-integrate-solution-partners.md
[開発の概要]: sql-data-warehouse-overview-develop.md
[統計]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

<!---HONumber=AcomDC_0615_2016-->