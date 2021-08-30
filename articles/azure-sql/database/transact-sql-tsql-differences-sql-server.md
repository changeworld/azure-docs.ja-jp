---
title: T-SQL の相違点を解決する-移行
description: Azure SQL Database で完全にはサポートされていない T-SQL ステートメント。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 06/17/2021
ms.openlocfilehash: 479800be0301d40fb1f20127f3d35fd11e7ba69e
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004594"
---
# <a name="t-sql-differences-between-sql-server-and-azure-sql-database"></a>SQL Server と Azure SQL Database の T-SQL の相違点

SQL Server から Azure SQL Database に[データベースを移行する](migrate-to-database-from-sql-server.md)際、事前に SQL Server データベースになんらかの設計変更が必要であると判明する場合があります。 この記事では、再構築を実行し、再構築が必要な根本的な原因を理解するうえで役立つガイダンスを提供します。 非互換性を検出してデータベースを Azure SQL Database に移行するには、[Data Migration Assistant (DMA)](/sql/dma/dma-overview) を使用します。

## <a name="overview"></a>概要

アプリケーションが使用する T-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方で完全にサポートされます。 たとえば、データ型、演算子、文字列、算術、論理、カーソル関数などのコア SQL コンポーネントは、SQL Server と SQL Database で同様に動作します。 ただし、DDL (データ定義言語) と DML (データ操作言語) 要素における T-SQL のいくつかの相違点により、T-SQL ステートメントとクエリは部分的にしかサポートされません (これについてはこの記事で後ほど説明します)。

さらに、Azure SQL Database はシステム データベースとオペレーティング システムへの依存関係から機能を分離するように設計されているため、まったくサポートされない機能や構文がいくつか存在します。 そのような事情から、インスタンスレベルの機能は、その大半が SQL Database ではサポートされません。 T-SQL ステートメントとオプションは、インスタンスレベルのオプションやオペレーティング システムのコンポーネントを構成するか、またはファイル システムの構成を指定する場合は使用できません。 このような機能が必要な場合は、SQL Database や別の Azure 機能またはサービスから代わりの適切な機能を使用できることがあります。

たとえば、Azure SQL Database には高可用性が組み込まれています。 可用性グループに関連する T-SQL ステートメントは SQL Database でサポートされず、AlwaysOn 可用性グループに関連する動的管理ビューもサポートされていません。

SQL Database でサポートされる機能とサポートされない機能の一覧については、[Azure SQL Database の機能の比較](features-comparison.md)に関する記事を参照してください。 このページは、その記事を補完する内容となっており、T-SQL ステートメントを中心に取り上げています。

## <a name="t-sql-syntax-statements-with-partial-differences"></a>部分的に異なる T-SQL 構文のステートメント

主要な DDL ステートメントは利用できますが、ディスクへのファイルの配置など、サポート対象外の機能に関連した DDL ステートメントの拡張機能はサポートされません。

- SQL Server では、`CREATE DATABASE` ステートメントと `ALTER DATABASE` ステートメントに 30 を超えるオプションがあります。 ステートメントには SQL Server にのみ適用されるファイルの配置、FILESTREAM、および Service Broker のオプションが含まれます。 これは、移行する前に SQL Database にデータベースを作成する場合は問題になりませんが、データベースを作成する T-SQL コードを移行している場合は、[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) を [CREATE DATABASE (SQL Server T-SQL)](/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver15&preserve-view=true) にある SQL Server 構文と比較して、使用するすべてのオプションがサポートされていることを確認する必要があります。 また Azure SQL Database 用の `CREATE DATABASE` には、SQL Database にのみ適用されるサービス目標とエラスティック プール オプションがあります。
- `CREATE TABLE` ステートメントと `ALTER TABLE` ステートメントの `FILETABLE` オプションと `FILESTREAM` オプションは、SQL Database ではサポートされない機能であり使用できません。
- `CREATE LOGIN` ステートメントと `ALTER LOGIN` ステートメントはサポートされますが、SQL Server で利用できるオプションの一部が提供されません。 データベースの移植性を高めるために、SQL Database は可能な限りログインの代わりに包含データベース ユーザーを使用することを推奨しています。 詳細については、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true)」と「[ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql?view=azuresqldb-current&preserve-view=true)」および「[ログインとユーザーの管理](logins-create-manage.md)」を参照してください。

## <a name="t-sql-syntax-not-supported-in-azure-sql-database"></a>Azure SQL Database でサポートされない T-SQL 構文

[Azure SQL Database の機能の比較](features-comparison.md)に関する記事で説明されている、サポートされていない機能に関連する T-SQL ステートメントに加えて、次のステートメントとステートメントのグループはサポートされていません。 そのため、移行するデータベースが次の機能のいずれかを使用している場合は、アプリケーションの設計を変更してこれらの T-SQL の機能とステートメントを取り除きます。

