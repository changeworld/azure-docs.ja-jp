---
title: Azure SQL Database と Data Warehouse の Transparent Data Encryption | Microsoft Docs
description: SQL Database と Data Warehouse の Transparent Data Encryption の概要。 このドキュメントでは、Transparent Data Encryption の利点と構成オプション (サービスによって管理された Transparent Data Encryption や Bring Your Own Key など) について説明します。
services: sql-database
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0ed05fd2d55f1c4c80bec9f64925be2eddddc067
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043571"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>SQL Database と Data Warehouse の Transparent Data Encryption

Transparent Data Encryption (TDE) を使用すると、悪意のあるアクティビティの脅威から Azure SQL Database と Azure Data Warehouse を保護できます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 既定では、新しくデプロイされるすべての Azure SQL Database で TDE が有効になります。 TDE を使用して、SQL Database の論理 **master** データベースを暗号化することはできません。  **master** データベースには、ユーザー データベースで TDE 操作を実行するために必要なオブジェクトが含まれています。

古いデータベースや Azure SQL Data Warehouse の場合、TDE を手動で有効にする必要があります。  

Transparent Data Encryption は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 このデータベース暗号化キーは、Transparent Data Encryption 保護機能によって保護されます。 保護機能は、サービスによって管理された証明書 (サービスによって管理された Transparent Data Encryption) または Azure Key Vault に格納されている非対称キー (Bring Your Own Key) です。 Transparent Data Encryption 保護機能はサーバー レベルで設定します。 

データベースの起動時に、暗号化されたデータベース暗号化キーが暗号化解除され、SQL Server データベース エンジン プロセスでデータベース ファイルの暗号化解除と再暗号化に使用されます。 Transparent Data Encryption では、データのリアルタイムの I/O 暗号化と暗号化解除がページ レベルで実行されます。 各ページは、メモリに読み込まれるときに暗号化解除され、ディスクに書き込まれる前に暗号化されます。 Transparent Data Encryption の概要については、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) に関する記事をご覧ください。

Azure 仮想マシン上で実行されている SQL Server も、Key Vault の非対称キーを使用できます。 構成手順は、SQL Database で非対称キーを使用する場合とは異なります。 詳細については、「[Azure Key Vault を使用する拡張キー管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)」をご覧ください。

## <a name="service-managed-transparent-data-encryption"></a>サービスによって管理された Transparent Data Encryption

Azure での Transparent Data Encryption の既定の設定では、データベース暗号化キーは組み込みのサーバー証明書によって保護されます。 組み込みのサーバー証明書は、サーバーごとに一意です。 データベースが geo レプリケーションのリレーションシップに含まれている場合、プライマリ データベースと geo セカンダリ データベースの両方が、プライマリ データベースの親サーバー キーによって保護されます。 2 つのデータベースが同じサーバーに接続されている場合は、同じ組み込み証明書が共有されます。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。

また、geo レプリケーションと復元のために、必要に応じてキーの移動と管理をシームレスに行います。 

> [!IMPORTANT]
> 新しく作成されたすべての SQL データベースは、サービスによって管理された Transparent Data Encryption を使用して既定で暗号化されます。 2017 年 5 月より前の既存のデータベースと、復元、geo レプリケーション、データベースのコピーによって作成されたデータベースは、既定で暗号化されません。
>

## <a name="bring-your-own-key"></a>Bring Your Own Key

Bring Your Own Key のサポートにより、ユーザーは Transparent Data Encryption キーを管理し、誰がどのような場合にキーにアクセスできるかを制御できます。 Azure のクラウド ベースの外部キー管理システムである Key Vault は、Transparent Data Encryption が Bring Your Own Key サポートと統合された最初のキー管理サービスです。 Bring Your Own Key のサポートにより、データベース暗号化キーは Key Vault に格納されている非対称キーによって保護されます。 非対称キーが Key Vault の外部に移動されることはありません。 サーバーにキー コンテナーへのアクセス許可が付与されると、サーバーは Key Vault を介して基本的なキー操作要求を送信します。 非対称キーはサーバー レベルで設定し、そのサーバーにあるすべてのデータベースによって継承されます。

Bring Your Own Key のサポートにより、キーの交換や Key Vault アクセス許可などのキー管理タスクを制御できるようになりました。 また、キーを削除したり、すべての暗号化キーの監査/レポートを有効にしたりすることもできます。 Key Vault はキーの一元管理を提供し、厳しく監視されたハードウェア セキュリティ モジュールを使用します。 Key Vault は、キーとデータの管理の分離を促進することによって規制順守への対応を支援します。 Key Vault の詳細については、[Key Vault のドキュメント ページ](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)をご覧ください。

