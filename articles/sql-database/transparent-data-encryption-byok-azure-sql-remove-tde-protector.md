---
title: PowerShell - TDE 保護機能の削除 - Azure SQL Database| Microsoft Docs
description: Bring Your Own Key (BYOK) をサポートする TDE を使用している Azure SQL Database または Data Warehouse の、侵害された可能性のある TDE 保護機能に対応するためのハウツー ガイド。
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043551"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell を使用した Transparent Data Encryption (TDE) 保護機能の削除
## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- Azure PowerShell バージョン 4.2.0 以降がインストールされ、実行されている必要があります。 
- このハウツー ガイドは、Azure SQL Database または Data Warehouse の TDE 保護機能として、Azure Key Vault のキーを既に使用していることを前提としています。 詳細については、[BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。

## <a name="overview"></a>概要
このハウツー ガイドでは、Bring Your Own Key (BYOK) サポートする TDE を使用している Azure SQL Database または Data Warehouse の、侵害された可能性がある TDE 保護機能に対応する方法について説明します。 TDE の BYOK サポートの詳細については、[概要ページ](transparent-data-encryption-byok-azure-sql.md)をご覧ください。 

以下の手順は、極端な状況またはテスト環境でのみ実行する必要があります。 アクティブに使用されている TDE 保護機能を Azure Key Vault から削除すると、**データが失われる**可能性があるため、このハウツー ガイドを入念に確認してください。 

サービスまたはユーザーがキーに不正アクセスしているなど、キーが侵害された疑いがある場合は、キーを削除するのが最善です。

Key Vault の TDE 保護機能を削除すると、**サーバーにある暗号化されたデータベースへのすべての接続がブロックされ、これらのデータベースはオフラインになり、24 時間以内に削除される**ことに注意してください。 侵害されたキーで暗号化された古いバックアップにはアクセスできなくなります。

このハウツー ガイドでは、インシデント対応後の望ましい結果に応じた次の 2 つの方法を説明します。
- Azure SQL Databases/Data Warehouses を引き続き**アクセス可能**にしておく
- Azure SQL Databases/Data Warehouses を**アクセス不可**にする

## <a name="to-keep-the-encrypted-resources-accessible"></a>暗号化されたリソースを引き続きアクセス可能にしておくには
1. [Key Vault に新しいキー](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0)を作成します。 アクセス制御はコンテナー レベルでプロビジョニングされるため、この新しいキーは、侵害された可能性のある TDE 保護機能とは別のキー コンテナーに作成する必要があります。 
2. [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) および [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用してサーバーに新しいキーを追加し、サーバーの新しい TDE 保護機能として更新します。

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用して、サーバーとすべてのレプリカが新しい TDE 保護機能に更新されたことを確認します。 

   >[!NOTE]
   > サーバーにあるすべてのデータベースとセカンダリ データベースに新しい TDE 保護機能が伝達されるまでに数分かかることがあります。
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault 内の[新しいキーのバックアップ](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)を作成します。

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) コマンドレットを使用して、侵害されたキーを Key Vault から削除します。 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. 今後、Key Vault にキーを復元するときは、[Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) コマンドレットを使用します。
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>暗号化されたリソースをアクセス不可にするには
1. 侵害された可能性のあるキーによって暗号化されているデータベースを削除します。
データベースとログ ファイルは自動的にバックアップされるので、(キーを提供すれば) データベースのポイントインタイム リストアをいつでも実行できます。 最新のトランザクションの最大 10 分間のデータが失われる可能性を防ぐために、アクティブな TDE 保護機能を削除する前にデータベースを削除する必要があります。 
2. Key Vault 内の TDE 保護機能のキー マテリアルをバックアップします。
3. 侵害された可能性のあるキーを Key Vault から削除します。

## <a name="next-steps"></a>次の手順

- セキュリティ要件に準拠するためにサーバーの TDE 保護機能を交換する方法を確認する: [PowerShell を使用して Transparent Data Encryption 保護機能を交換する](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- TDE の Bring Your Own Key サポートを使用する: [PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)
