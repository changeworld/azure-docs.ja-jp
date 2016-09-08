<properties
	pageTitle="SQL Data Warehouse サービスに関するすべてのトピック | Microsoft Azure"
	description="http://azure.microsoft.com/documentation/articles/ に存在する SQL Data Warehouse という名前の Azure サービスに関するすべてのトピックの一覧 (タイトルと説明)。"
	services="sql-data-warehouse"
	documentationCenter=""
	authors="barbkess"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-data-warehouse"
	ms.workload="sql-data-warehouse"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="barbkess"/>


# Azure SQL Data Warehouse サービスに関するすべてのトピック

このトピックでは、**SQL Data Warehouse** サービスに直接適用されるすべてのトピックを示します。この Web ページでは、**Ctrl + F** を使用してキーワード検索することで、現在関心があるトピックを見つけることができます。



## 更新された記事

このセクションでは、最近、大幅な更新または重要な更新が行われた記事を示します。更新された各記事について、追加されたマークダウン テキストが大まかに抜粋されています。これらの記事は、**2016 年 7 月 26 日**～ **2016 年 8 月 21 日**に更新されたものです。

| &nbsp; | 記事 | 更新されたテキスト (抜粋) |
| --: | :-- | :-- |
| 1 | [SQL Data Warehouse での同時実行とワークロード管理](sql-data-warehouse-develop-concurrency.md) | **同時実行の制限が考慮されるクエリ** ほとんどのクエリは、リソース クラスによって管理されます。これらのクエリは、同時実行クエリと同時実行スロットの両方のしきい値以下である必要があります。エンド ユーザーは、同時実行スロット モデルからクエリを除外することはできません。繰り返しますが、次のステートメントではリソース クラスが**考慮**されます。/ INSERT-SELECT / UPDATE / DELETE / SELECT (ユーザー テーブルのクエリを実行する場合) / ALTER INDEX REBUILD / ALTER INDEX REORGANIZE / ALTER TABLE REBUILD / CREATE INDEX / CREATE CLUSTERED COLUMNSTORE INDEX / CREATE TABLE AS SELECT (CTAS) / データの読み込み / Data Movement Service (DMS) によって実行されるデータ移動操作 ** 同時実行の制限に対するクエリの例外** |
| 2 | [Premium Storage への移行の詳細](sql-data-warehouse-migrate-to-premium-storage.md) | Premium Storage の変更により、Data Warehouse の基になるアーキテクチャでデータベース BLOB ファイルの数も増えています。パフォーマンスの問題が発生した場合は、下記のスクリプトを使用して、クラスター化列ストア インデックスを再構築することをお勧めします。これにより、既存のデータの一部が追加の BLOB に転送されます。何も行わない場合、Data Warehouse のテーブルに読み込むデータが増えるにつれて、データは自然に再配布されます。**前提条件:** 1. Data Warehouse を 1,000 DWU 以上で実行する必要があります (コンピューティング能力のスケーリングに関する記事をご覧ください)。2. このスクリプトを実行するユーザーは、mediumrc ロール以上である必要があります。1. このロールにユーザーを追加するには、次のコマンドを実行します。1. ````EXEC sp_addrolemember 'xlargerc', 'MyUser'```` ````sql /------------------------------------------------------------------------------ /- Step 1: Create Table to control Index Rebuild /- Run as user in mediumrc or higher /------------------------------------------------------------ |





