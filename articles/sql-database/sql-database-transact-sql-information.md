<properties
   pageTitle="Azure SQL Database でサポートされない T-SQL | Microsoft Azure"
   description="Azure SQL Database では完全にサポートされない Transact-SQL ステートメント"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL Database の Transact-SQL の相違点


アプリケーションが依存する Transact-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方でサポートされます。アプリケーションに対してサポートされる機能の一部を次に示します。

- データ型。
- 演算子。
- 文字列、算術、論理、およびカーソルに関連する機能。

ただし、Azure SQL Database は、**マスター** データベースへの依存から機能を分離するように設計されています。その結果、サーバー レベルの多くのアクティビティは SQL Database には不適切であり、サポートされません。通常、SQL Server で廃止になった機能は SQL Database でサポートされません。

> [AZURE.NOTE]
このトピックでは、最新バージョン SQL Database V12 にアップグレードする際、SQL Database で利用できる機能について説明します。V12 に関する詳細については、「[SQL Database V12 の新機能](sql-database-v12-whats-new.md)」を参照してください。

次のセクションでは、一部サポートされている機能と、完全にサポートされていない機能を示します。


## SQL Database V12 で部分的にサポートされる機能

SQL Database V12 では、該当 SQL Server 2016 Transact-SQL ステートメントに存在する引数の全部ではなく一部がサポートされています。たとえば、CREATE PROCEDURE ステートメントは利用できますが、CREATE PROCEDURE のすべてのオプションを利用できるわけではありません。各ステートメントのサポートされる領域については、リンクされている構文トピックを参照してください。

- データベース: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV は一般的に利用できる機能で利用できます。
- 関数: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx)
- ログイン: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- ストアド プロシージャ: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- テーブル: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- タイプ (カスタム): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- ユーザー: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- ビュー: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## SQL Database でサポートされていない機能

- システム オブジェクトの照合順序
- 関連接続: エンドポイント ステートメント、ORIGINAL\_DB\_NAMESQL データベースは Windows 認証をサポートしませんが、同様の Azure Active Directory 認証をサポートします。いくつかの認証の種類には、最新バージョン の SSMS が必要です。詳細については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」を参照してください。
- 3 部構成または 4 部構成の名前を使用したデータベース間クエリ (読み取り専用のデータベース間クエリは、[エラスティック データベース クエリ](sql-database-elastic-query-overview.md)を使用してサポートされます)。
- データベース間での所有権の継承、TRUSTWORTHY 設定
- データ コレクター
- データベース ダイアグラム
- データベース メール
- DATABASEPROPERTY (代わりに DATABASEPROPERTYEX を使用)
- EXECUTE AS ログイン
- 暗号化: 拡張可能キー管理
- イベント: イベント、イベント通知、クエリ通知
- Microsoft Azure により自動的に管理されるデータベース ファイル置換、サイズ、データベース ファイルに関連する機能。
- Microsoft Azure アカウントを使用して管理される高可用性に関連する機能: バックアップ、復元、AlwaysOn、データベース ミラーリング、ログ配布、復旧モード。詳細については、「Azure SQL Database のバックアップと復元」を参照してください。
- SQL Database で実行されているログ リーダーに依存する機能: プッシュ レプリケーション、変更データ キャプチャ。
- SQL Server Agent または MSDB データベースに依存する機能: ジョブ、警告、演算子、ポリシーベースの管理、データベース メール、中央管理サーバー。
- FILESTREAM
- 関数: fn\_get\_sql, fn\_virtualfilestats、fn\_virtualservernodes
- グローバル一時テーブル
- ハードウェア関連のサーバー設定: メモリ、ワーカー スレッド、CPU アフィニティ、トレース フラグなど。代わりにサービス レベルを使用します。
- HAS\_DBACCESS
- KILL STATS JOB
- リンク サーバー、OPENQUERY、OPENROWSET、OPENDATASOURCE、BULK INSERT、3 つまたは 4 つの部品名
- マスター/対象サーバー
- .NET framework の [ CLR と SQL Server の統合](http://msdn.microsoft.com/library/ms254963.aspx)
- リソース ガバナー
- セマンティック検索
- サーバー資格情報。代わりに データベース スコープの資格情報を使用してください。
- サーバーレベル項目: サーバー ロール、IS\_SRVROLEMEMBER、sys.login\_token。サーバーレベルのアクセス許可は利用できません。ただし、一部がデータベースレベルのアクセス許可で置換されます。一部のサーバーレベルの DMV は利用できません。ただし、一部がデータベースレベルの DMV で置換されます。
- サーバーレスの高速: localdb、ユーザー インスタンス
- Service Broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure オプションと RECONFIGURE。一部のオプションは、[ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) で使用できます。
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server 監査。代わりに SQL Database 監査を使用してください。
- SQL Server プロファイラー
- SQL Server トレース
- トレース フラグ。トレース フラグのいくつかの項目は、互換性モードに移動されました。
- Transact-SQL デバッグ
- トリガー: サーバー スコープ トリガーまたはログオン トリガー
- USE ステートメント: データベース コンテキストを別のデータベースに変更するには、新しいデータベースへの接続を新たに確立する必要があります。


## 完全 Transact-SQL リファレンス

Transact-SQL の文法、使用方法、例の詳細については、SQL Server オンライン ブックの「[Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx)」を参照してください。

### 「適用先」タグについて

TRANSACT-SQL リファレンスには、SQL Server 2008 以降のバージョンに関連するトピックが含まれています。トピック タイトルの下に、4 つの SQL Server プラットフォームを一覧表示して、適用性を示すアイコン バーがあります。たとえば、可用性グループは SQL Server 2012 で導入されました。[可用性グループの作成](https://msdn.microsoft.com/library/ff878399.aspx)トピックでは、そのステートメントが** SQL Server (2012 年以降) に適用されることが示されます 。ステートメントは、SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure SQL Data Warehouse、または並列データ ウェアハウスには適用されません。

場合によっては、トピックの一般的な項目を製品で使用できますが、製品の間には若干の違いがあります。相違点は、必要に応じてトピックの中で指示されます。

<!---HONumber=AcomDC_0831_2016-->