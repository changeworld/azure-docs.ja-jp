---
title: ダイジェストの管理とデータベースの検証
description: この記事では、Azure SQL Database での台帳データベースのダイジェスト管理とデータベース検証について説明します。
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: df0b87543c213b77a3cf47a7e9020f7f3c0e1866
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059585"
---
# <a name="digest-management-and-database-verification"></a>ダイジェストの管理とデータベースの検証

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

Azure SQL Database 台帳では、*前方整合性* と呼ばれるデータ整合性の形式が提供されており、台帳テーブルのデータに対するデータ改ざんに関する証拠が示されます。 たとえば、残高が `x` の値に更新された台帳テーブルで銀行取引が発生し、攻撃者が後でデータを変更して `x` から `y` に残高を変えた場合、データベースの検証によって、この改ざんアクティビティが検出されます。  

データベース検証プロセスでは、以前に生成された 1 つ以上のデータベース ダイジェストが入力として受け取られます。 続いて、現在の台帳テーブルの状態に基づいて、データベース台帳に格納されているハッシュが再度計算されます。 計算されたハッシュが入力ダイジェストと一致しない場合、検証は失敗します。 このエラーは、データが改ざんされていることを示します。 検証プロセスで、検出されたすべての不一致が報告されます。

## <a name="database-digests"></a>データベース ダイジェスト

データベース台帳における最新のブロックのハッシュは、*データベース ダイジェスト* と呼ばれます。 これは、ブロックが生成された時点のデータベース内のすべての台帳テーブルの状態を表します。 データベース ダイジェストの生成は、最近追加されたブロックのハッシュだけが計算されるため、効率的です。 

データベース ダイジェストは、システムによって自動的に生成することも、ユーザーが手動で生成することもできます。 後でこれらを使用して、データベースのデータ整合性を確認することができます。 

データベース ダイジェストは、最新ブロックのハッシュと、ブロック ID のメタデータを含む JSON ドキュメントの形式で生成されます。 メタデータには、ダイジェストが生成された時刻と、このブロック内の最後のトランザクションのコミット タイムスタンプが含まれます。

検証プロセスとデータベースの整合性は、入力ダイジェストの整合性によって左右されます。 このため、データベースから抽出されるデータベース ダイジェストは、Azure SQL Database サーバーの高い特権を持つユーザーや攻撃者が改ざんできない、信頼できるストレージに保管する必要があります。

### <a name="automatic-generation-and-storage-of-database-digests"></a>データベース ダイジェストの自動生成と保存

Azure SQL Database 台帳は、[Azure Blob Storage の不変ストレージ機能](../../storage/blobs/immutable-storage-overview.md)および [Azure Confidential Ledger](../../confidential-ledger/index.yml) と統合されています。 この統合により、Azure においてセキュリティで保護されたストレージ サービスがもたらされ、データベース ダイジェストは改ざんの可能性から保護されます。 この統合により、ユーザーは可用性や地理的なレプリケーションについて心配することなく、簡単でコスト効率の高い方法でダイジェスト管理を自動化できます。 

Azure portal、PowerShell、または Azure CLI を使用して、データベース ダイジェストの自動生成とストレージを構成できます。 自動生成とストレージを構成すると、データベース ダイジェストは、30 秒の事前定義された間隔で生成され、選択したストレージ サービスにアップロードされます。 30 秒間隔でシステムでトランザクションが発生しない場合、データベース ダイジェストは生成およびアップロードされません。 このメカニズムにより、データベース ダイジェストは、データベースでデータが更新された場合にのみ生成されます。

:::image type="content" source="media/ledger/automatic-digest-management.png" alt-text="ダイジェスト ストレージを有効にするための選択内容を示したスクリーンショット。"::: 

> [!IMPORTANT]
> データベース ダイジェストが改ざんから確実に保護されるように、プロビジョニングの後、コンテナーで[不変性ポリシー](../../storage/blobs/immutable-policy-configure-version-scope.md)を構成します。

### <a name="manual-generation-and-storage-of-database-digests"></a>データベース ダイジェストの手動生成と保存

Azure SQL Database 台帳を使用して、必要に応じてデータベース ダイジェストを生成し、信頼される保管先と見なされるサービスやデバイスにダイジェストを手動で保管することもできます。 たとえば、オンプレミスの Write Once Read Many (WORM) デバイスを保管先として選択することができます。 データベース ダイジェストを手動で生成するには、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) で、[sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) ストアド プロシージャを実行します。

