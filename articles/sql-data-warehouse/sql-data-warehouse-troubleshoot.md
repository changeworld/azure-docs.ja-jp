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
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL Data Warehouse のトラブルシューティング

このトピックでは、お客様から寄せられる一般的なトラブルシューティングに関する質問の一部を一覧で紹介します。

## 接続

| 問題 | 解決策 |
| :----------------------------------| :---------------------------------------------- |
| ユーザー ' NT AUTHORITY\\ANONYMOUS LOGON' はログインできませんでした。(Microsoft SQL Server、エラー: 18456) | このエラーは、AAD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。この問題を解決するには、接続時に接続する SQL Data Warehouse を指定するか、master データベースにユーザーを追加します。詳細については、[セキュリティの概要][]に関する記事を参照してください。|
|サーバー プリンシパル"MyUserName" が、現在のセキュリティ コンテキストでデータベース "master" にアクセスできません。ユーザー既定のデータベースを開けません。ログインできませんでした。ユーザー 'MyUserName' はログインできませんでした。(Microsoft SQL Server、エラー: 916) | このエラーは、AAD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。この問題を解決するには、接続時に接続する SQL Data Warehouse を指定するか、master データベースにユーザーを追加します。詳細については、[セキュリティの概要][]に関する記事を参照してください。|
| CTAIP エラー | このエラーは、ログインが SQL Data Warehouse データベースではなく、SQL Server マスター データベース上で作成された場合に発生する可能性があります。このエラーが発生した場合は、[セキュリティの概要][]に関する記事を参照してください。この記事では、マスター上にログインとユーザーを作成する方法と、SQL Data Warehouse データベースにユーザーを作成する方法について説明しています。|
| ファイアウォールによってブロックされる |Azure SQL Database は、既知の IP アドレスのみがデータベースにアクセスできるように、サーバーとデータベース レベルのファイアウォールによって保護されています。ファイアウォールは、既定でセキュリティ保護されています。つまり、接続する前に、IP アドレスまたはアドレス範囲を明示的に有効にする必要があります。アクセスできるようにファイアウォールを構成するには、[プロビジョニング手順][]に関するページの、[クライアント IP 用のサーバー ファイアウォール アクセスの構成][]に関するトピックの手順に従ってください。|
| ツールまたはドライバーで接続できない | SQL Data Warehouse では、[SSMS][]、[SSDT for Visual Studio 2015][]、または [sqlcmd][] を使用してデータのクエリを実行することをお勧めします。ドライバーおよび SQL Data Warehouse への接続に関する詳細については、「[Azure SQL Data Warehouse のドライバー][]」と「[Azure SQL Data Warehouse への接続][]」の記事を参照してください。|


## ツール

| 問題 | 解決策 |
| :----------------------------------| :---------------------------------------------- |
| Visual Studio オブジェクト エクスプローラーに AAD ユーザーが表示されない | これは既知の問題です。回避策として、ユーザーを [sys.database\_principals][] で表示してください。SQL Data Warehouse での Azure Active Directory の使用方法の詳細については、[Azure SQL Data Warehouse への認証][]に関するページを参照してください。|

## パフォーマンス

| 問題 | 解決策 |
| :----------------------------------| :---------------------------------------------- |
| クエリ パフォーマンスのトラブルシューティング | 特定のクエリのトラブルシューティングを行う必要がある場合は、[クエリを監視する方法][]に関する記事を参照してください。|
| 統計情報の不足を原因とする、クエリのパフォーマンス低下と不適切なプラン | パフォーマンスの低下の最も一般的な原因は、テーブルの統計情報の不足です。統計を作成する方法と、統計情報がパフォーマンスにとって重要な理由の詳細については、[テーブルの統計情報の更新][Statistics]に関するページを参照してください。|
| 低い同時実行性とキューに置かれたクエリ | [ワークロード管理][]を理解することは、同時実行でのメモリの割り当てのバランスを調整する方法を理解するために重要です。|
| ベスト プラクティスを実装する方法 | クエリのパフォーマンスを向上させる方法については、[SQL Data Warehouse のベスト プラクティス][]に関する記事を参照してください。|
| スケーリングでパフォーマンスを向上させる方法 | [SQL Data Warehouse をスケーリング][]し、クエリのコンピューティング能力を強化するだけで、パフォーマンスを改善できる場合があります。|
| インデックスの品質が低いことによるクエリ パフォーマンスの低下 | [列ストア インデックスの品質の低さ][]が原因で、クエリの処理速度が低下する場合があります。詳細と[インデックスを再構築してセグメントの品質を向上する][]方法に関する記事を参照してください。|

