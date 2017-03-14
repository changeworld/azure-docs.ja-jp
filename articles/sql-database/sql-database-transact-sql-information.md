---
title: "Azure SQL Database でサポートされない T-SQL | Microsoft Docs"
description: "Azure SQL Database では完全にサポートされない Transact-SQL ステートメント"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/22/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: a08d9f2ef29002f10473b0e041737c9c607f3ca0
ms.openlocfilehash: 7d6de93c99141248ea970ea668fb0b2191267b62
ms.lasthandoff: 03/01/2017


---
# <a name="azure-sql-database-transact-sql-differences"></a>Azure SQL Database の Transact-SQL の相違点   
アプリケーションが依存する Transact-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方でサポートされます。 たとえば、データ型、演算子、文字列、算術演算子、論理、およびカーソル機能などのコア SQL コンポーネントは、SQL Server との相違なしで動作します。

# <a name="why-some-transact-sql-is-not-supported"></a>一部の Transact-SQL がサポートされていないのはなぜですか。
Azure SQL Database は、マスター データベースおよびオペレーティング システムへの依存から機能を分離するように設計されています。 その結果、サーバー レベルの多くのアクティビティは SQL Database には不適切です。 Transact-SQL ステートメントは、サーバー レベルのオプション、オペレーティング システムのコンポーネントを構成した場合、またはファイル システムの構成を指定した場合は通常、使用できません。 ユーザー データベースの外にある機能が必要な場合は、SQL Database や別の Azure 機能またはサービスから代わりの適切な機能を使用できることがあります。 

たとえば、Azure SQL Database には高可用性が組み込まれており、すべてのデータベースで高い可用性が実現しているため、Always On は適用されません。 そのため、使用可能なグループに関連する Transact-SQL ステートメントは SQL Database でサポートされず、AlwaysOn に関連する動的管理ビューもサポートされていません。  

SQL Database でサポートされる機能とサポートされない機能の一覧については、[Azure SQL Database considerations, guidelines and features (Azure SQL Database の考慮事項、ガイドライン、および機能)](sql-database-features.md) に関する記事を参照してください。


## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>SQL Database で部分的にサポートされている Transact-SQL 構文
SQL Database では、データベースとログインを管理するための、該当する SQL Server 2016 Transact-SQL ステートメントに存在する引数の全部ではなく一部がサポートされています。 たとえば、`CREATE DATABASE` ステートメントは Azure SQL Database で使用できますが、SQL Server でサポートされるすべてのオプションが Azure SQL Database でサポートされるわけではありません。また、Azure SQL Database でサポートされるすべてのオプションが SQL Server でサポートされるわけではありません。 各ステートメントのサポートされる領域については、リンクされている構文トピックを参照してください。

- データベース: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/mt574871.aspx)   
- ログイン: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>SQL Database でサポートされない Transact-SQL 構文   
[Azure SQL Database considerations, guidelines and features (Azure SQL Database の考慮事項、ガイドライン、および機能)](sql-database-features.md) に関する記事に記載されている、サポートされていない機能に関連する Transact-SQL ステートメントの他に、次のステートメントとステートメントのグループはサポートされていません。
- システム オブジェクトの照合順序
- 関連接続: エンドポイント ステートメント、`ORIGINAL_DB_NAME`。 SQL データベースは Windows 認証をサポートしませんが、同様の Azure Active Directory 認証をサポートします。 いくつかの認証の種類には、最新バージョン の SSMS が必要です。 詳細については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」を参照してください。
- 3 部構成または&4; 部構成の名前を使用したデータベース間クエリ  (読み取り専用のデータベース間クエリは、[エラスティック データベース クエリ](sql-database-elastic-query-overview.md)を使用してサポートされます)。
- データベース間での所有権の継承、`TRUSTWORTHY` 設定
- `DATABASEPROPERTY` 代わりに `DATABASEPROPERTYEX` を使用してください。
- `EXECUTE AS LOGIN` 代わりに 'EXECUTE AS USER' を使用してください。
- 拡張キー管理以外で暗号化がサポートされています。
- イベント: イベント、イベント通知、クエリ通知
- Microsoft Azure により自動的に管理されるデータベース ファイル配置、サイズ、データベース ファイルに関連する構文。
- Microsoft Azure アカウントを通じて管理される高可用性に関連する構文。 これには、バックアップのための構文、復元、AlwaysOn、データベース ミラーリング、ログ配布、復旧モードが含まれます。
- SQL Database で使用できないログ リーダーに依存する構文: プッシュ レプリケーション、変更データ キャプチャ。 SQL Database は、プッシュ レプリケーションの記事のサブスクライバーである可能性があります。
- SQL Server エージェントまたは MSDB データベースに依存する構文: アラート、演算子、中央管理サーバー。 Azure PowerShell などのスクリプトを代わりに使用します。
- 関数: `fn_get_sql`、`fn_virtualfilestats`、`fn_virtualservernodes`
- グローバル一時テーブル
- ハードウェア関連のサーバー設定に関連する構文: メモリ、ワーカー スレッド、CPU アフィニティ、トレース フラグなど。代わりにサービス レベルを使用します。
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`、`OPENROWSET`、`OPENDATASOURCE`、および&4; 部構成の名前
- .NET framework の [CLR と SQL Server の統合](http://msdn.microsoft.com/library/ms254963.aspx)
- セマンティック検索
- サーバー資格情報。 代わりに データベース スコープの資格情報を使用してください。
- サーバー レベルの項目: サーバーの役割、`IS_SRVROLEMEMBER`、`sys.login_token`。 サーバー レベルのアクセス許可の `GRANT`、`REVOKE`、`DENY` は使用できませんが、一部はデータベース レベルのアクセス許可に置き換えられます。 いくつかの便利なサーバー レベルの DMV には、同等のデータベース レベルの DMV があります。
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` オプションと `RECONFIGURE`。 一部のオプションは、 [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)で使用できます。
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server 監査。 代わりに SQL Database 監査を使用してください。
- SQL Server トレース
- トレース フラグ。 トレース フラグのいくつかの項目は、互換性モードに移動されました。
- Transact-SQL デバッグ
- トリガー: サーバー スコープ トリガーまたはログオン トリガー
- `USE` ステートメント: データベース コンテキストを別のデータベースに変更するには、新しいデータベースへの接続を新たに確立する必要があります。

## <a name="full-transact-sql-reference"></a>完全 Transact-SQL リファレンス
Transact-SQL の文法、使用方法、例の詳細については、SQL Server オンライン ブックの「 [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) 」を参照してください。 

### <a name="about-the-applies-to-tags"></a>「適用先」タグについて
TRANSACT-SQL リファレンスには、SQL Server 2008 以降のバージョンに関連するトピックが含まれています。 トピック タイトルの下に、4 つの SQL Server プラットフォームを一覧表示して、適用性を示すアイコン バーがあります。 たとえば、可用性グループは SQL Server 2012 で導入されました。 [CREATE AVAILABILITY GROUP (可用性グループの作成)](https://msdn.microsoft.com/library/ff878399.aspx) トピックでは、そのステートメントが **SQL Server (2012 年以降)** に適用されることが示されます 。 ステートメントは、SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure SQL Data Warehouse、または並列データ ウェアハウスには適用されません。

場合によっては、トピックの一般的な項目を製品で使用できますが、製品の間には若干の違いがあります。 相違点は、必要に応じてトピックの中で指示されます。

