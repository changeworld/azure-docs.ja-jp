<properties
   pageTitle="Azure SQL Database Transact-SQL 情報 | Microsoft Azure"
   description="Azure SQL Database の Transact-SQL ステートメント"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/02/2015"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL Database Transact-SQL 情報

Most SQL Server 2016 Transact-SQL ステートメントは Microsoft Azure SQL Database で完全サポートされています。これには SQL Server のデータ型、演算子、文字列関数、算術関数、論理関数、カーソル関数、ほとんどのアプリケーションが依存するその他の Transact-SQL 要素が含まれます。部分的にサポートされる関数やサポートされない関数は SQL Database によるデータベースの管理方法の違い (ファイル、高い可用性、セキュリティ機能など) に関連するか、Service Broker など、特別目的の機能のためのものです。SQL Database はマスター データベースに対する依存から多くの機能を分離するため、サーバーレベルの多くのアクティビティが不適切となり、サポートされません。通常、SQL Server で廃止になった機能は SQL Database でサポートされません。

## SQL Database V12 にアップグレードする

このトピックでは、無料の SQL Database V12 にアップグレードする際、SQL Database で利用できる機能について説明します。V12 に関する詳細については、「[SQL Database V12 の新機能](sql-database-v12-whats-new.md)」を参照してください。SQL Database V12 ではパフォーマンスが改善され、管理しやすくなり、機能が追加されました。追加された機能の一覧を以下に示します。完全にサポートされている機能と部分的にサポートされている機能に分割されています。

## SQL Database V12 で部分的にサポートされている機能

SQL Database V12 では、該当 SQL Server 2016 Transact-SQL ステートメントに存在する引数の全部ではなく一部がサポートされています。たとえば、CREATE PROCEDURE ステートメントは利用できますが、CREATE PROCEDURE の WITH ENCRYPTION オプションは利用できません。各ステートメントのサポートされる領域については、リンクされている構文トピックを参照してください。

- CLR アセンブリ: [CREATE ASSEMBLY](https://msdn.microsoft.com/library/ms189524.aspx)
- データベース: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV は一般的に利用できる機能で利用できます
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
- 関連接続: エンドポイント ステートメント、ORIGINAL\_DB\_NAMEWindows 認証はログインまたは包含データベース ユーザーには利用できません。
- データベース間のクエリ、データベース間の所有者変更、TRUSTWORTHY 設定
- データ コレクター
- データベース ダイアグラム
- データベース メール
- DATABASEPROPERTY (代わりに DATABASEPROPERTYEX を使用)
- 分散トランザクション
- EXECUTE AS ログイン
- 暗号化: 拡張可能キー管理
- イベント: イベント、イベント通知、クエリ通知
- Microsoft Azure により自動的に管理されるデータベース ファイル置換、サイズ、データベース ファイルに関連する機能。
- Microsoft Azure アカウントを使用して管理される高可用性に関連する機能: バックアップ、復元、AlwaysOn、データベース ミラーリング、ログ配布、復旧モード。詳細については、「Azure SQL Database のバックアップと復元」を参照してください。
- ログ リーダーに依存する機能: レプリケーション、変更データ キャプチャ。
- SQL Server Agent または MSDB データベースに依存する機能: ジョブ、警告、演算子、ポリシーベースの管理、データベース メール、中央管理サーバー。
- FILESTREAM
- 関数: fn\_get\_sql, fn\_virtualfilestats、fn\_virtualservernodes
- グローバル一時テーブル
- ハードウェア関連のサーバー設定: メモリ、ワーカー スレッド、CPU アフィニティ、トレース フラグなど。代わりにサービス レベルを使用します。
- HAS\_DBACCESS
- KILL STATS JOB
- リンク サーバー、OPENQUERY、OPENROWSET、OPENDATASOURCE、BULK INSERT、3 つまたは 4 つの部品名
- マスター/対象サーバー
- リソース ガバナー
- セマンティック検索
- サーバー資格情報
- サーバーレベル項目: サーバー ロール、IS\_SRVROLEMEMBER、sys.login\_token。サーバーレベルのアクセス許可は利用できません。ただし、一部がデータベースレベルのアクセス許可で置換されます。一部のサーバーレベルの DMV は利用できません。ただし、一部がデータベースレベルの DMV で置換されます。
- サーバーレスの高速: localdb、ユーザー インスタンス
- Service Broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure オプションと RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server 監査 (代わりに SQL Database 監査を使用)
- SQL Server プロファイラー
- SQL Server トレース
- トレース フラグ
- Transact-SQL デバッグ
- トリガー: サーバー スコープ トリガーまたはログオン トリガー
- USE ステートメント

## 完全 Transact-SQL リファレンス

Transact-SQL の文法、使用方法、例の詳細については、SQL Server オンライン ブックの「[Transact-SQL リファレンス (データベース エンジン)](https://msdn.microsoft.com/library/bb510741.aspx)」を参照してください。

### 「適用先」タグについて

Transact-SQL リファレンスには SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014、Microsoft Azure SQL Database に関連するトピックが含まれています。各トピックの先頭近くにトピックの主題をサポートする製品を示すセクションがあります。製品が省略されている場合、トピックで説明されている機能はその製品で利用できません。たとえば、可用性グループは SQL Server 2012 で導入されました。**CREATE AVAILABILTY GROUP** トピックはそれが **SQL Server (現在のバージョンを介して SQL Server 2012)** に適用されることを示します。SQL Server 2008、SQL Server 2008 R2、Microsoft Azure SQL Database には適用されないためです。

トピックの全体的主題がある製品で使用されるが、一部の引数がサポートされないことがあります。たとえば、含有データベース ユーザーは SQL Server 2012 で導入されました。**CREATE USER** ステートメントは SQL Server 製品で利用できますが、**WITH PASSWORD** は以前のバージョンでは利用できません。この場合、追加の「**適用先**」セクションがトピックの本文の引数説明に挿入されます。

<!---HONumber=Nov15_HO2-->