- システム オブジェクトの照合順序。
- 接続関連:エンドポイント ステートメント。 SQL Database は Windows 認証をサポートしていませんが、Azure Active Directory 認証はサポートしています。 これには、Azure Active Directory とフェデレーションされた Active Directory プリンシパルの認証も含まれます。 詳細については、[Azure Active Directory 認証を使用した SQL Database または Azure Synapse Analytics への接続](authentication-aad-overview.md)に関する記事を参照してください。
- 3 部構成または 4 部構成の名前を使用したデータベース間クエリとインスタンス間クエリ。 `tempdb` データベースおよび現在のデータベースを参照する 3 部構成の名前はサポートされます。 [エラスティック クエリ](elastic-query-overview.md)では、他の MSSQL データベースにあるテーブルへの読み取り専用の参照がサポートされます。
- データベース間での所有権の継承と `TRUSTWORTHY` データベース プロパティ。
- `EXECUTE AS LOGIN`. 代わりに `EXECUTE AS USER` を使用してください
- 暗号化キーの拡張キー管理 (EKM)。 Transparent Data Encryption (TDE) の[カスタマー マネージド キー](transparent-data-encryption-byok-overview.md)と Always Encrypted の[列マスター キー](always-encrypted-azure-key-vault-configure.md)は Azure Key Vault に保存することができます。
- イベント: イベント通知、クエリ通知。
- ファイル プロパティ: データベース ファイルの名前、配置、サイズなど、ファイルのプロパティは SQL Database によって自動的に管理されます。
- 高可用性: 高可用性とデータベース復旧に関連した構文。これらは SQL Database によって管理されます。 これには、バックアップのための構文、復元、AlwaysOn、データベース ミラーリング、ログ配布、復旧モードが含まれます。
- スナップショット レプリケーション、トランザクション レプリケーション、マージ レプリケーションに関連した構文。これは SQL Database では利用できません。 [レプリケーション サブスクリプション](replication-to-sql-database.md)はサポートされます。
- 関数: `fn_get_sql`、`fn_virtualfilestats`、`fn_virtualservernodes`。
- インスタンスの構成: サーバー メモリ、ワーカー スレッド、CPU 関係、トレース フラグに関連した構文。 代わりにサービス レベルとコンピューティング サイズを使用します。
- `KILL STATS JOB`.
- `OPENQUERY`、`OPENDATASOURCE`、および 4 部構成の名前。
- .NET Framework: CLR 統合
- セマンティック検索
- サーバー資格情報:代わりに[データベース スコープの資格情報](/sql/t-sql/statements/create-database-scoped-credential-transact-SQL)を使用してください。
- サーバーレベルのアクセス許可: サーバーレベルのアクセス許可である `GRANT`、`REVOKE`、`DENY` はサポートされません。 一部のサーバーレベルのアクセス許可は、データベースレベルのアクセス許可に置き換えられるか、組み込みのサーバー ロールによって暗黙的に付与されます。 一部のサーバーレベルの DMV とカタログ ビューには、データベースレベルの同様のビューがあります。
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` および `RECONFIGURE`。 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) はサポートされません。
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server エージェント:SQL Server エージェントまたは MSDB データベースに依存する構文: アラート、演算子、中央管理サーバー。 PowerShell などのスクリプトを代わりに使用します。
- SQL Server 監査: 代わりに SQL Database [監査](auditing-overview.md)を使用してください。
- SQL Server トレース。
- トレース フラグ。
- T-SQL デバッグ。
- サーバー スコープ トリガーまたはログオン トリガー。
- `USE` ステートメント: データベース コンテキストを別のデータベースに変更するには、そのデータベースへの接続を新たに作成する必要があります。

## <a name="full-t-sql-reference"></a>完全 T-SQL リファレンス

T-SQL の文法、使用法、および使用例の詳細については、[T-SQL リファレンス (データベース エンジン)](/sql/t-sql/language-reference) に関するページを参照してください。

### <a name="about-the-applies-to-tags"></a>「適用先」タグについて

T-SQL リファレンスには、最近の全 SQL Server バージョンに関連した記事が含まれています。 記事のタイトルの下にアイコン バーがあり、MSSQL プラットフォームを一覧表示し、適用性を示しています。 たとえば、可用性グループは SQL Server 2012 で導入されました。 [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) の記事では、そのステートメントが **SQL Server (2012 以降)** に適用されることが示されています。 このステートメントは、SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure Synapse Analytics、Parallel Data Warehouse には適用されません。

場合によっては、記事にある一般的な項目を製品で使用できますが、製品間で若干の違いがあることがあります。 相違点は、必要に応じて記事の中で示されます。 たとえば `CREATE TRIGGER` の記事は、SQL Database に対応しています。 しかし、`ALL SERVER` オプションはサーバーレベルのトリガーを作成するためのもので、サーバーレベルのトリガーは SQL Database では使用できないことがわかります。 代わりにデータベース レベルのトリガーを使用します。

## <a name="next-steps"></a>次のステップ

SQL Database でサポートされる機能とサポートされない機能の一覧については、[Azure SQL Database の機能の比較](features-comparison.md)に関する記事を参照してください。

Azure SQL Database への移行前に SQL Server データベースの互換性の問題を検出してデータベースを移行するには、[Data Migration Assistant (DMA)](/sql/dma/dma-overview) を使用してください。