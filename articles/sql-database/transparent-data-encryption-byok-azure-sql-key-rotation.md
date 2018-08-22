---
title: PowerShell - TDE 保護機能のローテーション - Azure SQL Database | Microsoft Docs
description: Azure SQL サーバーの Transparent Data Encryption (TDE) 保護機能をローテーションする方法について説明します。
services: sql-database
keywords: ''
documentationcenter: ''
author: becczhang
manager: jhubbard
editor: ''
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ryzhang26
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 53ec5a315245537957c7c9eecde862da724b12f1
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043538"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする 

この記事では、Azure Key Vault から TDE 保護機能を使用して Azure SQL サーバーのキーをローテーションする方法について説明します。 Azure SQL サーバーの TDE 保護機能をローテーションすることは、サーバー上のデータベースを保護する新しい非対称キーに切り替えることを意味します。 キー ローテーションはオンラインで行われ、データベース全体ではなくデータべースのデータ暗号化キーを復号化して再暗号化するのみであるため、完了までに数秒しかかかりません。

このガイドでは、サーバーの TDE 保護機能をローテーションする 2 つのオプションについて説明します。

> [!NOTE]
> キー ローテーションを行う前に、停止された SQL Data Warehouse を再開する必要があります。
>

> [!IMPORTANT]
> ロールオーバー後に以前のバージョンのキーは**削除しないでください**。  キーがロール オーバーされると、古いデータベース バックアップなど、一部のデータは引き続き以前のキーで暗号化されます。 
>

## <a name="prerequisites"></a>前提条件

- このハウツー ガイドは、Azure SQL Database または Data Warehouse の TDE 保護機能として、Azure Key Vault のキーを既に使用していることを前提としています。 [BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。
- Azure PowerShell バージョン 3.7.0 以降がインストールされ、実行されている必要があります。 
- [推奨ただし省略可能] まず TDE 保護機能のキー素材をハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアで作成し、そのキー素材を Azure Key Vault にインポートします。 詳しくは、[ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)をご覧ください。

## <a name="option-1-auto-rotation"></a>オプション 1: 自動ローテーション

Key Vault の同じキー名とキー コンテナーの下に、既存 TDE 保護機能のキーの新しいバージョンを生成します。 Azure SQL サービスは 24 時間以内のこの新しいバージョンの使用を開始します。 

[Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) コマンドレットを使用して TDE 保護機能の新しいバージョンを作成するには:

   ```powershell
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>
   ```

## <a name="option-2-manual-rotation"></a>オプション 2: 手動ローテーション

このオプションでは、[Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) コマンドレット、[Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) コマンドレット、および [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用して、まったく新しいキーを追加します。これは新しいキー名の下または別のキー コンテナーに追加できます。 

>[!NOTE]
>キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>その他の便利な PowerShell コマンドレット

- TDE 保護機能を Microsoft マネージドから BYOK モードに切り替えるには、[Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用します。

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 保護機能を BYOK モードから Microsoft マネージドに切り替えるには、[Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用します。

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>次の手順

- セキュリティ リスクが発生した場合に備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する: [侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- TDE の Bring Your Own Key サポートを使用する: [PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)
