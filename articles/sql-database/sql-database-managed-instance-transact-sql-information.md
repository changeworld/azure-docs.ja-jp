---
title: Azure SQL Database Managed Instance の T-SQL の違い | Microsoft Docs
description: この記事では、Azure SQL Database Managed Instance と SQL Server の T-SQL の相違点について説明します。
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 95eca05d695e039f59b71caa4d730f4e1f84fc97
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337300"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance と SQL Server の T-SQL の相違点 

Azure SQL Database Managed Instance (プレビュー) は、オンプレミスの SQL Server データベース エンジンとの高い互換性を備えています。 SQL Server データベース エンジンのほとんどの機能が Managed Instance でサポートされています。 ただし、構文と動作に違いがあるため、この記事ではこれらの違いについて説明します。
 - [T-SQL の相違点とサポートされていない機能](#Differences)
 - [Managed Instance で動作が異なる機能](#Changes)
 - [一時的な制限事項と既知の問題](#Issues)

## <a name="Differences"></a>SQL Server の T-SQL との相違点 

このセクションでは、Managed Instance とオンプレミスの SQL Server データベース エンジンの T-SQL の構文および動作の主な相違点と、サポートされていない機能について簡単に説明します。

### <a name="always-on-availability"></a>Always-on 可用性

[高可用性](sql-database-high-availability.md)は Managed Instance に組み込まれており、ユーザーが制御することはできません。 次のステートメントはサポートされていません。
 - [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) ステートメントの [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 句

### <a name="auditing"></a>監査 
 
Managed Instance、Azure SQL Database、オンプレミスの SQL Server の SQL 監査の主な相違点は次のとおりです。
- Managed Instance では、SQL 監査はサーバー レベルで機能し、Azure BLOB ストレージ アカウントに `.xel` ファイルが保存されます。  
- Azure SQL Database では、SQL 監査はデータベース レベルで機能します。
- オンプレミス/仮想マシンの SQL Server では、SQL 監査はサーバー レベルで機能しますが、イベントはファイル システム/Windows イベント ログに保存されます。  
  
Managed Instance の XEvent 監査では、対象として Azure Blob Storage をサポートしています。 ファイル ログと Windows ログはサポートされていません。    
 
Azure Blob Storage の監査の `CREATE AUDIT` 構文の主な相違点は次のとおりです。
- 新しい `TO URL` 構文が用意されています。この構文を使用して、`.xel` ファイルを配置する Azure blob Storage コンテナーの URL を指定できます。 
- Managed Instance は Windows ファイル共有にアクセスできないため、`TO FILE` 構文はサポートされていません。 
 
詳細については、次を参照してください。  
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

Managed Instance には自動バックアップがあり、ユーザーは完全なデータベースの `COPY_ONLY` バックアップを作成できます。 差分、ログ、ファイル スナップショットの各バックアップはサポートされていません。  
- Managed Instance では、Azure BLOB ストレージ アカウントにのみデータベースをバックアップできます。 
 - `BACKUP TO URL` だけがサポートされています。 
 - `FILE`、`TAPE`、バックアップ デバイスはサポートされていません。  
- ほとんどの一般的な `WITH` オプションがサポートされています。 
 - `COPY_ONLY` は必須です。
 - `FILE_SNAPSHOT` はサポートされていません。  
 - テープ オプション: `REWIND`、`NOREWIND`、`UNLOAD`、`NOUNLOAD` はサポートされていません。 
 - ログ固有のオプション: `NORECOVERY`、`STANDBY`、`NO_TRUNCATE` はサポートされていません。 
 
制限事項:   
- Managed Instance では、最大 32 個のストライプを使用するバックアップにデータベースをバックアップできます。バックアップの圧縮を使用した場合、このバックアップで最大 4 TB のデータベースに十分対応できます。
- 最大バックアップ ストライプ サイズは 195 GB (最大 BLOB サイズ) です。 バックアップ コマンドのストライプ数を増やして、個々のストライプ サイズを小さくし、この制限を超えないようにします。 

> [!TIP]
> オンプレミスでこの制限を回避するには、`URL` ではなく `DISK` にバックアップし、バックアップ ファイルを BLOB にアップロードしてから復元します。 別の BLOB の種類が使用されるため、復元ではより大きなファイルがサポートされます。  

T-SQL を使用したバックアップについては、[BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) に関する記事をご覧ください。

### <a name="buffer-pool-extension"></a>バッファー プール拡張機能 
 
- [バッファー プール拡張機能](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)はサポートされていません。
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` はサポートされていません。 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql) に関する記事をご覧ください。 
 
### <a name="bulk-insert--openrowset"></a>一括挿入/openrowset

Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、Azure Blob Storage からファイルをインポートする必要があります。
- Azure Blob Storage からファイルをインポートするときは、`BULK INSERT` コマンドで `DATASOURCE` が必須になります。 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql) に関する記事をご覧ください。
- Azure Blob Storage からファイルの内容を読み取るときは、`OPENROWSET` 関数で `DATASOURCE` が必須になります。 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql) に関する記事をご覧ください。
 
### <a name="certificates"></a>証明書 

Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。 
- ファイルからの作成/ファイルへのバックアップ (`CREATE FROM`/`BACKUP TO`) は、証明書ではサポートされていません。
- ファイル/アセンブリ (`FILE`/`ASSEMBLY`) からの証明書の作成/バックアップ (`CREATE`/`BACKUP`) はサポートされていません。 秘密キー ファイルは使用できません。  
 
[CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) に関する記事、および [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql) に関する記事をご覧ください。  
  
> [!TIP]
> 対処法: 証明書/秘密キーのスクリプトを作成し、.sql ファイルとして保存してバイナリから作成します。 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。 
- `CREATE ASSEMBLY FROM BINARY` だけがサポートされています。 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql) に関する記事をご覧ください。  
- `CREATE ASSEMBLY FROM FILE` はサポートされていません。 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql) に関する記事をご覧ください。
- `ALTER ASSEMBLY` ではファイルを参照できません。 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql) に関する記事をご覧ください。
 
### <a name="compatibility-levels"></a>互換性レベル 
 
- サポートされている互換性レベルは、100、110、120、130、140 です。  
- 100 より低い互換性レベルはサポートされていません。 
- 新しいデータベースの既定の互換性レベルは 140 です。 互換性レベルが 100 以上のデータベースが復元された場合、互換性レベルは変更されません。

[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。
 
### <a name="credential"></a>資格情報 
 
Azure Key Vault と `SHARED ACCESS SIGNATURE` の ID だけがサポートされています。 Windows ユーザーはサポートされていません。
 
[CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) に関する記事、および [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql) に関する記事をご覧ください。 
 
### <a name="cryptographic-providers"></a>暗号化プロバイダー

Managed Instance はファイルにアクセスできないため、暗号化プロバイダーは作成できません。
- `CREATE CRYPTOGRAPHIC PROVIDER` はサポートされていません。 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql) に関する記事をご覧ください。
- `ALTER CRYPTOGRAPHIC PROVIDER` はサポートされていません。 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql) に関する記事をご覧ください。 

### <a name="collation"></a>Collation 
 
サーバーの照合順序は `SQL_Latin1_General_CP1_CI_AS` であり、変更することはできません。 「[照合順序](https://docs.microsoft.com/sql/t-sql/statements/collations)」をご覧ください。
 
### <a name="database-options"></a>データベース オプション 
 
- 複数のログ ファイルはサポートされていません。 
- インメモリ オブジェクトは、General Purpose サービス レベルではサポートされていません。  
- インスタンスあたり 280 ファイル (データベースあたり最大 280 ファイル) の制限があります。 データ ファイルとログ ファイルの両方がこの制限にカウントされます。  
- filestream データを含むファイル グループをデータベースに含めることはできません。  .bak に `FILESTREAM` データが含まれていると、復元は失敗します。  
- すべてのファイルが Azure Premium Storage に配置されます。 Azure Premium Storage ディスクの場合と同様に、ファイルあたりの IO とスループットは個々のファイルのサイズによって異なります。 [Azure Premium ディスクのパフォーマンス](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)に関するセクションをご覧ください。  
 
#### <a name="create-database-statement"></a>CREATE DATABASE ステートメント

`CREATE DATABASE` ステートメントの制限事項は次のとおりです。 
- ファイルおよびファイル グループは定義できません。  
- `CONTAINMENT` オプションはサポートされていません。  
- `WITH` オプションはサポートされていません。  
   > [!TIP]
   > 対処法として、`CREATE DATABASE` の後に `ALTER DATABASE` を使用して、ファイルの追加またはコンテインメントの設定を行うデータベース オプションを設定します。  

- `FOR ATTACH` オプションはサポートされていません。 
- `AS SNAPSHOT OF` オプションはサポートされていません。 

詳細については、[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql) に関する記事をご覧ください。

#### <a name="alter-database-statement"></a>ALTER DATABASE ステートメント

ファイルの一部のプロパティは設定または変更できません。
- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL ステートメントでは、ファイル パスは指定できません。 ファイルは Managed Instance によって自動的に配置されるため、スクリプトから `FILENAME` を削除します。  
- `ALTER DATABASE` ステートメントを使用してファイル名を変更することはできません。

次のオプションは既定で設定されており、変更することはできません。 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

次のオプションは変更できません。 
- `AUTO_CLOSE` 
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)` 
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)` 
- `DISABLE_BROKER` 
- `EMERGENCY` 
- `ENABLE_BROKER` 
- `FILESTREAM` 
- `HADR`   
- `NEW_BROKER` 
- `OFFLINE` 
- `PAGE_VERIFY` 
- `PARTNER` 
- `READ_ONLY` 
- `RECOVERY BULK_LOGGED` 
- `RECOVERY_SIMPLE` 
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER` 
- `SINGLE_USER` 
- `WITNESS`

名前の変更はサポートされていません。

詳細については、[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options) に関する記事をご覧ください。

### <a name="database-mirroring"></a>データベース ミラーリング

データベース ミラーリングはサポートされていません。
 - `ALTER DATABASE SET PARTNER` および `SET WITNESS` オプションはサポートされていません。
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING` はサポートされていません。

詳細については、[ALTER DATABASE の SET PARTNER と SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring)、および [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql) の説明をご覧ください。

### <a name="dbcc"></a>DBCC 
 
SQL Server で有効になっている、ドキュメントに記載されていない DBCC ステートメントは、Managed Instance ではサポートされていません。
- `Trace Flags` はサポートされていません。 [トレース フラグ](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)に関する記事をご覧ください。
- `DBCC TRACEOFF` はサポートされていません。 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) に関する記事をご覧ください。
- `DBCC TRACEON` はサポートされていません。 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) に関する記事をご覧ください。

### <a name="distributed-transactions"></a>分散トランザクション

MSDTC も[エラスティック トランザクション](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-transactions-overview)も現在、Managed Instance ではサポートされていません。

### <a name="extended-events"></a>拡張イベント 

XEvents の一部の Windows 固有のターゲットはサポートされていません。
- `etw_classic_sync target` はサポートされていません。 Azure Blob Storage に `.xel` ファイルを保存します。 「[etw_classic_sync ターゲット](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target)」をご覧ください。 
- `event_file target` はサポートされていません。 Azure Blob Storage に `.xel` ファイルを保存します。 「[event_file ターゲット](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target)」をご覧ください。

### <a name="external-libraries"></a>外部ライブラリ

In-Database R および In-Database Python 外部ライブラリはまだサポートされていません。 「[SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)」をご覧ください。

### <a name="filestream-and-filetable"></a>Filestream と Filetable

- filestream データはサポートされていません。 
- `FILESTREAM` データを含むファイル グループをデータベースに含めることはできません。
- `FILETABLE` はサポートされていません。
- テーブルに `FILESTREAM` 型を含めることはできません。
- 次の関数はサポートされていません。
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

詳細については、[FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) に関する記事、および [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server) に関する記事をご覧ください。

### <a name="full-text-semantic-search"></a>フルテキスト セマンティック検索

[セマンティック検索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)はサポートされていません。

### <a name="linked-servers"></a>リンク サーバー
 
Managed Instance のリンク サーバーがサポートするターゲットの数は限られています。 
- サポートされるターゲット: SQL Server と SQL Database
- サポートされていないターゲット: Analysis Services、他の RDBMS

[操作]

- クロス インスタンス書き込みトランザクションはサポートされていません。
- リンク サーバーの削除で `sp_dropserver` がサポートされています。 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql) に関する記事をご覧ください。
- SQL Server インスタンス (マネージ、オンプレミス、または仮想マシン内) でのみ、`OPENROWSET` 関数を使用してクエリを実行できます。 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql) に関する記事をご覧ください。
- SQL Server インスタンス (マネージ、オンプレミス、または仮想マシン内) でのみ、`OPENDATASOURCE` 関数を使用してクエリを実行できます。 プロバイダーとしてサポートされる値は、`SQLNCLI`、`SQLNCLI11`、`SQLOLEDB` だけです。 たとえば、「 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`」のように入力します。 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql) に関する記事をご覧ください。
 
### <a name="logins--users"></a>ログイン/ユーザー 

- `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY`、`FROM SID` を使用して作成された SQL ログインはサポートされています。 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql) に関する記事をご覧ください。
- `CREATE LOGIN ... FROM WINDOWS` 構文を使用して作成された Windows ログインはサポートされていません。
- インスタンスを作成した Azure Active Directory (Azure AD) ユーザーは、[制限のない管理特権](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts)を持ちます。
- 管理者以外の、データベース レベルの Azure Active Directory (Azure AD) ユーザーは、`CREATE USER ... FROM EXTERNAL PROVIDER` 構文を使用して作成できます。 [CREATE USER ...FROM EXTERNAL PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users) に関するセクションをご覧ください。
 
### <a name="polybase"></a>PolyBase

HDFS または Azure Blob Storage 内のファイルを参照する外部テーブルはサポートされていません。 PolyBase については、[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) に関する記事をご覧ください。

### <a name="replication"></a>レプリケーション 
 
レプリケーションはまだサポートされていません。 レプリケーションについては、「[SQL Server のレプリケーション](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication)」をご覧ください。
 
### <a name="restore-statement"></a>RESTORE ステートメント 
 
- サポートされている構文  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- サポートされていない構文 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- ソース  
 - サポートされているオプションは、`FROM URL` (Azure Blob Storage) だけです。
 - `FROM DISK`/`TAPE`/バックアップ デバイスはサポートされていません。
 - バックアップ セットはサポートされていません。 
- `WITH` オプションはサポートされていません (`DIFFERENTIAL`、`STATS` などは使用不可)。     
- `ASYNC RESTORE` - クライアント接続が切断されても、復元は続行されます。 接続が破棄された場合は、`sys.dm_operation_status` ビューで復元操作の状態 (およびデータベースの CREATE 操作と DROP 操作) を確認できます。 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) に関する記事をご覧ください。  
 
