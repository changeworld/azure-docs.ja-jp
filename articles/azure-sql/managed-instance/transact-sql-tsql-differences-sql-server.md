---
title: SQL Server と Azure SQL Managed Instance での T-SQL の相違点
description: この記事では、Azure SQL Managed Instance と SQL Server の Transact-SQL (T-SQL) の相違点について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: reference
author: danimir
ms.author: danil
ms.reviewer: mathoma, bonova, danil
ms.date: 10/21/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 0bc0bf771d8a50acfe09ab4b4abdded4dc078c49
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707663"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server と Azure SQL Managed Instance での T-SQL の相違点
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、Azure SQL Managed Instance と SQL Server の構文および動作の相違点について簡単に説明します。 


SQL Managed Instance は、SQL Server データベース エンジンとの高い互換性を備えており、ほとんどの機能は SQL Managed Instance でサポートされています。

![SQL Server からの移行が簡単](./media/transact-sql-tsql-differences-sql-server/migration.png)

SQL Managed Instance には、SQL Server と比べて PaaS の制限と動作の違いがいくつかあります。 それらの相違点は、次のカテゴリに分けることができます。<bpt id="p1"><a name="Differences"></bpt><ept id="p1"></a></ept>

- <bpt id="p1">[</bpt>可用性<ept id="p1">](#availability)</ept>には、<bpt id="p2">[</bpt>Always On 可用性グループ<ept id="p2">](#always-on-availability-groups)</ept>と<bpt id="p3">[</bpt>バックアップ<ept id="p3">](#backup)</ept>の相違点が含まれます。
- <bpt id="p1">[</bpt>セキュリティ<ept id="p1">](#security)</ept>には、<bpt id="p2">[</bpt>監査<ept id="p2">](#auditing)</ept>、<bpt id="p3">[</bpt>証明書<ept id="p3">](#certificates)</ept>、<bpt id="p4">[</bpt>資格情報<ept id="p4">](#credential)</ept>、<bpt id="p5">[</bpt>暗号化プロバイダー<ept id="p5">](#cryptographic-providers)</ept>、<bpt id="p6">[</bpt>ログイン/ユーザー<ept id="p6">](#logins-and-users)</ept>、<bpt id="p7">[</bpt>サービス キーとサービス マスター キー<ept id="p7">](#service-key-and-service-master-key)</ept>の相違点が含まれます。
- <bpt id="p1">[</bpt>構成<ept id="p1">](#configuration)</ept>には、<bpt id="p2">[</bpt>バッファー プール拡張機能<ept id="p2">](#buffer-pool-extension)</ept>、<bpt id="p3">[</bpt>照合順序<ept id="p3">](#collation)</ept>、<bpt id="p4">[</bpt>互換性レベル<ept id="p4">](#compatibility-levels)</ept>、<bpt id="p5">[</bpt>データベース ミラーリング<ept id="p5">](#database-mirroring)</ept>、<bpt id="p6">[</bpt>データベース オプション<ept id="p6">](#database-options)</ept>、<bpt id="p7">[</bpt>SQL Server エージェント<ept id="p7">](#sql-server-agent)</ept>、<bpt id="p8">[</bpt>テーブル オプション<ept id="p8">](#tables)</ept>の相違点が含まれます。
- <bpt id="p1">[</bpt>機能<ept id="p1">](#functionalities)</ept>には、<bpt id="p2">[</bpt>BULK INSERT/OPENROWSET<ept id="p2">](#bulk-insert--openrowset)</ept>、<bpt id="p3">[</bpt>CLR<ept id="p3">](#clr)</ept>、<bpt id="p4">[</bpt>DBCC<ept id="p4">](#dbcc)</ept>、<bpt id="p5">[</bpt>分散トランザクション<ept id="p5">](#distributed-transactions)</ept>、<bpt id="p6">[</bpt>拡張イベント<ept id="p6">](#extended-events)</ept>、<bpt id="p7">[</bpt>外部ライブラリ<ept id="p7">](#external-libraries)</ept>、<bpt id="p8">[</bpt>filestream と FileTable<ept id="p8">](#filestream-and-filetable)</ept>、<bpt id="p9">[</bpt>フルテキスト セマンティック検索<ept id="p9">](#full-text-semantic-search)</ept>、<bpt id="p10">[</bpt>リンク サーバー<ept id="p10">](#linked-servers)</ept>、<bpt id="p11">[</bpt>PolyBase<ept id="p11">](#polybase)</ept>、<bpt id="p12">[</bpt>レプリケーション<ept id="p12">](#replication)</ept>、<bpt id="p13">[</bpt>RESTORE<ept id="p13">](#restore-statement)</ept>、<bpt id="p14">[</bpt>Service Broker<ept id="p14">](#service-broker)</ept>、<bpt id="p15">[</bpt>ストアド プロシージャ、関数、およびトリガー<ept id="p15">](#stored-procedures-functions-and-triggers)</ept>が含まれます。
- VNet やサブネット構成などの<bpt id="p1">[</bpt>環境設定<ept id="p1">](#Environment)</ept>。

これらの機能の大半はアーキテクチャ上の制約であり、サービスの機能を表します。

SQL Managed Instance で見つかり、将来解決される予定の一時的な既知の問題については、[新機能](doc-changes-updates-release-notes-whats-new.md)に関するページで説明しています。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a><bpt id="p1"><a name="always-on-availability-groups"></bpt><ept id="p1"></a></ept>Always On 可用性グループ

<bpt id="p1">[</bpt>高可用性<ept id="p1">](../database/high-availability-sla.md)</ept>は SQL Managed Instance に組み込まれており、ユーザーが制御することはできません。 次のステートメントはサポートされていません。

- <bpt id="p1">[</bpt>CREATE ENDPOINT … FOR DATABASE_MIRRORING<ept id="p1">](/sql/t-sql/statements/create-endpoint-transact-sql)</ept>
- <bpt id="p1">[</bpt>CREATE AVAILABILITY GROUP<ept id="p1">](/sql/t-sql/statements/create-availability-group-transact-sql)</ept>
- <bpt id="p1">[</bpt>ALTER AVAILABILITY GROUP<ept id="p1">](/sql/t-sql/statements/alter-availability-group-transact-sql)</ept>
- <bpt id="p1">[</bpt>DROP AVAILABILITY GROUP<ept id="p1">](/sql/t-sql/statements/drop-availability-group-transact-sql)</ept>
- <bpt id="p2">[</bpt>ALTER DATABASE<ept id="p2">](/sql/t-sql/statements/alter-database-transact-sql)</ept> ステートメントの <bpt id="p1">[</bpt>SET HADR<ept id="p1">](/sql/t-sql/statements/alter-database-transact-sql-set-hadr)</ept> 句

### <a name="backup"></a>バックアップ

SQL Managed Instance には自動バックアップがあるので、ユーザーは完全なデータベースの <ph id="ph1">`COPY_ONLY`</ph> バックアップを作成できます。 差分、ログ、ファイル スナップショットの各バックアップはサポートされていません。

- SQL Managed Instance では、Azure BLOB ストレージ アカウントにのみインスタンス データベースをバックアップできます。
  - サポートされるのは <ph id="ph1">`BACKUP TO URL`</ph> のみです。
  - <ph id="ph1">`FILE`</ph>、<ph id="ph2">`TAPE`</ph>、およびバックアップ デバイスはサポートされていません。
- ほとんどの一般的な <ph id="ph1">`WITH`</ph> オプションがサポートされています。
  - `COPY_ONLY` は必須です。
  - <ph id="ph1">`FILE_SNAPSHOT`</ph> はサポートされていません。
  - テープ オプション: <ph id="ph1">`REWIND`</ph>、<ph id="ph2">`NOREWIND`</ph>、<ph id="ph3">`UNLOAD`</ph>、<ph id="ph4">`NOUNLOAD`</ph> はサポートされていません。
  - ログ固有のオプション: <ph id="ph1">`NORECOVERY`</ph>、<ph id="ph2">`STANDBY`</ph>、<ph id="ph3">`NO_TRUNCATE`</ph> はサポートされていません。

制限事項: 

- SQL Managed Instance では、最大 32 個のストライプを使用するバックアップにインスタンス データベースをバックアップできます。バックアップの圧縮を使用した場合、このバックアップで最大 4 TB のデータベースに十分対応できます。
- サービス管理 Transparent Data Encryption (TDE) を使用して暗号化されたデータベースでは、<ph id="ph1">`BACKUP DATABASE ... WITH COPY_ONLY`</ph> は実行できません。 サービス管理 TDE では、バックアップを内部の TDE のキーで暗号化するように強制します。 キーはエクスポートできないので、バックアップを復元することはできません。 自動バックアップとポイントインタイム リストアを使用するか、代わりに<bpt id="p1">[</bpt>顧客管理 (BYOK) TDE<ept id="p1">](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key)</ept> を使用します。 また、データベースで暗号化を無効にすることができます。
- Managed Instance 上で行われたネイティブ バックアップを SQL Server に復元することはできません。 これは、SQL Server のどのバージョンと比べても、Managed Instance の内部データベース バージョンが高いためです。
- Azure ストレージとの間でデータベースのバックアップまたは復元を行うには、Azure Storage リソースへの制限付きアクセス権が付与される Shared Access Signature (SAS) URI を作成する必要があります。[詳細についてはこちらを参照してください](restore-sample-database-quickstart.md#restore-from-a-backup-file-using-t-sql)。 これらのシナリオでのアクセス キーの使用はサポートされていません。
- SQL Managed Instance で <ph id="ph1">`BACKUP`</ph> コマンドを使用した場合の最大バックアップ ストライプ サイズは、最大 BLOB サイズである 195 GB です。 バックアップ コマンドでストライプ サイズを増やして、個々のストライプ サイズを減らし、この制限内に収まるようにします。

    > [!TIP]
    > 次の操作を実行すると、オンプレミス環境の SQL Server または仮想マシンからデータベースをバックアップするときに、この制限を回避できます。
    >
    > - <ph id="ph2">`URL`</ph> にバックアップする代わりに <ph id="ph1">`DISK`</ph> にバックアップします。
    > - バックアップ ファイルを BLOB ストレージにアップロードします。
    > - SQL Managed Instance に復元します。
    >
    > SQL Managed Instance で <ph id="ph1">`Restore`</ph> コマンドを実行すると、アップロードされたバックアップ ファイルに対して異なる BLOB タイプが使用されるため、より大きな BLOB サイズがサポートされます。

T-SQL を使用したバックアップについては、<bpt id="p1">[</bpt>BACKUP<ept id="p1">](/sql/t-sql/statements/backup-transact-sql)</ept> に関する記事をご覧ください。

## <a name="security"></a>セキュリティ

### <a name="auditing"></a>監査

Microsoft Azure SQL と SQL Server での監査の主な違いは次のとおりです。

- SQL Managed Instance では、監査はサーバー レベルで機能します。 <ph id="ph1">`.xel`</ph> ログ ファイルは、Azure BLOB ストレージに格納されます。
- Azure SQL Database では、監査はデータベース レベルで機能します。 <ph id="ph1">`.xel`</ph> ログ ファイルは、Azure BLOB ストレージに格納されます。
- オンプレミスまたは仮想マシンの SQL Server では、監査はサーバー レベルで機能します。 イベントは、ファイル システムまたは Windows イベント ログに格納されます。
 
SQL Managed Instance の XEvent 監査では、Azure Blob Storage のターゲットがサポートされます。 ファイル ログと Windows ログはサポートされていません。

Azure Blob Storage を監査するための `CREATE AUDIT` 構文の主な相違点は次のとおりです。

- <ph id="ph2">`.xel`</ph> ファイルが配置されている Azure BLOB ストレージ コンテナーの URL の指定に使用できる新しい <ph id="ph1">`TO URL`</ph> 構文が用意されています。
- SQL Managed Instance は Windows ファイル共有にアクセスできないため、<ph id="ph1">`TO FILE`</ph> 構文はサポートされていません。

詳細については、次を参照してください。 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [監査](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>証明書

SQL Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。

- <ph id="ph1">`CREATE FROM`</ph><ph id="ph2">/</ph><ph id="ph3">`BACKUP TO`</ph> ファイルは、証明書ではサポートされていません。
- <ph id="ph4">`FILE`</ph><ph id="ph5">/</ph><ph id="ph6">`ASSEMBLY`</ph> からの <ph id="ph1">`CREATE`</ph><ph id="ph2">/</ph><ph id="ph3">`BACKUP`</ph> 証明書はサポートされていません。 秘密キー ファイルは使用できません。 

<bpt id="p1">[</bpt>CREATE CERTIFICATE<ept id="p1">](/sql/t-sql/statements/create-certificate-transact-sql)</ept> に関する記事、および <bpt id="p2">[</bpt>BACKUP CERTIFICATE<ept id="p2">](/sql/t-sql/statements/backup-certificate-transact-sql)</ept> に関する記事をご覧ください。 
 
<bpt id="p1">**</bpt>回避策<ept id="p1">**</ept>:証明書のバックアップを作成してバックアップを復元するのでなく、<bpt id="p2">[</bpt>証明書のバイナリ コンテンツと秘密キーを取得し、それを .sql ファイルとして保存し、バイナリから作成します<ept id="p2">](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)</ept>。

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>資格情報

Azure Key Vault と <ph id="ph1">`SHARED ACCESS SIGNATURE`</ph> の ID だけがサポートされています。 Windows ユーザーはサポートされていません。

<bpt id="p1">[</bpt>CREATE CREDENTIAL<ept id="p1">](/sql/t-sql/statements/create-credential-transact-sql)</ept> に関する記事、および <bpt id="p2">[</bpt>ALTER CREDENTIAL<ept id="p2">](/sql/t-sql/statements/alter-credential-transact-sql)</ept> に関する記事をご覧ください。

### <a name="cryptographic-providers"></a>暗号化プロバイダー

SQL Managed Instance はファイルにアクセスできないため、暗号化プロバイダーは作成できません。

- <ph id="ph1">`CREATE CRYPTOGRAPHIC PROVIDER`</ph> はサポートされていません。 <bpt id="p1">[</bpt>CREATE CRYPTOGRAPHIC PROVIDER<ept id="p1">](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`ALTER CRYPTOGRAPHIC PROVIDER`</ph> はサポートされていません。 <bpt id="p1">[</bpt>ALTER CRYPTOGRAPHIC PROVIDER<ept id="p1">](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)</ept> に関する記事をご覧ください。

### <a name="logins-and-users"></a>ログインとユーザー

- <ph id="ph1">`FROM CERTIFICATE`</ph>、<ph id="ph2">`FROM ASYMMETRIC KEY`</ph>、<ph id="ph3">`FROM SID`</ph> を使用して作成された SQL ログインはサポートされています。 <bpt id="p1">[</bpt>CREATE LOGIN<ept id="p1">](/sql/t-sql/statements/create-login-transact-sql)</ept> に関する記事をご覧ください。
- <bpt id="p1">[</bpt>CREATE LOGIN<ept id="p1">](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)</ept> 構文または <bpt id="p2">[</bpt>CREATE USER FROM Login [Azure AD Login]<ept id="p2">](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true)</ept> 構文を使用して作成された Azure Active Directory (Azure AD) サーバー プリンシパル (ログイン) がサポートされます。 これらのログインは、サーバー レベルで作成されます。

    SQL Managed Instance は、<ph id="ph1">`CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`</ph> 構文で Azure AD データベース プリンシパルをサポートします。 この機能は、Azure AD 包含データベース ユーザーとも呼ばれます。

- <ph id="ph1">`CREATE LOGIN ... FROM WINDOWS`</ph> 構文を使用して作成された Windows ログインはサポートされていません。 Azure Active Directory のログインとユーザーを使用します。
- インスタンスの Azure AD 管理者は、[無制限の管理特権](../database/logins-create-manage.md)を持ちます。
- 管理者以外の、データベース レベルの Azure AD ユーザーは、<ph id="ph1">`CREATE USER ... FROM EXTERNAL PROVIDER`</ph> 構文を使用して作成できます。 <bpt id="p1">[</bpt>CREATE USER ...FROM EXTERNAL PROVIDER<ept id="p1">](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)</ept> を参照してください。
- Azure AD サーバー プリンシパル (ログイン) は、1 つの SQL Managed Instance 内のみで SQL 機能をサポートします。 同じ Azure AD テナント内か、または異なるテナント内かに関係なく、Azure AD ユーザーの場合、クロス インスタンス対話を必要とする機能はサポートされていません。 そのような機能の例として次のものがあります。

  - SQL トランザクション レプリケーション。
  - リンク サーバー。

- Azure AD グループにマップされている Azure AD ログインをデータベース所有者として設定することはサポートされていません。 Azure AD グループのメンバーは、データベースにログインが作成されていない場合でも、データベース所有者になることができます。
- <bpt id="p1">[</bpt>EXECUTE AS<ept id="p1">](/sql/t-sql/statements/execute-as-transact-sql)</ept> 句など、他の Azure AD プリンシパルを使用することによる Azure AD サーバー レベル プリンシパルの権限の借用はサポートされています。 EXECUTE AS の制限事項は次のとおりです。

  - 名前がログイン名と異なる場合、EXECUTE AS USER は Azure AD ユーザーに対してサポートされません。 たとえば、CREATE USER [myAadUser] FROM LOGIN [<ph id="ph1">john@contoso.com</ph>] 構文を使用してユーザーが作成されて、EXEC AS USER = <bpt id="p1">_</bpt>myAadUser<ept id="p1">_</ept> を使用して権限借用が試行された場合などです。 Azure AD サーバー プリンシパル (ログイン) から <bpt id="p1">**</bpt>USER<ept id="p1">**</ept> を作成するときは、user_name を <bpt id="p2">**</bpt>LOGIN<ept id="p2">**</ept> と同じ login_name として指定します。
  - Azure AD プリンシパルを対象とした次の操作を実行できるのは、<ph id="ph1">`sysadmin`</ph> ロールに属している SQL Server レベル プリンシパル (ログイン) のみです。

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - EXECUTE AS ステートメントを使用してユーザーを偽装するには、そのユーザーが Azure AD サーバー プリンシパル (ログイン) に直接マップされる必要があります。 Azure AD サーバー プリンシパルにマップされた Azure AD グループのメンバーであるユーザーは、指定されたユーザー名に対する偽装権限を呼び出し元が持っている場合でも、EXECUTE AS ステートメントを使用して有効に偽装できません。

- bacpac ファイルを使用したデータベースのエクスポート/インポートは、<bpt id="p1">[</bpt>SSMS V 18.4 以降<ept id="p1">](/sql/ssms/download-sql-server-management-studio-ssms)</ept>または <bpt id="p2">[</bpt>SQLPackage.exe<ept id="p2">](/sql/tools/sqlpackage-download)</ept> のいずれかを使用している SQL Managed Instance 内の Azure AD ユーザーに対してサポートされます。
  - データベース bacpac ファイルを使用すると、次の構成がサポートされます。 
    - 同じ Azure AD ドメイン内の異なるマネージド インスタンス間でのデータベースのエクスポート/インポート。
    - SQL Managed Instance からデータベースをエクスポートし、同じ Azure AD ドメイン内の SQL Database にインポートする。 
    - SQL Database からデータベースをエクスポートし、同じ Azure AD ドメイン内の SQL Managed Instance にインポートする。
    - SQL Managed Instance からデータベースをエクスポートし、SQL Server (バージョン 2012 以降) にインポートする。
      - この構成では、すべての Azure AD ユーザーが、ログインなしの SQL Server データベース プリンシパル (ユーザー) として作成されます。 ユーザーの種類は <ph id="ph1">`SQL`</ph> として表示され、sys.database_principals では <ph id="ph2">`SQL_USER`</ph> として表示されます。 これらのアクセス許可とロールは SQL Server データベースのメタデータに残り、権限の借用に使用できます。 ただし、これらの資格情報を使用して SQL Server にアクセスしてログインすることはできません。

- SQL Managed Instance のプロビジョニング プロセスによって作成されるサーバーレベル プリンシパル ログイン、<ph id="ph1">`securityadmin`</ph> や <ph id="ph2">`sysadmin`</ph> などのサーバー ロールのメンバー、あるいはサーバー レベルの ALTER ANY LOGIN アクセス許可を持つその他のログインのみが、SQL Managed Instance のマスター データベースに Azure AD サーバー プリンシパル (ログイン) を作成できます。
- ログインが SQL プリンシパルの場合、作成コマンドを使用して Azure AD アカウントのログインを作成できるのは、<ph id="ph1">`sysadmin`</ph> ロールに属しているログインのみです。
- Azure AD ログインは、Azure SQL Managed Instance に使用されるものと同じディレクトリ内の Azure AD のメンバーでなければなりません。
- Azure AD サーバー プリンシパル (ログイン) は、SQL Server Management Studio 18.0 プレビュー 5 から、オブジェクト エクスプローラーに表示されます。
- *sysadmin* アクセス レベルのサーバー プリンシパルは、インスタンスで有効にされた後、Azure AD 管理者アカウントに対して自動的に作成されます。
- 認証時、認証するプリンシパルを解決するために次の順序が適用されます。

    1. Azure AD アカウントが、Azure AD サーバー プリンシパル (ログイン) に直接マップされているものとして存在する (sys.server_principals に type "E" と指定されている) 場合、アクセスを許可し、Azure AD サーバー プリンシパル (ログイン) のアクセス権を適用する。
    1. Azure AD アカウントが、Azure AD サーバー プリンシパル (ログイン) にマップされている Azure AD グループのメンバーである (sys.server_principals に type "X" と指定されている) 場合、アクセスを許可し、Azure AD グループ ログインのアクセス権を適用する。
    1. Azure AD アカウントが、データベース内の Azure AD ユーザーに直接マップされたものとして存在する (sys.database_principals に type "E" と指定されている) 場合、アクセスを許可し、Azure AD データベース ユーザーのアクセス許可を適用する。
    1. Azure AD アカウントが、データベース内の Azure AD ユーザーにマップされた Azure AD グループのメンバーである (sys.database_principals に type "X" と指定されている) 場合、アクセスを許可し、Azure AD グループ ユーザーのアクセス許可を適用する。

### <a name="service-key-and-service-master-key"></a>サービス キーとサービス マスター キー

- <bpt id="p1">[</bpt>マスター キーのバックアップ<ept id="p1">](/sql/t-sql/statements/backup-master-key-transact-sql)</ept>はサポートされていません (SQL Database サービスによって管理されます)。
- <bpt id="p1">[</bpt>マスター キーの復元<ept id="p1">](/sql/t-sql/statements/restore-master-key-transact-sql)</ept>はサポートされていません (SQL Database サービスによって管理されます)。
- <bpt id="p1">[</bpt>サービス マスター キーのバックアップ<ept id="p1">](/sql/t-sql/statements/backup-service-master-key-transact-sql)</ept>はサポートされていません (SQL Database サービスによって管理されます)。
- <bpt id="p1">[</bpt>サービス マスター キーの復元<ept id="p1">](/sql/t-sql/statements/restore-service-master-key-transact-sql)</ept>はサポートされていません (SQL Database サービスによって管理されます)。

## <a name="configuration"></a>構成

### <a name="buffer-pool-extension"></a>バッファー プール拡張

- <bpt id="p1">[</bpt>バッファー プール拡張機能<ept id="p1">](/sql/database-engine/configure-windows/buffer-pool-extension)</ept>はサポートされていません。
- <ph id="ph1">`ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`</ph> はサポートされていません。 <bpt id="p1">[</bpt>ALTER SERVER CONFIGURATION<ept id="p1">](/sql/t-sql/statements/alter-server-configuration-transact-sql)</ept> に関する記事をご覧ください。

### <a name="collation"></a>照合順序

既定のインスタンスの照合順序は <ph id="ph1">`SQL_Latin1_General_CP1_CI_AS`</ph> であり、作成パラメーターとして指定できます。 「<bpt id="p1">[</bpt>照合順序<ept id="p1">](/sql/t-sql/statements/collations)</ept>」をご覧ください。

### <a name="compatibility-levels"></a>互換性レベル

- サポートされている互換性レベルは、100、110、120、130、140、150 です。
- 100 より低い互換性レベルはサポートされていません。
- 新しいデータベースの既定の互換性レベルは 140 です。 互換性レベルが 100 以上のデータベースが復元された場合、互換性レベルは変更されません。

<bpt id="p1">[</bpt>ALTER DATABASE の互換性レベル<ept id="p1">](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)</ept>に関する記事をご覧ください。

### <a name="database-mirroring"></a>データベース ミラーリング

データベース ミラーリングはサポートされていません。

- <ph id="ph1">`ALTER DATABASE SET PARTNER`</ph> オプションと <ph id="ph2">`SET WITNESS`</ph> オプションはサポートされていません。
- <ph id="ph1">`CREATE ENDPOINT … FOR DATABASE_MIRRORING`</ph> はサポートされていません。

詳細については、<bpt id="p1">[</bpt>ALTER DATABASE の SET PARTNER と SET WITNESS<ept id="p1">](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring)</ept>、および <bpt id="p2">[</bpt>CREATE ENDPOINT … FOR DATABASE_MIRRORING<ept id="p2">](/sql/t-sql/statements/create-endpoint-transact-sql)</ept> の説明をご覧ください。

### <a name="database-options"></a>データベース オプション

- 複数のログ ファイルはサポートされていません。
- インメモリ オブジェクトは、General Purpose サービス レベルではサポートされていません。 
- General Purpose インスタンスあたり 280 ファイル (データベースあたり最大 280 ファイル) の制限があります。 General Purpose レベルのデータ ファイルとログ ファイルの両方がこの制限にカウントされます。 <bpt id="p1">[</bpt>Business Critical レベルでは、データベースあたり 32,767 ファイルがサポートされます<ept id="p1">](./resource-limits.md#service-tier-characteristics)</ept>。
- filestream データを含むファイル グループをデータベースに含めることはできません。 .bak に <ph id="ph1">`FILESTREAM`</ph> データが含まれていると、復元は失敗します。 
- すべてのファイルが Azure Blob Storage に配置されます。 ファイルあたりの IO およびスループットは、個々のファイルのサイズによって異なります。

#### <a name="create-database-statement"></a>CREATE DATABASE ステートメント

次の制限事項が <ph id="ph1">`CREATE DATABASE`</ph> に適用されます。

- ファイルおよびファイル グループは定義できません。 
- <ph id="ph1">`CONTAINMENT`</ph> オプションはサポートされていません。 
- <ph id="ph1">`WITH`</ph> オプションはサポートされていません。 
   > [!TIP]
   > 対処法として、<ph id="ph2">`CREATE DATABASE`</ph> の後に <ph id="ph1">`ALTER DATABASE`</ph> を使用して、ファイルの追加またはコンテインメントの設定を行うデータベース オプションを設定します。 

- <ph id="ph1">`FOR ATTACH`</ph> オプションはサポートされていません。
- <ph id="ph1">`AS SNAPSHOT OF`</ph> オプションはサポートされていません。

詳細については、<bpt id="p1">[</bpt>CREATE DATABASE<ept id="p1">](/sql/t-sql/statements/create-database-sql-server-transact-sql)</ept> に関する記事をご覧ください。

#### <a name="alter-database-statement"></a>ALTER DATABASE ステートメント

ファイルの一部のプロパティは設定または変更できません。

- <ph id="ph1">`ALTER DATABASE ADD FILE (FILENAME='path')`</ph> T-SQL ステートメントでは、ファイル パスは指定できません。 ファイルは SQL Managed Instance によって自動的に配置されるため、スクリプトから <ph id="ph1">`FILENAME`</ph> を削除します。 
- <ph id="ph1">`ALTER DATABASE`</ph> ステートメントを使用してファイル名を変更することはできません。

次のオプションは既定で設定されており、変更することはできません。

- `MULTI_USER`
- `ENABLE_BROKER`
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

一部の <ph id="ph1">`ALTER DATABASE`</ph> ステートメント (たとえば、<bpt id="p1">[</bpt>SET CONTAINMENT<ept id="p1">](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)</ept>) は、データベースの自動バックアップ中やデータベースの作成直後などに一時的に失敗することがあります。 この場合、<ph id="ph1">`ALTER DATABASE`</ph> ステートメントを再試行する必要があります。 関連するエラー メッセージの詳細については、「<bpt id="p1">[</bpt>解説<ept id="p1">](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)</ept>」セクションを参照してください。

詳細については、<bpt id="p1">[</bpt>ALTER DATABASE<ept id="p1">](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)</ept> に関する記事をご覧ください。

### <a name="sql-server-agent"></a>SQL Server エージェント

- SQL Managed Instance では現在、SQL Server エージェントの有効化/無効化はサポートされていません。 SQL エージェントは常に実行されています。
- アイドル状態の CPU に基づくジョブ スケジュール トリガーはサポートされていません。
- SQL Server エージェントの設定は読み取り専用です。 <ph id="ph1">`sp_set_agent_properties`</ph> プロシージャは、SQL Managed Instance ではサポートされていません。 
- ジョブ
  - T-SQL ジョブ ステップがサポートされています。
  - 次のレプリケーション ジョブがサポートされています。
    - トランザクション ログ リーダー
    - スナップショット
    - ディストリビューター
  - SSIS ジョブ ステップがサポートされています。
  - 他の種類のジョブ ステップは現在サポートされていません。
    - マージ レプリケーション ジョブ ステップはサポートされていません。 
    - キュー リーダーはサポートされていません。 
    - コマンド シェルはまだサポートされていません。
  - SQL Managed Instance は、外部リソース (例: robocopy を介したネットワーク共有) にはアクセスできません。 
  - SQL Server Analysis Services はサポートされていません。
- 通知は部分的にサポートされています。
- 電子メール通知がサポートされていますが、データベース メール プロファイルを構成する必要があります。 SQL Server エージェントで使用できるデータベース メール プロファイルは 1 つだけであり、<ph id="ph1">`AzureManagedInstance_dbmail_profile`</ph> という名前である必要があります。 
  - Pager はサポートされていません。
  - NetSend はサポートされていません。
  - アラートはまだサポートされていません。
  - プロキシはサポートされていません。
- EventLog はサポートされていません。
- SQL Agent ジョブを作成、変更、実行するために、ユーザーは Azure AD サーバー プリンシパル (ログイン) に直接マップされる必要があります。 直接マップされていないユーザー (たとえば、SQL Agent ジョブを作成、変更、または実行する権利を持つ Azure AD グループに属しているユーザー) は、これらの操作を実質的に実行できません。 これは、Managed Instance の借用と <bpt id="p1">[</bpt>EXECUTE AS の制限事項<ept id="p1">](#logins-and-users)</ept>のためです。
- マスター/ターゲット (MSX/TSX) ジョブのマルチサーバー管理機能はサポートされていません。

SQL Server エージェントについては、「<bpt id="p1">[</bpt>SQL Server エージェント<ept id="p1">](/sql/ssms/agent/sql-server-agent)</ept>」をご覧ください。

### <a name="tables"></a>テーブル

次のテーブルの種類はサポートされていません。

- <bpt id="p1">[</bpt>FILESTREAM<ept id="p1">](/sql/relational-databases/blob/filestream-sql-server)</ept>
- <bpt id="p1">[</bpt>FILETABLE<ept id="p1">](/sql/relational-databases/blob/filetables-sql-server)</ept>
- <bpt id="p1">[</bpt>EXTERNAL TABLE<ept id="p1">](/sql/t-sql/statements/create-external-table-transact-sql)</ept> (Polybase)
- <bpt id="p1">[</bpt>MEMORY_OPTIMIZED<ept id="p1">](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)</ept> (General Purpose レベルでのみサポートされません)

テーブルの作成および変更方法については、<bpt id="p1">[</bpt>CREATE TABLE<ept id="p1">](/sql/t-sql/statements/create-table-transact-sql)</ept> に関する記事および <bpt id="p2">[</bpt>ALTER TABLE<ept id="p2">](/sql/t-sql/statements/alter-table-transact-sql)</ept> に関する記事をご覧ください。

## <a name="functionalities"></a>機能

### <a name="bulk-insert--openrowset"></a>一括挿入/OPENROWSET

SQL Managed Instance はファイル共有や Windows フォルダーにアクセスできないため、ファイルは Azure BLOB ストレージからインポートする必要があります。

- が、Azure BLOB ストレージからファイルをインポートしている間、<ph id="ph2">`BULK INSERT`</ph> コマンドで <ph id="ph1">`DATASOURCE`</ph> が必要です。 <bpt id="p1">[</bpt>BULK INSERT<ept id="p1">](/sql/t-sql/statements/bulk-insert-transact-sql)</ept> に関する記事をご覧ください。
- Azure BLOB ストレージからのファイルの内容を読み取る場合、<ph id="ph2">`OPENROWSET`</ph> 関数で <ph id="ph1">`DATASOURCE`</ph> が必要です。 <bpt id="p1">[</bpt>OPENROWSET<ept id="p1">](/sql/t-sql/functions/openrowset-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`OPENROWSET`</ph> を使用すると、Azure SQL Database、Azure SQL Managed Instance、または SQL Server インスタンスからデータを読み取ることができます。 Oracle データベースや Excel ファイルなどのその他のソースはサポートされていません。

### <a name="clr"></a>CLR

SQL Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。

- サポートされるのは <ph id="ph1">`CREATE ASSEMBLY FROM BINARY`</ph> のみです。 <bpt id="p1">[</bpt>CREATE ASSEMBLY FROM BINARY<ept id="p1">](/sql/t-sql/statements/create-assembly-transact-sql)</ept> に関する記事をご覧ください。 
- <ph id="ph1">`CREATE ASSEMBLY FROM FILE`</ph> はサポートされていません。 <bpt id="p1">[</bpt>CREATE ASSEMBLY FROM FILE<ept id="p1">](/sql/t-sql/statements/create-assembly-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`ALTER ASSEMBLY`</ph> ではファイルを参照できません。 <bpt id="p1">[</bpt>ALTER ASSEMBLY<ept id="p1">](/sql/t-sql/statements/alter-assembly-transact-sql)</ept> に関する記事をご覧ください。

### <a name="database-mail-db_mail"></a>データベース メール (db_mail)
 - <ph id="ph1">`sp_send_dbmail`</ph> では、<ph id="ph2">@file_attachments</ph> パラメーターを使用して添付ファイルを送信できません。 この手順では、ローカル ファイル システムと外部共有または Azure Blob Storage にはアクセスできません。
 - <ph id="ph1">`@query`</ph> パラメーターと認証に関連する既知の問題をご覧ください。
 
### <a name="dbcc"></a>DBCC

SQL Server で有効になっている、ドキュメントに記載されていない DBCC ステートメントは、SQL Managed Instance ではサポートされていません。

- サポートされるグローバル トレース フラグの数は限られています。 セッションレベルの <ph id="ph1">`Trace flags`</ph> はサポートされません。 <bpt id="p1">[</bpt>トレース フラグ<ept id="p1">](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)</ept>に関する記事をご覧ください。
- <bpt id="p1">[</bpt>DBCC TRACEOFF<ept id="p1">](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)</ept> と <bpt id="p2">[</bpt>DBCC TRACEON<ept id="p2">](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)</ept> で使用できるトレースフラグの数が限られています。
- <bpt id="p1">[</bpt>DBCC CHECKDB<ept id="p1">](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql)</ept> とオプション REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST、REPAIR_REBUILD の組み合わせは使用できません。これは、データベースを <ph id="ph1">`SINGLE_USER`</ph> モードで設定することはできないためです。<bpt id="p2">[</bpt>ALTER DATABASE の相違点<ept id="p2">](#alter-database-statement)</ept>を参照してください。 データベースが破損した場合は Azure サポート チームが対応します。 データベースが破損している徴候がある場合は、Azure サポートにお問い合わせください。

### <a name="distributed-transactions"></a>分散トランザクション

<bpt id="p1">[</bpt>分散トランザクション<ept id="p1">](../database/elastic-transactions-overview.md)</ept>の部分的なサポートは、現在パブリック プレビューの段階です。 分散トランザクションは次の条件で使用できます (すべての条件を満たす必要があります)。
* すべてのトランザクション参加者が、<bpt id="p1">[</bpt>サーバー信頼グループ<ept id="p1">](./server-trust-group-overview.md)</ept>に属する Azure SQL Managed Instance である。
* .NET (TransactionScope クラス) または Transact-SQL でトランザクションを開始する。

現在、Azure SQL Managed Instance では、MSDTC オンプレミスまたは Azure Virtual Machines で定期的にサポートされている他のシナリオはサポートされていません。

### <a name="extended-events"></a>拡張イベント

拡張イベント (XEvents) の一部の Windows 固有のターゲットはサポートされていません。

- <ph id="ph1">`etw_classic_sync`</ph> ターゲットはサポートされていません。 Azure BLOB ストレージに <ph id="ph1">`.xel`</ph> ファイルを保存します。 「<bpt id="p1">[</bpt>etw_classic_sync ターゲット<ept id="p1">](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)</ept>」をご覧ください。
- <ph id="ph1">`event_file`</ph> ターゲットはサポートされていません。 Azure BLOB ストレージに <ph id="ph1">`.xel`</ph> ファイルを保存します。 「<bpt id="p1">[</bpt>event_file ターゲット<ept id="p1">](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)</ept>」をご覧ください。

### <a name="external-libraries"></a>外部ライブラリ

In-Database R および Python 外部ライブラリは、限られたパブリック プレビューでサポートされています。 「<bpt id="p1">[</bpt>Azure SQL Managed Instance の Machine Learning Services (プレビュー)<ept id="p1">](machine-learning-services-overview.md)</ept>」を参照してください。

### <a name="filestream-and-filetable"></a>Filestream と FileTable

- filestream データはサポートされていません。
- <ph id="ph1">`FILESTREAM`</ph> データを含むファイル グループをデータベースに含めることはできません。
- <ph id="ph1">`FILETABLE`</ph> はサポートされていません。
- テーブルに <ph id="ph1">`FILESTREAM`</ph> 型を含めることはできません。
- 次の関数はサポートされていません。
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

詳細については、<bpt id="p1">[</bpt>FILESTREAM<ept id="p1">](/sql/relational-databases/blob/filestream-sql-server)</ept> に関する記事、および <bpt id="p2">[</bpt>FileTables<ept id="p2">](/sql/relational-databases/blob/filetables-sql-server)</ept> に関する記事をご覧ください。

### <a name="full-text-semantic-search"></a>フルテキスト セマンティック検索

<bpt id="p1">[</bpt>セマンティック検索<ept id="p1">](/sql/relational-databases/search/semantic-search-sql-server)</ept>はサポートされていません。

### <a name="linked-servers"></a>リンク サーバー

SQL Managed Instance の[リンク サーバー](/sql/relational-databases/linked-servers/linked-servers-database-engine)がサポートするターゲットの数は限られています。

- サポートされているターゲットは、SQL Managed Instance、SQL Database、Azure Synapse SQL の<bpt id="p1">[</bpt>サーバーレス<ept id="p1">](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)</ept>と専用プール、および SQL Server インスタンスです。 
- 分散書き込み可能なトランザクションは、マネージド インスタンス間でのみ可能です。 詳細については、<bpt id="p1">[</bpt>分散トランザクション<ept id="p1">](../database/elastic-transactions-overview.md)</ept>に関する記事を参照してください。 ただし、MS DTC はサポートされていません。
- サポートされていないターゲットは、ファイル、Analysis Services、他の RDBMS です。 ファイル インポートの代わりに <ph id="ph1">`BULK INSERT`</ph> または <ph id="ph2">`OPENROWSET`</ph> を使用して Azure Blob Storage からネイティブ CSV インポートを使用するか、<bpt id="p1">[</bpt>Azure Synapse Analytics 内のサーバーレス SQL プール<ept id="p1">](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)</ept>を使用してファイルの読み込みを試行します。

操作: 

- <bpt id="p1">[</bpt>インスタンス間<ept id="p1">](../database/elastic-transactions-overview.md)</ept>書き込みトランザクションは、マネージド インスタンスでのみサポートされています。
- リンク サーバーの削除で <ph id="ph1">`sp_dropserver`</ph> がサポートされています。 <bpt id="p1">[</bpt>sp_dropserver<ept id="p1">](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)</ept> に関する記事をご覧ください。
- SQL Server インスタンスでのみ、<ph id="ph1">`OPENROWSET`</ph> 関数を使用してクエリを実行できます。 これらは、マネージド、オンプレミス、仮想マシンのいずれかで配置できます。 <bpt id="p1">[</bpt>OPENROWSET<ept id="p1">](/sql/t-sql/functions/openrowset-transact-sql)</ept> に関する記事をご覧ください。
- SQL Server インスタンスでのみ、<ph id="ph1">`OPENDATASOURCE`</ph> 関数を使用してクエリを実行できます。 これらは、マネージド、オンプレミス、仮想マシンのいずれかで配置できます。 プロバイダーとしてサポートされる値は、<ph id="ph1">`SQLNCLI`</ph>、<ph id="ph2">`SQLNCLI11`</ph>、<ph id="ph3">`SQLOLEDB`</ph> だけです。 たとえば `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` です。 <bpt id="p1">[</bpt>OPENDATASOURCE<ept id="p1">](/sql/t-sql/functions/opendatasource-transact-sql)</ept> に関する記事をご覧ください。
- リンク サーバーを使用してネットワーク共有からファイル (Excel、CSV) を読み取ることはできません。 <bpt id="p1">[</bpt>BULK INSERT<ept id="p1">](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)</ept>、<bpt id="p2">[</bpt>OPENROWSET<ept id="p2">](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)</ept> (Azure Blob Storage から CSV ファイルを読み取る)、または <bpt id="p3">[</bpt>Synapse Analytics 内のサーバーレス SQL プールを参照するリンク サーバー<ept id="p3">](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)</ept>の使用を試行します。 この要求は、<bpt id="p1">[</bpt>SQL Managed Instance フィードバック項目<ept id="p1">](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)</ept><ph id="ph1">|</ph>で追跡します

Azure SQL Managed Instance のリンク サーバーでは、SQL 認証と [AAD 認証](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#linked-servers-with-azure-sql-managed-instance)の両方がサポートされています。

### <a name="polybase"></a>PolyBase

<<<<<<< HEAD
Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse プールに対して唯一使用可能な外部ソースの種類は RDBMS (パブリック プレビュー段階) です。 Azure Storage から直接読み取る Polybase 外部テーブルの回避策として、[Synapse Analytics 内のサーバーレス SQL プールを参照する外部テーブル](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)を使用できます。 Azure SQL Managed Instance では、[Synapse Analytics 内のサーバーレス SQL プール](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)や SQL Server へのリンク サーバーを使用して、Azure Storage のデータを読み取ることができます。
PolyBase については、[PolyBase](/sql/relational-databases/polybase/polybase-guide) に関する記事をご覧ください。
=======
SQL Managed Instance で Polybase のサポートを有効にする取り組みが<bpt id="p1">[</bpt>進行中<ept id="p1">](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35698078-enable-polybase-on-sql-managed-instance)</ept>です。 その間、対処法として、SQL Server または <bpt id="p1">[</bpt>Synapse Analytics のサーバーレス SQL プール<ept id="p1">](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)</ept>へのリンク サーバーを使用して、Azure Data Lake または Azure Storage に格納されているファイルからデータのクエリを実行できます。   
PolyBase の一般情報については、<bpt id="p1">[</bpt>PolyBase<ept id="p1">](/sql/relational-databases/polybase/polybase-guide)</ept> に関する記事を参照してください。
>>>>>>> repo_sync_working_branch

### <a name="replication"></a>レプリケーション

- スナップショットおよび双方向のレプリケーションの種類がサポートされています。 マージ レプリケーション、ピア ツー ピア レプリケーション、および更新可能サブスクリプションはサポートされていません。
- <bpt id="p1">[</bpt>トランザクション レプリケーション<ept id="p1">](replication-transactional-overview.md)</ept>は SQL Managed Instance のパブリック プレビューで使用できますが、制約がいくつかあります。
    - すべての種類のレプリケーション参加者 (パブリッシャー、ディストリビューター、プル サブスクライバー、プッシュ サブスクライバー) を SQL Managed Instance に配置できますが、パブリッシャーとディストリビューターは両者ともクラウドに配置するか、または両者ともオンプレミスに配置する必要があります。
    - SQL Managed Instance は、最新バージョンの SQL Server と通信できます。 詳細については、<bpt id="p1">[</bpt>バージョンのサポート マトリックス<ept id="p1">](replication-transactional-overview.md#supportability-matrix)</ept>を参照してください。
    - トランザクション レプリケーションには、いくつかの<bpt id="p1">[</bpt>追加のネットワーク要件<ept id="p1">](replication-transactional-overview.md#requirements)</ept>があります。

トランザクション レプリケーションの構成の詳細については、次に関するチュートリアルを参照してください。
- <bpt id="p1">[</bpt>SQL MI パブリッシャーと SQL MI サブスクライバー間のレプリケーション<ept id="p1">](replication-between-two-instances-configure-tutorial.md)</ept>
- <bpt id="p1">[</bpt>SQL MI パブリッシャー、SQL MI ディストリビューター、および SQL Server サブスクライバー間のレプリケーション<ept id="p1">](replication-two-instances-and-sql-server-configure-tutorial.md)</ept>

### <a name="restore-statement"></a>RESTORE ステートメント 

- サポートされている構文:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- サポートされていない構文:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- ソース: 
  - <ph id="ph1">`FROM URL`</ph> (Azure BLOB ストレージ) が、サポートされている唯一のオプションです。
  - <ph id="ph1">`FROM DISK`</ph><ph id="ph2">/</ph><ph id="ph3">`TAPE`</ph>/バックアップ デバイスはサポートされていません。
  - バックアップ セットはサポートされていません。
- <ph id="ph1">`WITH`</ph> オプションはサポートされていません。 <ph id="ph2">`DIFFERENTIAL`</ph>、<ph id="ph3">`STATS`</ph>、<ph id="ph4">`REPLACE`</ph>などの <ph id="ph1">`WITH`</ph> を含む復元の試行は失敗します。
- <ph id="ph1">`ASYNC RESTORE`</ph>:クライアント接続が切断されても、復元は続行されます。 接続が破棄された場合は、<ph id="ph1">`sys.dm_operation_status`</ph> ビューで復元操作とデータベースの CREATE 操作および DROP 操作の状態を確認できます。 <bpt id="p1">[</bpt>sys.dm_operation_status<ept id="p1">](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)</ept> に関する記事をご覧ください。 

次のデータベース オプションを設定またはオーバーライドします。これらを後で変更することはできません。 

- <ph id="ph1">`NEW_BROKER`</ph> (.bak ファイルでブローカーが有効になっていない場合)。 
- <ph id="ph1">`ENABLE_BROKER`</ph> (.bak ファイルでブローカーが有効になっていない場合)。 
- <ph id="ph1">`AUTO_CLOSE=OFF`</ph> (.bak ファイル内のデータベースに <ph id="ph2">`AUTO_CLOSE=ON`</ph> が設定されている場合)。 
- <ph id="ph1">`RECOVERY FULL`</ph> (.bak ファイル内のデータベースが <ph id="ph2">`SIMPLE`</ph> または <ph id="ph3">`BULK_LOGGED`</ph> 回復モードの場合)。
- メモリ最適化ファイル グループが追加され、(ソースの .bak ファイルに含まれていなかった場合) XTP という名前が付けられます。 
- 既存のメモリ最適化ファイル グループの名前が XTP に変更されます。 
- <ph id="ph1">`SINGLE_USER`</ph> および <ph id="ph2">`RESTRICTED_USER`</ph> のオプションは、<ph id="ph3">`MULTI_USER`</ph> に変換されます。

制限事項: 

- 破損したデータベースのバックアップは、破損の種類によっては復元される可能性がありますが、破損が修正されるまで自動バックアップは実行されません。 この問題を回避するには、必ずソース SQL Managed Instance 上で <ph id="ph1">`DBCC CHECKDB`</ph> を実行し、バックアップ <ph id="ph2">`WITH CHECKSUM`</ph> を使用します。
- このドキュメントで説明されている制限 (たとえば <ph id="ph2">`FILESTREAM`</ph> または <ph id="ph3">`FILETABLE`</ph> オブジェクト) を含むデータベースの <ph id="ph1">`.BAK`</ph> ファイルの復元は、SQL Managed Instance では復元できません。
- <ph id="ph1">`.BAK`</ph> ファイルに複数のバックアップ セットが含まれている場合、復元できません。 
- <ph id="ph1">`.BAK`</ph> ファイルに複数のログ ファイルが含まれている場合、復元できません。
- 8 TB を超えるデータベース、アクティブなインメモリ OLTP オブジェクト、または 280 個を超えるファイル数を含むバックアップは、General Purpose インスタンスでは復元できません。 
- 4 TB を超えるデータベース、または<bpt id="p1">[</bpt>リソースの制限<ept id="p1">](resource-limits.md)</ept>で説明されているサイズよりも合計サイズが大きいインメモリ OLTP オブジェクトを含むバックアップは、Business Critical インスタンスでは復元できません。
RESTORE ステートメントについては、<bpt id="p1">[</bpt>RESTORE ステートメント<ept id="p1">](/sql/t-sql/statements/restore-statements-transact-sql)</ept>に関する記事をご覧ください。

 > [!IMPORTANT]
 > 同じ制限が、組み込みのポイントインタイム リストア操作に適用されます。 たとえば、4 TB を超える General Purpose データベースは、Business Critical インスタンスで復元することはできません。 インメモリ OLTP ファイルまたは 280 個を超えるファイルを含む Business Critical データベースは、General Purpose インスタンスで復元することはできません。

### <a name="service-broker"></a>Service Broker

クロス インスタンス Service Broker メッセージ交換は、Azure SQL Managed Instance 間でのみサポートされます。

- <ph id="ph1">`CREATE ROUTE`</ph>: <ph id="ph2">`CREATE ROUTE`</ph> を <ph id="ph4">`LOCAL`</ph> 以外の <ph id="ph3">`ADDRESS`</ph> または別の Azure SQL Managed Instance の DNS 名で使用することはできません。 ポートは常に 4022 です。
- <ph id="ph1">`ALTER ROUTE`</ph>: <ph id="ph2">`ALTER ROUTE`</ph> を <ph id="ph4">`LOCAL`</ph> 以外の <ph id="ph3">`ADDRESS`</ph> または別の Azure SQL Managed Instance の DNS 名で使用することはできません。 ポートは常に 4022 です。

トランスポート セキュリティはサポートされていますが、ダイアログ セキュリティはサポートされていません。
- <ph id="ph1">`CREATE REMOTE SERVICE BINDING`</ph> はサポートされていません。

Service Broker は既定で有効になっており、無効にできません。 次の ALTER DATABASE オプションはサポートされていません。
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>ストアド プロシージャ、関数、トリガー

- <ph id="ph1">`NATIVE_COMPILATION`</ph> は、General Purpose レベルではサポートされていません。
- <bpt id="p1">[</bpt>sp_configure<ept id="p1">](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql)</ept> の次のオプションはサポートされていません。 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- 次の <bpt id="p1">[</bpt>sp_configure<ept id="p1">](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql)</ept> オプションは無視され、影響はありません。 
  - `Ole Automation Procedures`
- <ph id="ph1">`sp_execute_external_scripts`</ph> はサポートされていません。 <bpt id="p1">[</bpt>sp_execute_external_scripts<ept id="p1">](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)</ept> に関するセクションをご覧ください。
- <ph id="ph1">`xp_cmdshell`</ph> はサポートされていません。 <bpt id="p1">[</bpt>xp_cmdshell<ept id="p1">](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`Extended stored procedures`</ph> はサポートされておらず、これには <ph id="ph2">`sp_addextendedproc`</ph> および <ph id="ph3">`sp_dropextendedproc`</ph> が含まれます。 この機能は SQL Server では非推奨になる予定のため、サポートされません。 詳細については、<bpt id="p1">[</bpt>拡張ストアド プロシージャ<ept id="p1">](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)</ept>に関するページを参照してください。
- <ph id="ph1">`sp_attach_db`</ph>、<ph id="ph2">`sp_attach_single_file_db`</ph>、<ph id="ph3">`sp_detach_db`</ph> はサポートされていません。 <bpt id="p1">[</bpt>sp_attach_db<ept id="p1">](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)</ept>、<bpt id="p2">[</bpt>sp_attach_single_file_db<ept id="p2">](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)</ept>、<bpt id="p3">[</bpt>sp_detach_db<ept id="p3">](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)</ept> に関する各記事をご覧ください。

### <a name="system-functions-and-variables"></a>システム関数とシステム変数

次の変数、関数、ビューは異なる結果を返します。

- <ph id="ph1">`SERVERPROPERTY('EngineEdition')`</ph> は値 8 を返します。 このプロパティは、SQL Managed Instance を一意に識別します。 <bpt id="p1">[</bpt>SERVERPROPERTY<ept id="p1">](/sql/t-sql/functions/serverproperty-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`SERVERPROPERTY('InstanceName')`</ph> は NULL を返します。これは SQL Server に関して存在するインスタンスの概念が、SQL Managed Instance には該当しないためです。 <bpt id="p1">[</bpt>SERVERPROPERTY('InstanceName')<ept id="p1">](/sql/t-sql/functions/serverproperty-transact-sql)</ept> に関する説明をご覧ください。
- <ph id="ph1">`@@SERVERNAME`</ph> は、"接続可能な" 完全 DNS 名を返します (例: my-managed-instance.wcus17662feb9ce98.database.windows.net)。 <bpt id="p1">[</bpt>@<ph id="ph1">@SERVERNAME</ph><ept id="p1">](/sql/t-sql/functions/servername-transact-sql)</ept> に関する記事をご覧ください。 
- <ph id="ph1">`SYS.SERVERS`</ph> は、"name" プロパティと "data_source" プロパティを表す <ph id="ph2">`myinstance.domain.database.windows.net`</ph> のような、"接続可能な" 完全 DNS 名を返します。 <bpt id="p1">[</bpt>SYS.SERVERS<ept id="p1">](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`@@SERVICENAME`</ph> は NULL を返します。これは SQL Server に関して存在するサービスの概念が、SQL Managed Instance には該当しないためです。 <bpt id="p1">[</bpt>@<ph id="ph1">@SERVICENAME</ph><ept id="p1">](/sql/t-sql/functions/servicename-transact-sql)</ept> に関する記事をご覧ください。
- <ph id="ph1">`SUSER_ID`</ph> はサポートされています。 Azure AD ログインが sys.syslogins に含まれていない場合は、NULL を返します。 <bpt id="p1">[</bpt>SUSER_ID<ept id="p1">](/sql/t-sql/functions/suser-id-transact-sql)</ept> に関する記事をご覧ください。 
- <ph id="ph1">`SUSER_SID`</ph> はサポートされていません。 正しくないデータが返されます。これは既知の一時的な問題です。 <bpt id="p1">[</bpt>SUSER_SID<ept id="p1">](/sql/t-sql/functions/suser-sid-transact-sql)</ept> に関する記事をご覧ください。 

## <a name="environment-constraints"></a><bpt id="p1"><a name="Environment"></bpt><ept id="p1"></a></ept>環境の制約

### <a name="subnet"></a>Subnet
-  SQL Managed Instance をデプロイしたサブネットに他のリソース (たとえば仮想マシン) を配置することはできません。 別のサブネットを使用してこれらのリソースをデプロイしてください。
- サブネットには、十分な数の利用可能な [IP アドレス](connectivity-architecture-overview.md#network-requirements)が含まれている必要があります。 最小で、サブネットに少なくとも 32 個の IP アドレスを設定します。
- リージョンでデプロイできるインスタンスの仮想コア数と種類には、いくつかの<bpt id="p1">[</bpt>制約と制限<ept id="p1">](resource-limits.md#regional-resource-limitations)</ept>があります。
- サブネットに適用する必要がある<bpt id="p1">[</bpt>ネットワーク構成<ept id="p1">](connectivity-architecture-overview.md#network-requirements)</ept>があります。

### <a name="vnet"></a>VNet
- VNet はリソース モデルを使用してデプロイできます。VNet のクラシック モードはサポートされていません。
- SQL Managed Instance の作成後の SQL Managed Instance または VNet の別のリソース グループまたはサブスクリプションへの移動はサポートされていません。
- 2020 年 9 月 22 日より前に作成された仮想クラスターでホストされる SQL マネージド インスタンスでは、<bpt id="p1">[</bpt>グローバル ピアリング<ept id="p1">](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)</ept>がサポートされません。 ExpressRoute、または VNet ゲートウェイ経由の VNet 対 VNet を介してこのようなリソースに接続できます。

### <a name="failover-groups"></a>フェールオーバー グループ
システム データベースは、フェールオーバー グループのセカンダリ インスタンスにはレプリケートされません。 そのため、オブジェクトがセカンダリに手動で作成されていない限り、セカンダリ インスタンスではシステム データベースのオブジェクトに依存するシナリオは実現できません。

### <a name="tempdb"></a>TEMPDB
- <ph id="ph1">`tempdb`</ph> の最大ファイル サイズは、General Purpose レベルではコアあたり 24 GB より大きくすることはできません。 Business Critical レベルでは、<ph id="ph1">`tempdb`</ph> の最大サイズは SQL Managed Instance ストレージ サイズによって制限されます。 <ph id="ph1">`Tempdb`</ph> ログ ファイルのサイズは、General Purpose レベルでは 120 GB に制限されています。 <ph id="ph1">`tempdb`</ph> のサイズがコアあたり 24 GB を超える場合、または 120 GB を超えるログ データが生成される場合は、一部のクエリでエラーが返されます。
- <ph id="ph1">`Tempdb`</ph> は常に 12 個のデータ ファイルに分割されます (1 個のプライマリ (マスターとも呼ばれる) データ ファイルと 11 個のプライマリ以外のデータ ファイル)。 ファイル構造を変更することも、<ph id="ph1">`tempdb`</ph> に新しいファイルを追加することもできません。 
- <bpt id="p1">[</bpt>[メモリ最適化]<ph id="ph1">`tempdb`</ph> メタデータ<ept id="p1">](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata)</ept> (新しい SQL Server 2019 のメモリ内データベース機能) は、サポートされていません。
- <ph id="ph2">`tempdb`</ph> ではモデル データベースから初期オブジェクト リストが取得されないため、再起動後またはフェールオーバー後に、モデル データベースで作成されたオブジェクトを <ph id="ph1">`tempdb`</ph> で自動作成できません。 再起動後またはフェールオーバー後に、<ph id="ph1">`tempdb`</ph> でオブジェクトを手動で作成する必要があります。

### <a name="msdb"></a>MSDB

SQL Managed Instance の次の MSDB スキーマは、それぞれの定義済みロールによって所有されている必要があります。

- 一般的なロール
  - TargetServersRole
- <bpt id="p1">[</bpt>固定データベース ロール<ept id="p1">](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)</ept>
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- <bpt id="p1">[</bpt>DatabaseMail ロール<ept id="p1">](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile)</ept>:
  - DatabaseMailUserRole
- <bpt id="p1">[</bpt>統合サービスのロール<ept id="p1">](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true)</ept>:
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 定義済みのロール名、スキーマ名、スキーマ所有者を顧客が変更すると、サービスの通常の動作に影響します。 通常のサービス動作を保証するために、これらに加えられた変更は検出されるとすぐに、または次の最新のサービス更新時に、事前に定義済みの値に戻されます。

### <a name="error-logs"></a>エラー ログ

SQL Managed Instance では、エラー ログに詳細情報が書き込まれます。 エラー ログに記録される内部システム イベントが数多く存在します。 カスタムの手順を使用して、関連のない項目をフィルターで除外するエラー ログを読み取ります。 詳細については、Azure Data Studio の <bpt id="p1">[</bpt>SQL Managed Instance - sp_readmierrorlog<ept id="p1">](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog)</ept> または <bpt id="p2">[</bpt>SQL Managed Instance の拡張機能 (プレビュー)<ept id="p2">](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs)</ept> に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- SQL Managed Instance の詳細については、<bpt id="p1">[</bpt>SQL Managed Instance の概要<ept id="p1">](sql-managed-instance-paas-overview.md)</ept>に関する記事を参照してください。
- 機能と比較の一覧については、<bpt id="p1">[</bpt>Azure SQL Managed Instance の機能の比較<ept id="p1">](../database/features-comparison.md)</ept>に関する記事を参照してください。
- リリースの更新については、[新機能](doc-changes-updates-release-notes-whats-new.md)に関するページを参照してください。
- 問題、回避策、および解決策については、[既知の問題](doc-changes-updates-known-issues.md)に関するページを参照してください。
- 新しい SQL Managed Instance の作成方法を示したクイックスタートについては、<bpt id="p1">[</bpt>SQL Managed Instance の作成<ept id="p1">](instance-create-quickstart.md)</ept>に関するページを参照してください。