> [!IMPORTANT]
> データベース ダイジェストを生成するには、**GENERATE LEDGER DIGEST** 権限が必要です。 台帳テーブルに関連する権限の詳細は、[権限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関する記事をご覧ください。 

```sql
EXECUTE sp_generate_database_ledger_digest
```

返される結果セットは、1 行のデータです。 これは、次のように、JSON ドキュメントとして信頼できる保管場所に保存する必要があります。

```json
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    }
```

## <a name="database-verification"></a>データベースの検証

検証プロセスでは、すべての台帳テーブルと履歴テーブルがスキャンされます。 それらの行の SHA-256 ハッシュが再計算されて、検証ストアド プロシージャに渡されたデータベース ダイジェスト ファイルと比較されます。 

大規模な台帳テーブルでは、データベースの検証はリソースを集中的に使用するプロセスになる可能性があります。 これは、データベースの整合性を確認する必要がある場合にのみ使用してください。 

検証プロセスは、データベースの整合性を頻繁に監視する必要がある場合は、1 時間ごとまたは毎日実行できます。 または、データをホストしている組織が監査を受け、データの整合性に関する暗号化された証拠を提供する必要がある場合にのみ実行することができます。 検証コストを削減するために、台帳では、個々の台帳テーブル、または台帳テーブルの一部のみを検証するオプションが公開されています。 

[自動ダイジェスト ストレージを使用](#database-verification-that-uses-automatic-digest-storage)するか、[手動でダイジェストを管理](#database-verification-that-uses-manual-digest-storage)するかに応じて、2 つのストアド プロシージャを使用してデータベース検証を実行します。

> [!IMPORTANT]
> データベースの検証には、*VIEW LEDGER CONTENT* 権限が必要です。 台帳テーブルに関連するアクセス許可の詳細については、[アクセス許可](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関するページを参照してください。 

### <a name="database-verification-that-uses-automatic-digest-storage"></a>自動ダイジェスト ストレージを使用したデータベース検証

データベース ダイジェストの生成と格納に自動ダイジェスト ストレージを使用する場合、ダイジェスト ストレージの場所は、JSON オブジェクトとして [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) システム カタログ ビュー内にあります。 データベース検証の実行は、[sp_verify_database_ledger_from_digest_storage](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-from-digest-storage-transact-sql) システム ストアド プロシージャの実行から構成されます。 データベース ダイジェストが格納されるように構成されている [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) システム カタログ ビューから JSON オブジェクトを指定します。 

自動ダイジェスト ストレージを使用すると、台帳テーブルのライフサイクル全体でストレージの場所を変更できます。  たとえば、最初は Azure 不変ストレージを使用してダイジェスト ファイルを格納するが、後で代わりに Azure Confidential Ledger を使用する場合は、それを行うことができます。 この場所の変更は、[sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) に保管されます。 

複数のダイジェスト ストレージの場所を使用している場合に検証の実行を簡略化するために、次のスクリプトでは、ダイジェストの場所をフェッチし、それらの場所を使用して検証を実行します。

```sql
DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);
SELECT @digest_locations as digest_locations;
BEGIN TRY
    EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
    SELECT 'Ledger verification succeeded.' AS Result;
END TRY
BEGIN CATCH
    THROW;
END CATCH
```

### <a name="database-verification-that-uses-manual-digest-storage"></a>手動ダイジェスト ストレージを使用したデータベース検証

データベース ダイジェストの生成と格納に手動ダイジェスト ストレージを使用する場合は、次のストアド プロシージャを使用して台帳データベースを検証します。 ダイジェストの JSON コンテンツがストアド プロシージャに追加されます。 データベース検証を実行しているときに、データベース内のすべてのテーブルを検証するか、特定のテーブルを検証するかを選択できます。 

次に、[sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) ストアド プロシージャの構文を示します。

```sql
sp_verify_database_ledger <JSON_document_containing_digests>, <table_name> 
```

次のコードは、検証用に 2 つのダイジェストを渡して [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) ストアド プロシージャを実行する例です。 

```sql
EXECUTE sp_verify_database_ledger N'
[
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    },
    {
        "database_name":  "ledgerdb",
        "block_id":  1,
        "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
        "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
        "digest_time":  "2020-11-12T18:43:30.4701575"
    }
]
'
```

`sp_verify_database_ledger` と `sp_verify_database_ledger_from_digest_storage` の戻りコードは、`0` (成功) または `1` (失敗) です。

## <a name="next-steps"></a>次の手順

- [Azure SQL Database 台帳の概要](ledger-overview.md)
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)   
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)   
- [データベース台帳](ledger-database-ledger.md)