## 作業開始

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 3 | [Azure SQL Data Warehouse への認証](sql-data-warehouse-authentication.md) | Azure SQL Data Warehouse への Azure Active Directory (AAD) および SQL Server 認証。 |
| 4 | [Azure SQL Data Warehouse のベスト プラクティス](sql-data-warehouse-best-practices.md) | Azure SQL Data Warehouse のソリューションを開発する際に知っておく必要がある推奨事項とベスト プラクティス。これらは、成功に役立ちます。 |
| 5 | [Azure SQL Data Warehouse のドライバー](sql-data-warehouse-connection-strings.md) | SQL Data Warehouse の接続文字列と ドライバー |
| 6 | [Azure SQL Data Warehouse への接続](sql-data-warehouse-connect-overview.md) | Azure SQL Data Warehouse に接続するための接続の概要 |
| 7 | [Azure Machine Learning を使用したデータの分析](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Azure Machine Learning を使用し、Azure SQL Data Warehouse で保存されたデータに基づいて予測機械学習モデルを構築します。 |
| 8 | [Azure SQL Data Warehouse に対するクエリ (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | sqlcmd コマンド ライン ユーティリティを使用して Azure SQL Data Warehouse に対してクエリを実行します。 |
| 9 | [Transact-SQL (TSQL) を使用して SQL Data Warehouse データベースを作成する](sql-data-warehouse-get-started-create-database-tsql.md) | TSQL で Azure SQL Data Warehouse を作成する方法を説明します。 |
| 10 | [SQL Data Warehouse のサポート チケットを作成する方法](sql-data-warehouse-get-started-create-support-ticket.md) | Azure SQL Data Warehouse でサポート チケットを作成する方法。 |
| 11 | [Azure Data Factory を使用してデータを読み込む](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Azure Data Factory を使用してデータを読み込む方法 |
| 12 | [SQL Data Warehouse で PolyBase によってデータを読み込む](sql-data-warehouse-get-started-load-with-polybase.md) | データ ウェアハウジングのシナリオに沿って、PolyBase の概要と、その使用方法を学習します。 |
| 13 | [新しい論理サーバーの作成](sql-data-warehouse-get-started-new-server.md) | Azure ポータルで Azure SQL Data Warehouse を作成する方法を説明します。 |
| 14 | [Azure SQL Data Warehouse の作成](sql-data-warehouse-get-started-provision.md) | Azure ポータルで Azure SQL Data Warehouse を作成する方法を説明します。 |
| 15 | [PowerShell を使用して SQL Data Warehouse を作成する](sql-data-warehouse-get-started-provision-powershell.md) | PowerShell を使用して SQL Data Warehouse を作成します。 |
| 16 | [Power BI でデータを視覚化する](sql-data-warehouse-get-started-visualize-with-power-bi.md) | SQL Data Warehouse のデータを Power BI で視覚化します。 |
| 17 | [Azure SQL Data Warehouse に対するクエリ (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Visual Studio で SQL Data Warehouse に対してクエリを実行します。 |



## 開発

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 18 | [SQL Data Warehouse のトランザクションの最適化](sql-data-warehouse-develop-best-practices-transactions.md) | Azure SQL Data Warehouse で効率的なトランザクションの更新を記述するためのベスト プラクティス ガイダンス |
| 19 | [SQL Data Warehouse での同時実行とワークロード管理](sql-data-warehouse-develop-concurrency.md) | ソリューション開発のための Azure SQL Data Warehouse での同時実行とワークロード管理を理解します。 |
| 20 | [SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)](sql-data-warehouse-develop-ctas.md) | ソリューションの開発のために、Azure SQL Data Warehouse の CREATE TABLE AS SELECT (CTAS) ステートメントでコーディングする際のヒントです。 |
| 21 | [SQL Data Warehouse での動的 SQL](sql-data-warehouse-develop-dynamic-sql.md) | ソリューション開発のための Azure SQL Data Warehouse での動的 SQL の使用に関するヒント。 |
| 22 | [SQL Data Warehouse の Group By オプション](sql-data-warehouse-develop-group-by-options.md) | ソリューション開発のための Azure SQL Data Warehouse での Group By オプションの実装に関するヒント。 |
| 23 | [SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化](sql-data-warehouse-develop-label.md) | ソリューション開発のための Azure SQL Data Warehouse でのラベルを使用してクエリのインストルメント化に関するヒント。 |
| 24 | [SDL Data Warehouse でのループ](sql-data-warehouse-develop-loops.md) | ソリューションを開発するための Azure SQL Data Warehouse でのTRANSACT-SQL のループと、カーソルの置換に関するヒント。 |
| 25 | [SQL Data Warehouse のストアド プロシージャ](sql-data-warehouse-develop-stored-procedures.md) | ソリューション開発のための Azure SQL Data Warehouse でのストアド プロシージャの実装に関するヒント。 |
| 26 | [SQL Data Warehouse のトランザクション](sql-data-warehouse-develop-transactions.md) | ソリューション開発のための、Azure SQL Data Warehouse でのトランザクションの実装に関するヒント。 |
| 27 | [SQL Data Warehouse のユーザー定義のスキーマ](sql-data-warehouse-develop-user-defined-schemas.md) | ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL スキーマの使用に関するヒント。 |
| 28 | [SQL Data Warehouse の変数の代入](sql-data-warehouse-develop-variable-assignment.md) | ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL 変数の代入に関するヒント。 |
| 29 | [SQL Data Warehouse のビュー](sql-data-warehouse-develop-views.md) | ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL ビューの使用に関するヒント。 |
| 30 | [SQL Data Warehouse の設計上の決定と コーディング技法](sql-data-warehouse-overview-develop.md) | SQL Data Warehouse に関する開発コンセプト、設計上の決定、推奨事項、およびコーディング技法。 |



## 管理

| &nbsp; | タイトル | 説明 |
| --: | :-- | :-- |
| 31 | [Azure SQL Data Warehouse のコンピューティング能力の管理 (概要)](sql-data-warehouse-manage-compute-overview.md) | Azure SQL Data Warehouse のパフォーマンス スケールアウト機能。DWU を調整してスケールアウトする方法や、コンピューティング リソースを一時停止および再開してコストを節約する方法を説明します。 |
| 32 | [Azure SQL Data Warehouse のコンピューティング能力の管理 (Azure ポータル)](sql-data-warehouse-manage-compute-portal.md) | コンピューティング能力を管理するための Azure ポータルのタスク。DWU を調整することで、コンピューティング リソースをスケーリングします。また、コストを節約するために、コンピューティング リソースを一時停止および再開します。 |
| 33 | [Azure SQL Data Warehouse のコンピューティング能力の管理 (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | コンピューティング能力を管理するための PowerShell のタスク。DWU を調整することで、コンピューティング リソースをスケーリングします。また、コストを節約するために、コンピューティング リソースを一時停止および再開します。 |
| 34 | [Azure SQL Data Warehouse のコンピューティング能力の管理 (REST)](sql-data-warehouse-manage-compute-rest-api.md) | コンピューティング能力を管理するための PowerShell のタスク。DWU を調整することで、コンピューティング リソースをスケーリングします。また、コストを節約するために、コンピューティング リソースを一時停止および再開します。 |
| 35 | [Azure SQL Data Warehouse のコンピューティング能力の管理 (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | DWU を調整することでパフォーマンスをスケールアウトする Transact-SQL (T-SQL) タスク。ピーク時以外にリソースをスケール バックして、コストを削減します。 |
| 36 | [DMV を利用してワークロードを監視する](sql-data-warehouse-manage-monitor.md) | DMV を利用してワークロードを監視するについて説明します。 |
| 37 | [Azure SQL Data Warehouse でのデータベースの管理](sql-data-warehouse-overview-manage.md) | SQL Data Warehouse のデータベース管理の概要について説明します。ここでは管理ツール、DWU およびパフォーマンスのスケールアウト、クエリ パフォーマンスのトラブルシューティング、適切なセキュリティ ポリシーの設定、データの破損や地域的障害からのデータベースの復旧について見ていきます。 |
| 38 | [Azure SQL Data Warehouse でのユーザー クエリの監視](sql-data-warehouse-overview-manage-user-queries.md) | Azure SQL Data Warehouse でのユーザー クエリの監視に関する考慮事項、ベスト プラクティス、およびタスクの概要。 |
| 39 | [Azure SQL Data Warehouse の復元 (概要)](sql-data-warehouse-restore-database-overview.md) | Azure SQL Data Warehouse でデータベースを復旧するためのデータベース復元オプションの概要。 |
| 40 | [Azure SQL Data Warehouse の復元 (ポータル)](sql-data-warehouse-restore-database-portal.md) | Azure SQL Data Warehouse を復元するための Azure ポータル タスク。 |
| 41 | [Azure SQL Data Warehouse の復元 (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Azure SQL Data Warehouse を復元するための PowerShell タスク。 |
| 42 | [Azure SQL Data Warehouse の復元 (REST API)](sql-data-warehouse-restore-database-rest-api.md) | Azure SQL Data Warehouse を復元するための REST API タスク。 |



## テーブルとインデックス

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 43 | [SQL Data Warehouse のテーブルのデータ型](sql-data-warehouse-tables-data-types.md) | Azure SQL Data Warehouse のテーブルのデータ型の概要です。 |
| 44 | [SQL Data Warehouse のテーブルの分散](sql-data-warehouse-tables-distribute.md) | Azure SQL Data Warehouse のテーブルの分散の概要です。 |
| 45 | [SQL Data Warehouse でのテーブルのインデックス作成](sql-data-warehouse-tables-index.md) | Azure SQL Data Warehouse でのテーブルのインデックス作成の概要です。 |
| 46 | [SQL Data Warehouse のテーブルの概要](sql-data-warehouse-tables-overview.md) | Azure SQL Data Warehouse テーブルの概要です。 |
| 47 | [SQL Data Warehouse でのテーブルのパーティション分割](sql-data-warehouse-tables-partition.md) | Azure SQL Data Warehouse でのテーブルのパーティション分割の概要です。 |
| 48 | [SQL Data Warehouse のテーブルの統計の管理](sql-data-warehouse-tables-statistics.md) | Azure SQL Data Warehouse のテーブルの統計の概要です。 |
| 49 | [SQL Data Warehouse の一時テーブル](sql-data-warehouse-tables-temporary.md) | Azure SQL Data Warehouse の一時テーブルの概要です。 |



## 統合

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 50 | [SQL Data Warehouse での Azure Data Factory の使用](sql-data-warehouse-integrate-azure-data-factory.md) | ソリューション開発のための、Azure SQL Data Warehouse での Azure Data Factory (ADF) の使用に関するヒント。 |
| 51 | [SQL Data Warehouse での Azure Machine Learning の使用](sql-data-warehouse-integrate-azure-machine-learning.md) | ソリューション開発のための、Azure SQL Data Warehouse での Azure Machine Learning の使用に関するヒント。 |
| 52 | [SQL Data Warehouse での Azure Stream Analytics の使用](sql-data-warehouse-integrate-azure-stream-analytics.md) | ソリューション開発のための、Azure SQL Data Warehouse での Azure Stream Analytics の使用に関するヒント。 |
| 53 | [SQL Data Warehouse での Power BI の使用](sql-data-warehouse-integrate-power-bi.md) | ソリューション開発のための、Azure SQL Data Warehouse での Power BI の使用に関するヒント。 |
| 54 | [SQL Data Warehouse と他のサービスの併用](sql-data-warehouse-overview-integrate.md) | SQL Data Warehouse と統合されたソリューションを持つツールとパートナー。 |



## Load

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 55 | [Azure Blob Storage から Azure SQL Data Warehouse へのデータの読み込み (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Azure Data Factory を使用してデータを読み込む方法 |
| 56 | [Azure Blob Storage ストレージから SQL Data Warehouse へのデータの読み込み (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | PolyBase を使用して、Azure Blob Storage から SQL Data Warehouse にデータを読み込む方法について説明します。パブリック データから Contoso Retail Data Warehouse スキーマにテーブルをいくつか読み込みます。 |
| 57 | [SQL Server から Azure SQL Data Warehouse へのデータの読み込み (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | bcp を使用して SQL Server からフラット ファイルにデータをエクスポートし、AZCopy を使用してデータを Azure Blob Storage にインポートし、PolyBase を使用してデータを Azure SQL Data Warehouse に取り込みます。 |
| 58 | [SQL Server から Azure SQL Data Warehouse へのデータの読み込み (フラット ファイル)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | データのサイズが小さい場合は、bcp を使用して SQL Server からフラット ファイルにデータをエクスポートし、そのデータを Azure SQL Data Warehouse に直接読み込むことができます。 |
| 59 | [SQL Server から Azure SQL Data Warehouse へのデータの読み込み (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | さまざまなデータ ソースから SQL Data Warehouse にデータを移動する SQL Server Integration Services (SSIS) パッケージを作成する方法について説明します。 |
| 60 | [SQL Data Warehouse で PolyBase によってデータを読み込む](sql-data-warehouse-load-from-sql-server-with-polybase.md) | bcp を使用して SQL Server からフラット ファイルにデータをエクスポートし、AZCopy を使用してデータを Azure Blob Storage にインポートし、PolyBase を使用してデータを Azure SQL Data Warehouse に取り込みます。 |
| 61 | [SQL Data Warehouse で PolyBase を使用するためのガイド](sql-data-warehouse-load-polybase-guide.md) | SQL Data Warehouse のシナリオで PolyBase を使用するためのガイドラインおよび推奨事項を説明します。 |
| 62 | [SQL Data Warehouse へのサンプル データのロード](sql-data-warehouse-load-sample-databases.md) | SQL Data Warehouse へのサンプル データのロード |
| 63 | [bcp を使用したデータの読み込み](sql-data-warehouse-load-with-bcp.md) | データ ウェアハウジングのシナリオに沿って、bcp の概要と、その使用方法を学習します。 |
| 64 | [Azure SQL Data Warehouse へのデータの読み込み](sql-data-warehouse-overview-load.md) | SQL Data Warehouse にデータを読み込むための一般的なシナリオについて説明します。これには PolyBase、Azure Blob Storage、フラット ファイル、およびディスク発送の使用が含まれます。サード パーティ製のツールを使用することもできます。 |



## 移行

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 65 | [SQL Data Warehouse への SQL コードの移行](sql-data-warehouse-migrate-code.md) | ソリューション開発のための Azure SQL Data Warehouse への SQL コードの移行に関するヒント |
| 66 | [データの移行](sql-data-warehouse-migrate-data.md) | ソリューション開発のための Azure SQL Data Warehouse へのデータの移行に関するヒント |
| 67 | [Data Warehouse 移行ユーティリティ (プレビュー)](sql-data-warehouse-migrate-migration-utility.md) | SQL Data Warehouse に移行します。 |
| 68 | [SQL Data Warehouse へのスキーマの移行](sql-data-warehouse-migrate-schema.md) | ソリューション開発のための Azure SQL Data Warehouse へのスキーマの移行に関するヒント。 |
| 69 | [SQL Data Warehouse へのソリューションの移行](sql-data-warehouse-overview-migrate.md) | Azure SQL Data Warehouse プラットフォームにソリューションを組み込むための移行ガイダンス。 |



## パートナー

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 70 | [SQL Data Warehouse ビジネス インテリジェンス パートナー](sql-data-warehouse-partner-business-intelligence.md) | SQL Data Warehouse をサポートするソリューションを提供するサードパーティのビジネス インテリジェンス パートナーの一覧。 |
| 71 | [SQL Data Warehouse データ統合パートナー](sql-data-warehouse-partner-data-integration.md) | Azure SQL Data Warehouse をサポートするデータ統合ソリューションを提供するサードパーティのパートナーの一覧。 |
| 72 | [SQL Data Warehouse データ管理パートナー](sql-data-warehouse-partner-data-management.md) | SQL Data Warehouse をサポートするソリューションを提供するサードパーティのデータ管理パートナーの一覧。 |



## リファレンス

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 73 | [SQL Data Warehouse の参照トピック](sql-data-warehouse-overview-reference.md) | SQL Data Warehouse の参照コンテンツ リンク。 |
| 74 | [SQL Data Warehouse の PowerShell コマンドレットと REST API](sql-data-warehouse-reference-powershell-cmdlets.md) | データベースの一時停止と再開など、Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを紹介します。 |
| 75 | [言語要素](sql-data-warehouse-reference-tsql-language-elements.md) | SQL Data Warehouse に使用される Transact-SQL 言語要素のリファレンス コンテンツへのリンクのリスト。 |
| 76 | [Transact-SQL トピック](sql-data-warehouse-reference-tsql-statements.md) | SQL Data Warehouse によって使用される Transact-SQL トピックのリファレンス コンテンツへのリンク。 |
| 77 | [システム ビュー](sql-data-warehouse-reference-tsql-system-views.md) | SQL Data Warehouse のシステム ビュー コンテンツへのリンク。 |



## セキュリティ

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 78 | [SQL Data Warehouse - 監査と動的データ マスキングのためのダウンレベル クライアントのサポート](sql-data-warehouse-auditing-downlevel-clients.md) | データ監査のための SQL Data Warehouse のダウンレベル クライアントのサポートについて説明します |
| 79 | [Azure SQL Data Warehouse での監査](sql-data-warehouse-auditing-overview.md) | Azure SQL Data Warehouse での監査の概要 |
| 80 | [SQL Data Warehouse での Transparent Data Encryption (TDE) の概要](sql-data-warehouse-encryption-tde.md) | SQL Data Warehouse での Transparent Data Encryption (TDE) の概要 |
| 81 | [Transparent Data Encryption (TDE) の概要](sql-data-warehouse-encryption-tde-tsql.md) | SQL Data Warehouse Transparent Data Encryption (TDE) TSQL の概要 |
| 82 | [SQL Data Warehouse でのデータベース保護](sql-data-warehouse-overview-manage-security.md) | ソリューション開発のための Azure SQL Data Warehouse でのデータベース保護に関するヒント。 |



## その他

| &nbsp; | タイトル | Description |
| --: | :-- | :-- |
| 83 | [SQL Data Warehouse 用に Visual Studio 2015 と SSDT をインストールする](sql-data-warehouse-install-visual-studio.md) | Azure SQL Data Warehouse 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします |
| 84 | [Premium Storage への移行の詳細](sql-data-warehouse-migrate-to-premium-storage.md) | Premium Storage へ既存の SQL Data Warehouse を移行する手順 |
| 85 | [脅威の検出の概要](sql-data-warehouse-security-threat-detection.md) | 脅威の検出を開始する方法 |
| 86 | [SQL Data Warehouse の容量制限](sql-data-warehouse-service-capacity-limits.md) | SQL Data Warehouse の接続、データベース、テーブル、およびクエリの最大値。 |
| 87 | [Azure SQL Data Warehouse のトラブルシューティング](sql-data-warehouse-troubleshoot.md) | Azure SQL Data Warehouse のトラブルシューティングを行います。 |

<!---HONumber=AcomDC_0824_2016-->