SQL Database と Data Warehouse の Bring Your Own Key をサポートする Transparent Data Encryption の詳細については、[Bring Your Own Key をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。

Bring Your Own Key をサポートする Transparent Data Encryption の使用を開始する場合は、[PowerShell で Key Vault の独自のキーを使用して Transparent Data Encryption を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)方法のガイドをご覧ください。

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transparent Data Encryption で保護されたデータベースを移動する

Azure 内での操作のためにデータベースを暗号化解除する必要はありません。 ソース データベースまたはプライマリ データベースの Transparent Data Encryption 設定は、ターゲットで透過的に継承されます。 含まれている操作は次のとおりです。
- geo リストア。
- セルフサービスのポイントインタイム リストア。
- 削除されたデータベースの復元。
- アクティブ geo レプリケーション。
- データベース コピーの作成。

Transparent Data Encryption で保護されたデータベースをエクスポートした場合、データベースのエクスポートされたコンテンツは暗号化されません。 このエクスポートされたコンテンツは、暗号化されていない BACPAC ファイルに保存されます。 BACPAC ファイルを適切に保護し、新しいデータベースのインポートが完了してから Transparent Data Encryption を有効にする必要があります。

たとえば、BACPAC ファイルをオンプレミスの SQL Server インスタンスからエクスポートした場合、新しいデータベースのインポートされるコンテンツは自動的には暗号化されません。 同様に、BACPAC ファイルをオンプレミスの SQL Server インスタンスにエクスポートした場合も、新しいデータベースは自動的には暗号化されません。

唯一の例外は、SQL データベースとの間でエクスポートを実行する場合です。 Transparent Data Encryption は新しいデータベースで有効になりますが、BACPAC ファイル自体は暗号化されません。

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Azure portal で Transparent Data Encryption を管理する

Azure portal を使用して Transparent Data Encryption を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ管理者として接続する必要があります。 

Transparent Data Encryption はデータベース レベルで設定します。 データベースで Transparent Data Encryption を有効にするには、[Azure portal](https://portal.azure.com) に移動し、Azure 管理者または共同作成者アカウントでサインインします。 ユーザー データベースの Transparent Data Encryption 設定を見つけます。 既定では、サービスによって管理された Transparent Data Encryption が使用されます。 Transparent Data Encryption 証明書は、データベースを含むサーバーに対して自動的に生成されます。 

![サービスによって管理された Transparent Data Encryption](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Transparent Data Encryption マスター キー (Transparent Data Encryption 保護機能とも呼ばれます) は、サーバー レベルで設定します。 Bring Your Own Key をサポートする Transparent Data Encryption を使用し、Key Vault のキーでデータベースを保護するには、サーバーの Transparent Data Encryption 設定を確認します。 

![Bring Your Own Key をサポートする Transparent Data Encryption](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>PowerShell を使用して Transparent Data Encryption を管理する

PowerShell を使用して Transparent Data Encryption を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ管理者として接続する必要があります。 

| コマンドレット | 説明 |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |データベースの Transparent Data Encryption を有効または無効にします。|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |データベースの Transparent Data Encryption の状態を取得します。 |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |データベースの暗号化の進行状況を確認します。 |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |SQL Server インスタンスに Key Vault キーを追加します。 |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |SQL Server インスタンスの Key Vault キーを取得します。  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |SQL Server インスタンスの Transparent Data Encryption 保護機能を設定します。 |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Transparent Data Encryption 保護機能を取得します。 |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |SQL Server インスタンスから Key Vault キーを削除します。 |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Transact-SQL を使用して Transparent Data Encryption を管理する

master データベースの **dbmanager** ロールの管理者またはメンバーであるログインを使用してデータベースに接続します。

| コマンド | 説明 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF によって、データベースを暗号化または暗号化解除します。 |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |データベースの暗号化の状態と、関連付けられているデータベース暗号化キーに関する情報を返します。 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |各データ ウェアハウス ノードの暗号化の状態と、関連付けられているデータベース暗号化キーに関する情報を返します。 | 
|  | |

Transact-SQL を使用して、Transparent Data Encryption 保護機能を Key Vault のキーに切り替えることはできません。 PowerShell または Azure portal を使用してください。

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>REST API を使用して Transparent Data Encryption を管理する
 
REST API を使用して Transparent Data Encryption を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ管理者として接続する必要があります。 

| コマンド | 説明 |
| --- | --- |
|[サーバーの作成または更新](/rest/api/sql/servers/createorupdate)|SQL Server インスタンスに Azure Active Directory ID を追加します (Key Vault へのアクセスを許可するために使用)。|
|[サーバー キーの作成または更新](/rest/api/sql/serverkeys/createorupdate)|SQL Server インスタンスに Key Vault キーを追加します。|
|[サーバー キーの削除](/rest/api/sql/serverkeys/delete)|SQL Server インスタンスから Key Vault キーを削除します。|
|[サーバー キーの取得](/rest/api/sql/serverkeys/get)|SQL Server インスタンスから特定の Key Vault キーを取得します。|
|[サーバーごとにサーバー キーのリストを取得](/rest/api/sql/serverkeys/listbyserver)|SQL Server インスタンスの Key Vault キーを取得します。 |
|[暗号化保護機能の作成または更新](/rest/api/sql/encryptionprotectors/createorupdate)|SQL Server インスタンスの Transparent Data Encryption 保護機能を設定します。|
|[暗号化保護機能の取得](/rest/api/sql/encryptionprotectors/get)|SQL Server インスタンスの Transparent Data Encryption 保護機能を取得します。|
|[サーバーごとに暗号化保護機能のリストを取得](/rest/api/sql/encryptionprotectors/listbyserver)|SQL Server インスタンスの Transparent Data Encryption 保護機能を取得します。 |
|[Transparent Data Encryption 構成の作成または更新](/rest/api/sql/transparentdataencryptions/createorupdate)|データベースの Transparent Data Encryption を有効または無効にします。|
|[Transparent Data Encryption 構成の取得](/rest/api/sql/transparentdataencryptions/get)|データベースの Transparent Data Encryption 構成を取得します。|
|[Transparent Data Encryption 構成の結果リストの取得](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|データベースの暗号化の結果を取得します。|

## <a name="next-steps"></a>次の手順

- Transparent Data Encryption の概要については、[Transparent Data Encryption]((https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption) に関する記事をご覧ください。
- SQL Database と Data Warehouse の Bring Your Own Key をサポートする Transparent Data Encryption の詳細については、[Bring Your Own Key をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。
- Bring Your Own Key をサポートする Transparent Data Encryption の使用を開始する場合は、[PowerShell で Key Vault の独自のキーを使用して Transparent Data Encryption を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)方法のガイドをご覧ください。
- Key Vault の詳細については、[Key Vault のドキュメント ページ](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)をご覧ください。
