---
title: "Azure SQL Data Warehouse のトラブルシューティング | Microsoft Docs"
description: "Azure SQL Data Warehouse のトラブルシューティングを行います。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 18d3a5eca0b272a3fbe48c06e668c33aff2b3f11


---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のトラブルシューティング
このトピックでは、お客様から寄せられる一般的なトラブルシューティングに関する質問の一部を一覧で紹介します。

## <a name="connecting"></a>接続
| 問題 | 解決策 |
|:--- |:--- |
| ユーザー ' NT AUTHORITY\ANONYMOUS LOGON' はログインできませんでした。 (Microsoft SQL Server、エラー: 18456) |このエラーは、AAD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。  この問題を解決するには、接続時に接続する SQL Data Warehouse を指定するか、master データベースにユーザーを追加します。  詳細については、[セキュリティの概要][セキュリティの概要]に関する記事をご覧ください。 |
| サーバー プリンシパル"MyUserName" が、現在のセキュリティ コンテキストでデータベース "master" にアクセスできません。 ユーザー既定のデータベースを開けません。 ログインできませんでした。 ユーザー 'MyUserName' はログインできませんでした。 (Microsoft SQL Server、エラー: 916) |このエラーは、AAD ユーザーが master データベースに接続しようとするが、マスターにユーザーがいない場合に発生します。  この問題を解決するには、接続時に接続する SQL Data Warehouse を指定するか、master データベースにユーザーを追加します。  詳細については、[セキュリティの概要][セキュリティの概要]に関する記事をご覧ください。 |
| CTAIP エラー |このエラーは、ログインが SQL Data Warehouse データベースではなく、SQL Server マスター データベース上で作成された場合に発生する可能性があります。  このエラーが発生した場合は、[セキュリティの概要][セキュリティの概要]に関する記事をご覧ください。  この記事では、マスター上にログインとユーザーを作成する方法と、SQL Data Warehouse データベースにユーザーを作成する方法について説明しています。 |
| ファイアウォールによってブロックされる |Azure SQL Database は、既知の IP アドレスのみがデータベースにアクセスできるように、サーバーとデータベース レベルのファイアウォールによって保護されています。 ファイアウォールは、既定でセキュリティ保護されています。つまり、接続する前に、IP アドレスまたはアドレス範囲を明示的に有効にする必要があります。  アクセスできるようにファイアウォールを構成するには、[プロビジョニング手順][プロビジョニング手順]に関するページの、[クライアントの IP からアクセスできるようにサーバーのファイアウォールを構成する][クライアントの IP からアクセスできるようにサーバーのファイアウォールを構成する]に関するトピックの手順に従ってください。 |
| ツールまたはドライバーで接続できない |SQL Data Warehouse では、[SSMS][SSMS]、[Visual Studio 2015 用の SSDT][Visual Studio 2015 用の SSDT]、または [sqlcmd][sqlcmd] を使用してデータのクエリを実行することをお勧めします。 ドライバーおよび SQL Data Warehouse への接続に関する詳細については、「[Azure SQL Data Warehouse のドライバー][Azure SQL Data Warehouse のドライバー]」と「[Azure SQL Data Warehouse への接続][Azure SQL Data Warehouse への接続]」の記事をご覧ください。 |

## <a name="tools"></a>ツール
| 問題 | 解決策 |
|:--- |:--- |
| Visual Studio オブジェクト エクスプローラーに AAD ユーザーが表示されない |これは既知の問題です。  回避策として、ユーザーを [sys.database_principals][sys.database_principals] で表示してください。  SQL Data Warehouse での Azure Active Directory の使用方法の詳細については、[Azure SQL Data Warehouse への認証][Azure SQL Data Warehouse への認証]に関するページをご覧ください。 |

## <a name="performance"></a>パフォーマンス
| 問題 | 解決策 |
|:--- |:--- |
| クエリ パフォーマンスのトラブルシューティング |特定のクエリのトラブルシューティングを行う場合は、[クエリを監視する方法][クエリを監視する方法]に関する記事をご覧ください。 |
| 統計情報の不足を原因とする、クエリのパフォーマンス低下と不適切なプラン |パフォーマンスの低下の最も一般的な原因は、テーブルの統計情報の不足です。  統計を作成する方法と、統計情報がパフォーマンスにとって重要な理由の詳細については、[テーブルの統計情報の更新][統計]に関するページをご覧ください。 |
| 低い同時実行性とキューに置かれたクエリ |[ワークロード管理][ワークロード管理]を理解することは、同時実行でのメモリの割り当てのバランスを調整する方法を理解するために重要です。 |
| ベスト プラクティスを実装する方法 |クエリのパフォーマンスを向上させる方法については、[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse のベスト プラクティス]に関する記事をご覧ください。 |
| スケーリングでパフォーマンスを向上させる方法 |[SQL Data Warehouse をスケーリング][SQL Data Warehouse をスケーリング]し、クエリのコンピューティング能力を強化するだけで、パフォーマンスを改善できる場合があります。 |
| インデックスの品質が低いことによるクエリ パフォーマンスの低下 |[列ストア インデックスの品質の低さ][列ストア インデックスの品質の低さ]が原因で、クエリの処理速度が低下する場合があります。  詳細と[インデックスを再構築してセグメントの品質を向上する][インデックスを再構築してセグメントの品質を向上する]方法に関する記事をご覧ください。 |