## システム管理

| 問題 | 解決策 |
| :----------------------------------| :---------------------------------------------- |
| メッセージ 40847: サーバーが許容データベース トランザクション単位クォータ 45000 を超えることになるため、操作を実行できませんでした。 | 作成するデータベースの [DWU][] を減らすか、[クォータの引き上げを要求][]してください。|
| 領域使用率の調査 | システムの領域使用率の詳細については、[テーブルのサイズ][]に関するトピックをご覧ください。|
| テーブルの管理に関するヘルプ | テーブルの管理に関するヘルプについては、[テーブルの概要][Overview]に関する記事をご覧ください。この記事では、[テーブルのデータ型][Data types]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[テーブル統計の更新][Statistics]、[一時テーブル][Temporary]などのより詳細なトピックへのリンクも紹介しています。|

## PolyBase

| 問題 | 解決策 |
| :----------------------------------| :---------------------------------------------- |
| UTF-8 エラー | 現在、PolyBase では、UTF-8 でエンコードされたデータ ファイルの読み込みのみをサポートしています。この制限を回避する方法については、「[PolyBase UTF-8 要件に対処する][]」をご覧ください。|
| サイズの大きい行を原因とする読み込みの失敗 | サイズの大きい行は、現在 PolyBase でサポートされていません。つまり、テーブルに VARCHAR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) が含まれる場合は、データの読み込みに外部テーブルを使用できません。サイズの大きい行の読み込みは現在、Azure Data Factory (BCP を使用)、Azure Stream Analytics、SSIS、BCP、.NET の SQLBulkCopy クラスのみでサポートされています。PolyBase でのサイズの大きい行のサポートは、将来のリリースで追加されます。|
| MAX データ型を持つテーブルの bcp 読み込みの失敗 | VARCHAR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) は、既知の問題により、一部のシナリオでテーブルの最後に配置する必要があります。データ型が MAX の列をテーブルの末尾に移動してみてください。|

## SQL Database との違い

| 問題 | 解決策 |
| :----------------------------------| :---------------------------------------------- |
| サポートされていない SQL Database の機能 | 「[サポートされていないテーブルの機能][]」をご覧ください。|
| サポートされていない SQL Database のデータ型 | 「[サポートされていないデータ型][]」をご覧ください。|
| DELETE と UPDATE の制限事項 | [UPDATE の回避策][]、[DELETE の回避策][]、[サポートされていない UPDATE と DELETE の構文を回避するための CTAS の使用][]に関する各トピックをご覧ください。 |
| MERGE ステートメントがサポートされていない | [MERGE の対処方法][]に関するページをご覧ください。|
| ストアド プロシージャの制限事項 | ストアド プロシージャの制限事項のいくつかを理解するには、[ストアド プロシージャの制限事項][]に関するページをご覧ください。|
| UDF が SELECT ステートメントをサポートしていない | これは、UDF の現在の制限です。サポートされている構文については、[CREATE FUNCTION][] に関するページをご覧ください。 |

## 次のステップ

上記の方法で問題を解決できなかった場合は、次に示すその他のリソースを参照してください。

- [ブログ]
- [機能に関する要求]
- [ビデオ]
- [CAT チームのブログ]
- [サポート チケットを作成する]
- [MSDN フォーラム]
- [Stack Overflow フォーラム]
- [Twitter]

<!--Image references-->

<!--Article references-->
[セキュリティの概要]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Azure SQL Data Warehouse のドライバー]: ./sql-data-warehouse-connection-strings.md
[Azure SQL Data Warehouse への接続]: ./sql-data-warehouse-connect-overview.md
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[SQL Data Warehouse をスケーリング]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[クォータの引き上げを要求]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[クエリを監視する方法]: ./sql-data-warehouse-manage-monitor.md
[プロビジョニング手順]: ./sql-data-warehouse-get-started-provision.md
[クライアント IP 用のサーバー ファイアウォール アクセスの構成]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[テーブルのサイズ]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[サポートされていないテーブルの機能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[サポートされていないデータ型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[列ストア インデックスの品質の低さ]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[インデックスを再構築してセグメントの品質を向上する]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[ワークロード管理]: ./sql-data-warehouse-develop-concurrency.md
[サポートされていない UPDATE と DELETE の構文を回避するための CTAS の使用]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE の回避策]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE の回避策]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE の対処方法]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[ストアド プロシージャの制限事項]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Azure SQL Data Warehouse への認証]: ./sql-data-warehouse-authentication.md
[PolyBase UTF-8 要件に対処する]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database\_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT チームのブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0907_2016-->