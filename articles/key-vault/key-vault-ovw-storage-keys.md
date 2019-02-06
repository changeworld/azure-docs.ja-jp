---
ms.assetid: ''
title: Azure Key Vault の管理対象ストレージ アカウント - CLI
description: ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 0392d84efa3a82a6323d6d09db792df7d6c42256
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210677"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault の管理対象ストレージ アカウント - CLI

> [!NOTE]
> [現在、Azure ストレージと Azure Active Directory (Azure AD) の統合はプレビュー段階です](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 認証と承認には、Azure Key Vault と同様に、Azure ストレージへの OAuth2 トークンベースのアクセスを提供する Azure AD を使用することをお勧めします。 こうすることで以下の操作が可能になります。
> - ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証する。 
> - Azure 上で実行する場合に [Azure AD マネージド ID](/azure/active-directory/managed-identities-azure-resources/) を使用する。 マネージド ID を使用すると、クライアント認証を併用し、アプリケーションに資格情報を保存する必要がなくなります。
> - 承認の管理にロール ベースのアクセス制御 (RBAC) を使用する (これも Key Vault でサポートされています)。

- Azure Key Vault は、Azure Storage アカウント (ASA) のキーを管理します。
    - 内部的には、Azure Key Vault では、Azure Storage アカウントを使用してキーの一覧表示 (同期) ができます。    
    - Azure Key Vault は定期的にキーを再生成 (回転) します。
    - キーの値は、呼び出し元に応答で返されることはありません。
    - Azure Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。
    
> [!IMPORTANT]
> Azure AD テナントからは、登録された各アプリケーションに**[サービス プリンシパル](/azure/active-directory/develop/developer-glossary#service-principal-object)** が提供されます。これはアプリケーションの ID として機能します。 このサービス プリンシパルのアプリケーション ID は、ロールベースのアクセス制御 (RBAC) を介して、他の Azure リソースにアクセスするための承認を与えるときに使用されます。 Key Vault は Microsoft アプリケーションなので、各 Azure クラウド内のすべての Azure AD テナントでは、次のように同じアプリケーション ID で事前登録されています。
> - Azure Government の場合、Azure AD テナントにはアプリケーション ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c` が使用される可能性があります。
> - Azure パブリック クラウドとその他すべてのクラウドの場合、Azure AD テナントにはアプリケーション ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` が使用されます。

<a name="prerequisites"></a>前提条件
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI をインストールする   
2. [ストレージ アカウントを作成する](https://azure.microsoft.com/services/storage/)
    - この[ドキュメント](https://docs.microsoft.com/azure/storage/)に載っている手順に従って、ストレージ アカウントを作成してください。  
    - **名前付けのガイダンス:** ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。        
      
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
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>SAS トークンを使用してストレージ アカウントにアクセスする方法

このセクションでは、Key Vault から [SAS トークン](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)をフェッチすることによって、ストレージ アカウントに対する操作を実行する方法を説明します

以下のセクションでは、Key Vault に格納されているストレージ アカウント キーをフェッチし、それを使用してストレージ アカウントの SAS (Shared Access Signature) 定義を作成する方法を示します。

> [!NOTE] 
  [基本的な概念](key-vault-whatis.md#basic-concepts)に記載されているように、次の 3 つの方法で Key Vault に対する認証ができます
- マネージド サービス ID の使用 (強くお勧めします)
- サービス プリンシパルと証明書の使用 
- サービス プリンシパルとパスワードの使用 (推奨されません)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

SAS トークンの期限がまもなく切れる場合は、SAS トークンを再度 Key Vault からをフェッチし、コードを更新してください

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>関連する Azure CLI コマンドレット
[Azure CLI ストレージ コマンドレット](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

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
