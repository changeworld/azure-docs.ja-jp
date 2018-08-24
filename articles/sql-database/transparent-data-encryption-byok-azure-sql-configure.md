---
title: 'PowerShell と CLI: SQL TDE の有効化 - 独自のキー - Azure SQL Database | Microsoft Docs'
description: PowerShell または CLI を使用して、保存時の暗号化に Transparent Data Encryption (TDE) を使用するように Azure SQL Database と Data Warehouse を構成する方法について説明します。
services: sql-database
keywords: ''
documentationcenter: ''
author: aliceku
manager: craigg
editor: ''
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.devlang: azurecli, powershell
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0dd0d322bacd807dc0cc59aeeb854c427a745bca
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043568"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell と CLI: Azure Key Vault の独自のキーを使用して Transparent Data Encryption を有効にする

この記事では、SQL Database または Data Warehouse で、Transparent Data Encryption (TDE) に Azure Key Vault のキーを使用する方法について説明します。 Bring Your Own Key (BYOK) をサポートする TDE の詳細については、[Azure SQL の TDE Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。 

## <a name="prerequisites-for-powershell"></a>PowerShell の前提条件

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- [推奨されますが、必須ではありません] TDE 保護機能のキー マテリアルのローカル コピーを作成するためのハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアを用意します。
- Azure PowerShell バージョン 4.2.0 以降がインストールされ、実行されている必要があります。 
- TDE に使用する Azure Key Vault とキーを作成します。
   - [PowerShell を使用した Key Vault の操作](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)
   - [ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - TDE に使用するには、キー コンテナーに次のプロパティが必要です。
   - [論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
   - [PowerShell で Key Vault の論理的な削除を使用する方法](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) 
- TDE に使用するには、キーに次の属性が必要です。
   - 有効期限がない
   - 無効化されていない
   - "*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行できる

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>手順 1. Azure AD ID をサーバーに割り当てる 

既存のサーバーがある場合は、次のコマンドを使用して Azure AD ID をサーバーに追加します。

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

サーバーを作成する場合は、サーバーの作成時に、-Identity タグを指定した [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) コマンドレットを使用して、Azure AD ID を追加します。

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>手順 2. Key Vault アクセス許可をサーバーに付与する

キー コンテナーのキーを TDE に使用する前に、[Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) コマンドレットを使用して、キー コンテナーへのアクセス権をサーバーに付与します。

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>手順 3. Key Vault キーをサーバーに追加し、TDE 保護機能を設定する

- [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) コマンドレットを使用して、Key Vault のキーをサーバーに追加します。
- [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用して、キーをすべてのサーバー リソースの TDE 保護機能として設定します。
- [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) コマンドレットを使用して、TDE 保護機能が意図したとおりに構成されていることを確認します。

> [!Note]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。
> 

>[!Tip]
>Key Vault の KeyId の例: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>手順 4. TDE を有効にする 

[Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) コマンドレットを使用して、TDE を有効にします。

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

データベースまたはデータ ウェアハウスで、Key Vault の暗号化キーを使用して TDE が有効になりました。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>手順 5. 暗号化の状態と暗号化アクティビティを確認する

[Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) を使用して暗号化の状態を取得し、[Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) を使用して、データベースまたはデータ ウェアハウスの暗号化の進行状況を確認します。

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>その他の便利な PowerShell コマンドレット

- TDE を無効にするには、[Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) コマンドレットを使用します。

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- サーバーに追加された Key Vault キーのリストを取得するには、[Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) コマンドレットを使用します。

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- サーバーから Key Vault キーを削除するには、[Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) を使用します。

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>トラブルシューティング

問題が発生した場合は、以下を確認してください。
- キー コンテナーが見つからない場合は、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) コマンドレットを使用して、適切なサブスクリプションを使用していることを確認します。

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- 新しいキーをサーバーに追加できない場合や、新しいキーを TDE 保護機能として更新できない場合は、以下を確認します。
   - キーに有効期限がないこと。
   - キーで、"*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作が有効になっている必要があります。

## <a name="next-steps"></a>次の手順

- セキュリティ要件に準拠するためにサーバーの TDE 保護機能を交換する方法を確認する: [PowerShell を使用して Transparent Data Encryption 保護機能を交換する](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- セキュリティ リスクが発生した場合に備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する: [侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

## <a name="prerequisites-for-cli"></a>CLI の前提条件

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- [推奨されますが、必須ではありません] TDE 保護機能のキー マテリアルのローカル コピーを作成するためのハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアを用意します。
- コマンドライン インターフェイス バージョン 2.0 以降。 最新バージョンをインストールして Azure サブスクリプションに接続するには、「[Azure クロスプラットフォーム コマンド ライン インターフェイス 2.0 のインストールと構成](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。 
- TDE に使用する Azure Key Vault とキーを作成します。
   - [CLI 2.0 を使用した Key Vault の管理](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2)
   - [ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - TDE に使用するには、キー コンテナーに次のプロパティが必要です。
   - [論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
   - [CLI で Key Vault の論理的な削除を使用する方法](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli) 
- TDE に使用するには、キーに次の属性が必要です。
   - 有効期限がない
   - 無効化されていない
   - "*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行できる
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>手順 1. サーバーを作成し、Azure AD ID をサーバーに割り当てる
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>手順 2. Key Vault アクセス許可をサーバーに付与する
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>手順 3. Key Vault キーをサーバーに追加し、TDE 保護機能を設定する
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。
> 

>[!Tip]
>Key Vault の KeyId の例: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>手順 4. TDE を有効にする 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

データベースまたはデータ ウェアハウスで、Key Vault の暗号化キーを使用して TDE が有効になりました。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>手順 5. 暗号化の状態と暗号化アクティビティを確認する

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>SQL CLI リファレンス

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