次のデータベース オプションを設定/オーバーライドします。これらを後で変更することはできません。  
- `NEW_BROKER` (.bak ファイルでブローカーが有効になっていない場合)  
- `ENABLE_BROKER` (.bak ファイルでブローカーが有効になっていない場合)  
- `AUTO_CLOSE=OFF` (.bak ファイル内のデータベースに `AUTO_CLOSE=ON` が設定されている場合)  
- `RECOVERY FULL` (.bak ファイル内のデータベースが `SIMPLE` または `BULK_LOGGED` 回復モードの場合)
- メモリ最適化ファイル グループが追加され、XTP という名前が付けられます (ソース .bak ファイルに含まれていなかった場合)。  
- 既存のメモリ最適化ファイル グループの名前が XTP に変更されます。  
- `SINGLE_USER` および `RESTRICTED_USER` オプションは、`MULTI_USER`  に変換されます。  
制限事項:   
- 複数のバックアップ セットを含む `.BAK` ファイルは復元できません。 
- 複数のログ ファイルを含む `.BAK` ファイルは復元できません。 
- .bak に `FILESTREAM` データが含まれていると、復元は失敗します。
- 現時点では、アクティブなインメモリ オブジェクトが存在するデータベースが含まれたバックアップは復元できません。  
- 現時点では、ある時点でインメモリ オブジェクトが存在していたデータベースが含まれたバックアップは復元できません。   
- 現時点では、読み取り専用モードのデータベースが含まれたバックアップは復元できません。 この制限は間もなく削除されます。   
 