## <a name="system-management"></a>システム管理
| 問題 | 解決策 |
|:--- |:--- |
| メッセージ 40847: サーバーが許容データベース トランザクション単位クォータ 45000 を超えることになるため、操作を実行できませんでした。 |作成するデータベースの [DWU][DWU] を減らすか、[クォータの引き上げを要求][クォータの引き上げを要求]してください。 |
| 領域使用率の調査 |システムの領域使用率の詳細については、[テーブルのサイズ][テーブルのサイズ]に関するトピックをご覧ください。 |
| テーブルの管理に関するヘルプ |テーブルの管理に関するヘルプについては、[テーブルの概要][概要]に関する記事をご覧ください。  この記事では、[テーブルのデータ型][データ型]、[テーブルの分散][分散]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[テーブル統計の更新][統計]、[一時テーブル][一時]などのより詳細なトピックへのリンクも紹介しています。 |

## <a name="polybase"></a>PolyBase
| 問題 | 解決策 |
|:--- |:--- |
| UTF-8 エラー |現在、PolyBase では、UTF-8 でエンコードされたデータ ファイルの読み込みのみをサポートしています。  この制限を回避する方法については、「[PolyBase UTF-8 要件に対処する][PolyBase UTF-8 要件に対処する]」をご覧ください。 |
| サイズの大きい行を原因とする読み込みの失敗 |サイズの大きい行は、現在 PolyBase でサポートされていません。  つまり、テーブルに VARCHAR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) が含まれる場合は、データの読み込みに外部テーブルを使用できません。  サイズの大きい行の読み込みは現在、Azure Data Factory (BCP を使用)、Azure Stream Analytics、SSIS、BCP、.NET の SQLBulkCopy クラスのみでサポートされています。 PolyBase でのサイズの大きい行のサポートは、将来のリリースで追加されます。 |
| MAX データ型を持つテーブルの bcp 読み込みの失敗 |VARCHAR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) は、既知の問題により、一部のシナリオでテーブルの最後に配置する必要があります。  データ型が MAX の列をテーブルの末尾に移動してみてください。 |

## <a name="differences-from-sql-database"></a>SQL Database との違い
| 問題 | 解決策 |
|:--- |:--- |
| サポートされていない SQL Database の機能 |「[サポートされていないテーブルの機能][サポートされていないテーブルの機能]」をご覧ください。 |
| サポートされていない SQL Database のデータ型 |「[サポートされていないデータ型][サポートされていないデータ型]」をご覧ください。 |
| DELETE と UPDATE の制限事項 |「[UPDATE の対処方法][UPDATE の対処方法]、「[DELETE の対処方法][DELETE の対処方法]」、「[サポートされていない UPDATE と DELETE の構文を回避するための CTAS の使用][サポートされていない UPDATE と DELETE の構文を回避するための CTAS の使用]」をご覧ください。 |
| MERGE ステートメントがサポートされていない |「[MERGE の対処方法][MERGE の対処方法]」をご覧ください。 |
| ストアド プロシージャの制限事項 |ストアド プロシージャの制限事項について理解するには、[ストアド プロシージャの制限事項][ストアド プロシージャの制限事項]に関するページをご覧ください。 |
| UDF が SELECT ステートメントをサポートしていない |これは、UDF の現在の制限です。  サポートされている構文については、[CREATE FUNCTION][CREATE FUNCTION]に関するページをご覧ください。 |

## <a name="next-steps"></a>次のステップ
上記の方法で問題を解決できなかった場合は、次に示すその他のリソースを参照してください。

* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [CAT チームのブログ]
* [サポート チケットを作成する]
* [MSDN フォーラム]
* [Stack Overflow フォーラム]
* [Twitter]

<!--Image references-->

<!--Article references-->
[セキュリティの概要]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[Visual Studio 2015 用の SSDT]: ./sql-data-warehouse-install-visual-studio.md
[Azure SQL Data Warehouse のドライバー]: ./sql-data-warehouse-connection-strings.md
[Azure SQL Data Warehouse への接続]: ./sql-data-warehouse-connect-overview.md
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[SQL Data Warehouse をスケーリング]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[クォータの引き上げを要求]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[クエリを監視する方法]: ./sql-data-warehouse-manage-monitor.md
[プロビジョニング手順]: ./sql-data-warehouse-get-started-provision.md
[クライアントの IP からアクセスできるようにサーバーのファイアウォールを構成する]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[テーブルのサイズ]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[サポートされていないテーブルの機能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[サポートされていないデータ型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[概要]: ./sql-data-warehouse-tables-overview.md
[データ型]: ./sql-data-warehouse-tables-data-types.md
[分散]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[統計]: ./sql-data-warehouse-tables-statistics.md
[一時]: ./sql-data-warehouse-tables-temporary.md
[列ストア インデックスの品質の低さ]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[インデックスを再構築してセグメントの品質を向上する]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[ワークロード管理]: ./sql-data-warehouse-develop-concurrency.md
[サポートされていない UPDATE と DELETE の構文を回避するための CTAS の使用]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE の対処方法]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE の対処方法]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE の対処方法]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[ストアド プロシージャの制限事項]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Azure SQL Data Warehouse への認証]: ./sql-data-warehouse-authentication.md
[PolyBase UTF-8 要件に対処する]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT チームのブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse




<!--HONumber=Nov16_HO3-->


