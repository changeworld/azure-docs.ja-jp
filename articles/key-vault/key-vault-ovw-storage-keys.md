---
ms.assetid: ''
title: Azure Key Vault ストレージ アカウント キー
description: ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e62c599d82f488bf1fc30ce503c271084c5ae59d
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300225"
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault ストレージ アカウント キー

> [!NOTE]
> [Azure Storage で AAD 認証がサポートされました](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 Storage に対する認証と承認に Azure Active Directory を使用することをお勧めします。そうすれば、ユーザーはストレージ アカウント キーのローテーションについて心配する必要がありません。

- Azure Key Vault は、Azure Storage アカウント (ASA) のキーを管理します。
    - 内部的には、Azure Key Vault では、Azure Storage アカウントを使用してキーの一覧表示 (同期) ができます。    
    - Azure Key Vault は定期的にキーを再生成 (回転) します。
    - キーの値は、呼び出し元に応答で返されることはありません。
    - Azure Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。

<a name="prerequisites"></a>前提条件
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI をインストールする   
2. [ストレージ アカウントを作成する](https://azure.microsoft.com/services/storage/)
    - この[ドキュメント](https://docs.microsoft.com/azure/storage/)に載っている手順に従って、ストレージ アカウントを作成してください。  
    - **名前付けのガイダンス:** ストレージ アカウント名の長さは 3 文字から 24 文字で、数字と小文字のみを使用できます。        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Key Vault を使用してストレージ アカウント キーを管理する手順
--------------------------------------------------------------------------------
以下の手順では、ストレージ アカウントに対するオペレーター アクセス許可を持つサービスとして Key Vault を割り当てます。

> [!NOTE]
> いったん Azure Key Vault マネージド ストレージ アカウント キーを設定した後は、Key Vault を使用しないでキーを変更しては**ならない**ことに注意してください。 マネージド ストレージ アカウント キーとは、Key Vault によってストレージ アカウント キーのローテーションが管理されることを意味します

1. ストレージ アカウントの作成後に、次のコマンドを実行して、管理するストレージ アカウントのリソース ID を取得します。

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Azure Key Vault のサービス プリンシパルを表すアプリケーション ID を取得します。 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Azure Key Vault ID に Storage Key Operator ロールを割り当てます。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Key Vault の管理対象ストレージ アカウントを作成します。     <br /><br />
   次の例では、再生成期間を 90 日に設定しています。 90 日後、Key Vault は "key1" を再生成し、アクティブ キーを "key2" から "key1" に切り替えます。
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    ユーザーがストレージ アカウントを作成しておらず、ストレージ アカウントへのアクセス許可もない場合に備えて、以下の手順で自分のアカウントのアクセス許可を設定して、Key Vault 内のすべてのストレージ アクセス許可を確実に管理できるようにします。
 > [!NOTE] 
    ユーザーにストレージ アカウントに対するアクセス許可がない場合に備えて、最初にユーザーのオブジェクト ID を取得します。

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>関連する Azure CLI コマンドレット
- [Azure CLI ストレージ コマンドレット](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>関連した PowerShell コマンドレット

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>関連項目

- [キー、シークレット、証明書について](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault チーム ブログ](https://blogs.technet.microsoft.com/kv/)