RESTORE ステートメントについては、[RESTORE ステートメント](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)に関する記事をご覧ください。

### <a name="service-broker"></a>Service Broker 
 
- クロス インスタンス Service Broker はサポートされていません。 
 - `sys.routes` - 前提条件: sys.routes からアドレスを選択します。 アドレスは、各ルートで LOCAL である必要があります。 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql) に関する記事をご覧ください。
 - `CREATE ROUTE` - `LOCAL` 以外の `ADDRESS` を使用して、`CREATE ROUTE` を実行することはできません。 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql) に関する記事をご覧ください。
 - `ALTER ROUTE` - `LOCAL` 以外の `ADDRESS` を使用して、`ALTER ROUTE` を実行することはできません。 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql) に関する記事をご覧ください。  
 
### <a name="service-key-and-service-master-key"></a>サービス キーとサービス マスター キー 
 
- [マスター キーのバックアップ](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。 
- [マスター キーの復元](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。 
- [サービス マスター キーのバックアップ](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。 
- [サービス マスター キーの復元](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。 
 
### <a name="stored-procedures-functions-triggers"></a>ストアド プロシージャ、関数、トリガー 
 
- `NATIVE_COMPILATION` は現在サポートされていません。 
- [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) の次のオプションはサポートされていません。 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- `sp_execute_external_scripts` はサポートされていません。 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples) に関するセクションをご覧ください。
- `xp_cmdshell` はサポートされていません。 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql) に関する記事をご覧ください。
- `sp_addextendedproc` や `sp_dropextendedproc` など、`Extended stored procedures` はサポートされていません。 [拡張ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)に関する記事をご覧ください。
- `sp_attach_db`、`sp_attach_single_file_db`、`sp_detach_db` はサポートされていません。 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)、[sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql) に関する各記事をご覧ください。
- `sp_renamedb` はサポートされていません。 [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql) に関する記事をご覧ください。

### <a name="sql-server-agent"></a>SQL Server エージェント 
 
- SQL エージェントの設定は読み取り専用です。 `sp_set_agent_properties` プロシージャは、Managed Instance ではサポートされていません。  
- ジョブ - 現在サポートされているのは、T-SQL ジョブ ステップだけです (パブリック プレビュー期間に、さらに多くのステップが追加される予定です)。
 - SSIS はまだサポートされていません。 
 - レプリケーションはまだサポートされていません。  
  - トランザクション ログ リーダーはまだサポートされていません。  
  - スナップショットはまだサポートされていません。  
  - ディストリビューターはまだサポートされていません。  
  - マージはサポートされていません。  
  - キュー リーダーはサポートされていません。  
 - コマンド シェルはまだサポートされていません。 
  - Managed Instance は、外部リソース (例: robocopy を介したネットワーク共有) にはアクセスできません。  
 - PowerShell はまだサポートされていません。
 - Analysis Services はサポートされていません。  
- 通知は部分的にサポートされています。
 - 電子メール通知がサポートされていますが、データベース メール プロファイルを構成する必要があります。 データベース メール プロファイルは 1 つしか存在できず、パブリック プレビューでは `AzureManagedInstance_dbmail_profile` という名前である必要があります (一時的な制限)。  
 - ポケットベルはサポートされていません。  
 - NetSend はサポートされていません。 
 - アラートはまだサポートされていません。
 - プロキシはサポートされていません。  
- Eventlog はサポートされていません。 
 
次の機能は現在サポートされていませんが、今後有効になります。  
- プロキシ
- アイドル状態の CPU でのジョブのスケジューリング 
- エージェントの有効化/無効化
- アラート

SQL Server エージェントについては、「[SQL Server エージェント](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)」をご覧ください。
 
### <a name="tables"></a>テーブル 

次の要素はサポートされていません。 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

テーブルの作成と変更については、[CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) に関する記事および [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) に関する記事をご覧ください。
 
## <a name="Changes"></a>動作の変更 
 
次の変数、関数、ビューは異なる結果を返します。  
- `SERVERPROPERTY('EngineEdition')` は 8 を返します。 このプロパティは、マネージド インスタンスを一意に識別します。 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql) に関する記事をご覧ください。
- `SERVERPROPERTY('InstanceName')` は NULL を返します。これは SQL Server に関して存在するインスタンスの概念が、マネージド インスタンスには該当しないためです。 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql) に関する説明をご覧ください。
- `@@SERVERNAME` は、"接続可能な" 完全 DNS 名を返します (例: my-managed-instance.wcus17662feb9ce98.database.windows.net)。 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql) に関する記事をご覧ください。  
- `SYS.SERVERS` - "name" プロパティと "data_source" プロパティを表す `myinstance.domain.database.windows.net` のような、"接続可能な" 完全 DNS 名を返します。 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql) に関する記事をご覧ください。 
- `@@SERVICENAME` は NULL を返します。これは SQL Server に関して存在するサービスの概念が、マネージド インスタンスには該当しないためです。 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql) に関する記事をご覧ください。   
- `SUSER_ID` がサポートされています。 AAD ログインが sys.syslogins に含まれていない場合は、NULL を返します。 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql) に関する記事をご覧ください。  
- `SUSER_SID` はサポートされていません。 間違ったデータを返します (一時的な既知の問題)。 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql) に関する記事をご覧ください。 
- `GETDATE()` と他の組み込みの日付/時刻関数は、常に UTC タイム ゾーンの時刻を返します。 [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) に関する記事をご覧ください。

