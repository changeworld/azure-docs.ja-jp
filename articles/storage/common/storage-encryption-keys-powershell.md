---
title: PowerShell を使用してカスタマー マネージド キーを構成する
titleSuffix: Azure Storage
description: PowerShell を使用して Azure Storage 暗号化用にカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 77324dff7e3f34574f36aa3bb775aed6a945a3bd
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665278"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>PowerShell を使用して Azure Key Vault でカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、PowerShell を使用するカスタマー マネージド キーでAzure Key Vault を構成する方法を説明します。 Azure CLI を使用してキー コンテナーを作成する方法を学習するには、「[クイック スタート: PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/quick-create-powershell.md)」を参照してください。

## <a name="assign-an-identity-to-the-storage-account"></a>ストレージ アカウントに ID を割り当てる

お使いのストレージ アカウントでカスタマー マネージド キーを有効にするには、まず、そのストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をストレージ アカウントに付与します。

PowerShell を使用して、マネージド ID を割り当てるには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

PowerShell を使用してシステム割り当てマネージド ID を構成する方法の詳細については、「[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)」を参照してください。

## <a name="create-a-new-key-vault"></a>新しいキー コンテナーを作成する

PowerShell を使用して、新しいキー コンテナーを作成するには、[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) を呼び出します。 Azure Storage 暗号化用のカスタマー マネージド キーの格納に使用するキー コンテナーには、2 つのキー保護設定 ( **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** ) を有効にする必要があります。

角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

PowerShell を使用して既存のキー コンテナーで **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** を有効にする方法については、[PowerShell で論理的な削除を使用する方法](../../key-vault/key-vault-soft-delete-powershell.md)に関する記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

## <a name="configure-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを構成する

次に、キー コンテナーのアクセス ポリシーを構成し、ストレージ アカウントからアクセスできるようにします。 この手順では、以前にストレージ アカウントに割り当てたマネージド ID を使用します。

キー コンテナーのアクセス ポリシーを設定するには、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>新しいキーを作成する

次に、キー コンテナーに新しいキーを作成します。 新しいキーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) を呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を構成する

既定で Azure Storage の暗号化には、Microsoft マネージド キーを使用します。 この手順では、カスタマー マネージド キーを使用するように Azure Storage アカウントを構成し、そのストレージ アカウントに関連付けるキーを指定します。

ストレージ アカウントの暗号化設定を更新するには、次の例に示すように [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出します。 ストレージ アカウントのカスタマー マネージド キーを有効にするには、 **-KeyvaultEncryption** オプションを指定します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、ストレージ アカウントを更新する必要があります。 まず [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) を呼び出し、キーの最新バージョンを取得します。 次に、前のセクションで示したように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して、キーの新しいバージョンを使用するように、ストレージ アカウントの暗号化設定を更新します。

## <a name="use-a-different-key"></a>別のキーを使用する

Azure Storage の暗号化に使用するキーを変更するには、「[カスタマー マネージド キーによる暗号化を構成する](#configure-encryption-with-customer-managed-keys)」で示されているように [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して、新しいキーの名前とバージョンを指定します。 新しいキーが別のキー コンテナーにある場合は、キー コンテナー URI も更新します。

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、ストレージ アカウントはそれ以降、Microsoft が管理するキーを使用して暗号化されます。 カスタマー マネージド キーを無効にするには、次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して `-StorageEncryption` オプションを指定します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
