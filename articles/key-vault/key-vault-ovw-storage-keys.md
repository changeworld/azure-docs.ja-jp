---
ms.assetid: ''
title: Azure Key Vault ストレージ アカウント キー
description: ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: a3f8d540c7e4c8a86b151540980724777fd150fd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault ストレージ アカウント キー

Azure Key Vault ストレージ アカウント キーがなかったときは、開発者は自分たちの Azure Storage アカウント (ASA) キーを管理し、手動または外部オートメーションによって回す必要がありました。 現在は、Key Vault ストレージ アカウント キーは [Key Vault シークレット](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)として実装され、Azure Storage アカウントで認証するようになりました。

Azure Storage アカウント (ASA) の主要機能により、シークレットのローテーションが管理されます。 また、Shared Access Signature (SAS) をメソッドとして提供することにより、ASA キーを直接扱う必要がなくなりました。

Azure Storage アカウントの概要情報については、「[Azure Storage アカウントについて](https://docs.microsoft.com/azure/storage/storage-create-storage-account)」をご覧ください。

## <a name="supporting-interfaces"></a>インターフェイスのサポート

プログラミングとスクリプト インターフェイスの詳細な一覧およびリンクについては、「[Key Vault Developer's Guide](key-vault-developers-guide.md#coding-with-key-vault)」(Key Vault 開発者ガイド) でご確認ください。


## <a name="what-key-vault-manages"></a>Key Vault による管理

Key Vault では、管理対象ストレージ アカウント キーを使用するときに、いくつかの内部管理機能を自動的に実行します。

- Azure Key Vault は、Azure Storage アカウント (ASA) のキーを管理します。
    - 内部的には、Azure Key Vault では、Azure Storage アカウントを使用してキーの一覧表示 (同期) ができます。
    - Azure Key Vault は定期的にキーを再生成 (回転) します。
    - キーの値は、呼び出し元に応答で返されることはありません。
    - Azure Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。
- Azure Key Vault では、コンテナーやオブジェクトの所有者は SAS (アカウントまたはサービス SAS) 定義を作成できます。
    - SAS 値は SAS 定義を使用して作成され、REST URI パスを経由してシークレットとして返されます。 詳しくは、「[Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)」(Azure Key Vault ストレージ アカウントの操作) をご覧ください。

## <a name="naming-guidance"></a>名前付けのガイダンス

- ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。
- SAS 定義名は長さが 1 文字から 102 文字までで、0 ～ 9、a ～ z、A ～ Z のみを含む必要があります。

## <a name="developer-experience"></a>開発者エクスペリエンス

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault ストレージ キー以前

開発者は、Azure storage にアクセスにするために、ストレージ アカウント キーで以下のことを行う必要がありました。
1. Azure AppService アプリケーション設定または別のストレージに接続文字列または SAS トークンを保存します。
1. アプリケーションの起動時に、接続文字列または SAS トークンを取得します。
1. ストレージと対話する [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) を作成します。

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault ストレージ キー以後

開発者は [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) を作成し、それを利用してストレージの SAS トークンを取得します。 その後、そのトークンを使用して [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) を作成します。

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>開発者ガイダンス

- ASA キーの管理は、Key Vault のみを許可します。 Key Vault のプロセスと干渉するため、自身で管理しないでください。
- 複数の Key Vault オブジェクトによって ASA キーを管理しないでください。
- ASA キーを手動で再生成する必要がある場合は、Key Vault を使用して ASA キーを再生成することをお勧めします。

## <a name="getting-started"></a>使用の開始

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>ロール ベースのアクセス制御 (RBAC) の権限設定

Azure Key Vault アプリケーション ID では、ストレージ アカウントのキーを*一覧表示*し*再生成*する権限が必要です。 次の手順に従ってこれらのアクセス許可をセットアップします。

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>実際の例

次の例では、Key Vault が管理する Azure Storage アカウントと、関連した Shared Access Signature (SAS) 定義を作成する方法を示します。

### <a name="prerequisite"></a>前提条件

[ロールベースのアクセス制御 (RBAC) のアクセス許可の設定](#setup-for-role-based-access-control-rbac-permissions)が完了していることを確認します。

### <a name="setup"></a>セットアップ

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

次に、ストレージのすべてのアクセス許可を Key Vault で管理できるように、**アカウント**のアクセス許可を設定します。 次の例では、Azure アカウントは _developer@contoso.com_ です。

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault の管理対象ストレージ アカウントの作成

次に、Azure Key Vault で管理対象ストレージ アカウントを作成し、ストレージ アカウントのアクセス キーを使用して SAS トークンを作成します。
- `-ActiveKeyName` では、"key2" を使用して SAS トークンを生成します。
- `-AccountName` は、管理対象ストレージ アカウントの識別に使用されます。 次の例では、簡潔にするためにストレージ アカウント名を使用していますが、任意の名前を使用できます。
- `-DisableAutoRegenerateKey` は、ストレージ アカウント キーを再生成しないことを指定します。

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>キーの再生成

Key Vault でストレージのアクセス キーを定期的に再生成する場合は、再生成期間を設定できます。 次の例では、再生成期間を 3 日に設定しています。 3 日後、Key Vault は "key1" を再生成し、アクティブ キーを "key2" から "key1" に切り替えます。

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>SAS 定義の設定

アカウント SAS は、さまざまなアクセス許可を持つ BLOB サービスへのアクセスを提供します。
Key Vault で、管理対象のストレージ アカウント用に SAS 定義を設定します。
- `-AccountName` は、Key Vault の管理対象ストレージ アカウントの名前です。
- `-Name` は、ストレージ内の SAS トークンの識別子です。
- `-ValidityPeriod` では、生成された SAS トークンの有効期限を設定します。

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>SAS トークンの取得

対応する SAS トークンを取得し、ストレージへの呼び出しを行います。 `-SecretName` は、[Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/en-us/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition) を実行したときの `AccountName` パラメーターと `Name` パラメーターの入力を使用して作成されます。

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>ストレージの作成

*$readSasToken* を使用してアクセスしようとすると失敗しますが、*$writeSasToken* を使用するとアクセスできることに注意してください。

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

書き込みアクセス権を持つ SAS トークンを使用して、ストレージ BLOB コンテンツにアクセスできます。

### <a name="relevant-powershell-cmdlets"></a>関連した PowerShell コマンドレット

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>ストレージ アカウントのオンボード

例: Key Vault オブジェクトの所有者は、ストレージ アカウントをオンボードするために、ストレージ アカウント オブジェクトを Azure Key Vault に追加します。

オンボード時に Key Vault は、オンボードするアカウントの ID にストレージ キーの*一覧表示*と*再生成*の権限があることを確認する必要があります。 これらの権限を確認するために、Key Vault は、認証サービスから OBO (On Behalf Of) トークンを取得し、対象ユーザーを Azure Resource Manager に設定し、Azure Storage サービスの*一覧表示*キーを呼び出します。 *一覧表示*の呼び出しが失敗すると、Key Vault オブジェクトの作成が*禁止*の HTTP 状態コードで失敗します。 この方法で表示されるキーは、キー コンテナー エンティティ ストレージでキャッシュされます。

Key Vault では、ID がキー再生成の所有権を取得する前に、その ID に*再生成*の権限があることを確認する必要があります。 この ID が、OBO トークン経由で、Key Vault ファースト パーティ ID と同様に権限があることを確認するために、以下のことを行います。

- Key Vault は、ストレージ アカウント リソースに対するRBAC 権限を一覧表示します。
- Key Vault は、アクションとアクション以外の正規表現の照合によって応答を検証します。

その他の補助的な例は、「[Key Vault に管理されているストレージ アカウント キーのサンプル](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167)」に関するページをご覧ください。

ID に*再生成*の権限がない場合、あるいは Key Vault のファースト パーティ ID に*一覧表示*または*再生成*の権限がない場合、オンボード要求は失敗し、適切なエラー コードとメッセージを返します。

OBO トークンは、PowerShell または CLI のいずれかのファースト パーティのネイティブ クライアント アプリケーションを使用する場合にのみ機能します。

## <a name="other-applications"></a>他のアプリケーション

- Key Vault ストレージ アカウント キーを使用して構築された SAS トークンは、Azure Storage アカウントへのさらに制御されたアクセスを提供します。 詳細については、「[Shared Access Signatures (SAS) の使用](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)」を参照してください。

## <a name="see-also"></a>関連項目

- [キー、シークレット、証明書について](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault チーム ブログ](https://blogs.technet.microsoft.com/kv/)