## <a name="Issues"></a>既知の問題と制限事項

### <a name="tempdb-size"></a>TEMPDB のサイズ

`tempdb` は、それぞれ最大サイズが 14 GB の 12 個のファイルに分割されます。 ファイルあたりのこの最大サイズは変更できず、`tempdb` に新しいファイルを追加することはできません。 この制限は間もなく削除されます。 一部のクエリで `tempdb` のサイズが 168 GB を超える必要がある場合は、エラーが返されます。

### <a name="exceeding-storage-space-with-small-database-files"></a>小さなデータベース ファイルによる記憶域の超過

それぞれのマネージド インスタンスが、Azure Premium ディスク領域用に予約された最大 35 TB の記憶域を持ち、各データベース ファイルは別個の物理ディスク上に配置されます。 ディスク サイズとして、128 GB、256 GB、512 GB、1 TB、4 TB のいずれかを指定できます。 ディスク上の未使用領域については請求されませんが、Azure Premium ディスクのサイズ合計が 35 TB を超えることはできません。 場合によっては、内部の断片化のために、合計で 8 TB を必要としないマネージド インスタンスが、記憶域のサイズに関する 35 TB の Azure での制限を超える場合があります。 

たとえば、4 TB のディスクを使用するマネージド インスタンスに、サイズが 1.2 TB のファイルが 1 つあり、サイズが 128 GB の 248 個のディスクに、それぞれが 1 GB の 248 個のファイルが配置される可能性があります。 この例では、ディスク記憶域の合計サイズは、1 x 4 TB + 248 x 128 GB = 35 TB となります。 ただし、データベースのために予約される合計インスタンス サイズは、1 x 1.2 TB + 248 x 1 GB = 1.4 TB となります。 これは、特定の状況下では、非常に特殊なファイルの配分のために、マネージド インスタンスが、想定していなかった Azure Premium ディスクの記憶域の上限に到達する可能性があることを示しています。 

