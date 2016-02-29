<properties
   pageTitle="Azure SQL Database でサポートされない T-SQL | Microsoft Azure"
   description="Azure SQL Database では完全にサポートされない Transact-SQL ステートメント"
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
   ms.date="02/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL Database の Transact-SQL の相違点


アプリケーションが依存する Transact-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方でサポートされます。アプリケーションに対してサポートされる機能の一部を次に示します。

- データ型。
- 演算子。
- 文字列、算術、論理、およびカーソルに関連する機能。

ただし、Azure SQL Database は、**マスター** データベースへの依存から機能を分離するように設計されています。その結果、サーバー レベルの多くのアクティビティは SQL Database には不適切であり、サポートされません。このトピックでは、SQL Database では完全にサポートされない機能について説明します。

さらに、通常、SQL Server で廃止になった機能は SQL Database でもサポートされません。

## SQL Database V12 にアップグレードする

このトピックでは、無料の SQL Database V12 にアップグレードする際、SQL Database で利用できる機能について説明します。V12 に関する詳細については、「[SQL Database V12 の新機能](sql-database-v12-whats-new.md)」を参照してください。SQL Database V12 ではパフォーマンスが改善され、管理しやすくなり、機能が追加されました。追加された機能の一覧を以下に示します。完全にサポートされている機能と部分的にサポートされている機能に分割されています。

## SQL Database V12 で部分的にサポートされる機能

SQL Database V12 では、該当 SQL Server 2016 Transact-SQL ステートメントに存在する引数の全部ではなく一部がサポートされています。たとえば、CREATE PROCEDURE ステートメントは利用できますが、CREATE PROCEDURE の WITH ENCRYPTION オプションは利用できません。各ステートメントのサポートされる領域については、リンクされている構文トピックを参照してください。

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

## SQL Database で部分的にサポートされる機能

- システム オブジェクトの照合順序
- 関連接続: エンドポイント ステートメント、ORIGINAL\_DB\_NAMEWindows 認証はログインまたは包含データベース ユーザーには利用できません。
- 3 部構成または 4 部構成の名前を使用したデータベース間クエリ (読み取り専用のデータベース間クエリは、エラスティック データベース クエリを使用してサポートされます)。
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
- .NET framework の [ CLR と SQL Server の統合](http://msdn.microsoft.com/library/ms254963.aspx)
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

Transact-SQL の文法、使用方法、例の詳細については、SQL Server オンライン ブックの「[Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx)」を参照してください。

### 「適用先」タグについて

TRANSACT-SQL リファレンスには、SQL Server 2008 以降のバージョンに関連するトピックが含まれています。通常は、トピック タイトルの下に、SQL Server のバージョンを示す「適用先」行が表示されます。その他の製品名なども示される可能性があります。多くの場合、同じ「適用先」タグに、Azure SQL Database も表示されます。「適用先」に Azure SQL Database が表示されない場合、そのトピックのコンテンツは Azure SQL Database には適用されません。「適用先」行に複数の製品が表示され、トピックが各製品に適用されるかどうかを示す小さなアイコンも表示されることがあります。

 たとえば、可用性グループは SQL Server 2012 で導入されました。可用性グループは SQL Server 2008、SQL Server 2008 R2、または Azure SQL Database には適用されないため、「**可用性グループの作成**」トピックでは、そのトピックが **SQL Server (SQL Server 2012 から現在のバージョンまで)** に適用されることが示されます。

場合によっては、トピックの一般的な項目を製品で使用できますが、製品の間には若干の違いがあります。相違点は、必要に応じてトピックの中で指示されます。

<!---HONumber=AcomDC_0218_2016-->