既存のデータベースではエラーが発生せず、新しいファイルが追加されなければ問題なくデータベースの拡大が可能です。しかし、すべてのデータベースの合計サイズがインスタンスのサイズ制限に到達しない場合でも、新しいディスク ドライブ用に十分な領域がないため、新しいデータベースの作成や復元を行うことができません。 その場合に返されるエラーは明確ではありません。

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>データベースの復元時の SAS キーの構成が正しくない

`CREDENTIAL` の Shared Access Signature が正しくない場合、.bak ファイルを読み取る `RESTORE DATABASE` が、.bak ファイルの読み取りの再試行を繰り返し、長時間経過した後にエラーを返すことがあります。 データベースを復元する前に RESTORE HEADERONLY を実行して、SAS キーが正しいことを確認します。
Azure Portal を使用して生成された SAS キーから、先頭の `?` を必ず削除してください。

### <a name="tooling"></a>ツール

SQL Server Management Studio と SQL Server Data Tools には、Managed Instance にアクセスする際に問題がある可能性があります。 一般公開の前に、ツールに関するすべての問題に対処する予定です。

### <a name="incorrect-database-names"></a>データベース名が正しくない

Managed Instance では、復元中または一部のエラー メッセージにデータベース名の代わりに GUID 値が表示されることがあります。 一般公開の前に、これらの問題を解決する予定です。

### <a name="database-mail-profile"></a>データベース メール プロファイル
データベース メール プロファイルは 1 つしか存在できず、`AzureManagedInstance_dbmail_profile` という名前である必要があります。 これは間もなく削除される一時的な制限です。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスの詳細については、[マネージド インスタンスの概要](sql-database-managed-instance.md)に関する記事をご覧ください。
- 機能比較一覧については、[SQL 共通機能](sql-database-features.md)に関する記事をご覧ください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](sql-database-managed-instance-create-tutorial-portal.md)に関するページを参照してください。
