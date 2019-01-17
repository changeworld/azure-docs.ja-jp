---
title: PowerShell を使用した Azure Stack での Key Vault の管理 | Microsoft Docs
description: PowerShell を使用して Azure Stack で Key Vault を管理する方法を説明します
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: db68d3ac626d80361e456a251b93d847a73afb8c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192648"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>PowerShell を使用した Azure Stack での Key Vault の管理

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

PowerShell を使用して Azure Stack で Key Vault を管理できます。 この記事では、Key Vault の PowerShell コマンドレットを使用して次のことを行う方法について説明します。

* Key Vault を作成します。
* 暗号化キーとシークレットを格納および管理する。
* コンテナーで操作を呼び出すことができるように、ユーザーまたはアプリケーションを承認する。

>[!NOTE]
>この記事で説明する Key Vault の PowerShell コマンドレットは、Azure PowerShell SDK で提供されます。

## <a name="prerequisites"></a>前提条件

* Azure Key Vault サービスを含むプランをサブスクライブする必要があります。
* [PowerShell for Azure Stack をインストールします](azure-stack-powershell-install.md)。
* [Azure Stack ユーザーの PowerShell 環境を構成します](azure-stack-powershell-configure-user.md)。

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Key Vault 操作のためのテナント サブスクリプションの有効化

キー コンテナーに対してなんらかの操作を実行するには、テナント サブスクリプションでコンテナー操作が有効になっていることを確認する必要があります。 コンテナー操作が有効になっていることを確認するには、次のコマンドを実行します。

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Output**

サブスクリプションでコンテナー操作が有効になっている場合は、出力のキー コンテナーのすべてのリソースの種類で **RegistrationState** が **Registered** と表示されます。

![キー コンテナーの登録の状態](media/azure-stack-key-vault-manage-powershell/image1.png)

コンテナー操作が有効になっていない場合は、次のコマンドを実行して、サブスクリプションで Key Vault サービスを登録します。

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Output**

登録に成功した場合は、次の出力が返されます。

![Register](media/azure-stack-key-vault-manage-powershell/image2.png)

Key Vault コマンドを呼び出すと、"サブスクリプションが名前空間 'Microsoft.KeyVault' を使用するように登録されていません" などのエラーが表示されることがあります。エラーが表示された場合は、前に指定された手順に従うことによって、[Key Vault リソース プロバイダーを有効にした](#enable-your-tenant-subscription-for-vault-operations)ことを確認してください。

## <a name="create-a-key-vault"></a>Key Vault を作成します

キー コンテナーを作成する前に、リソース グループを作成し、このリソース グループに、キー コンテナー関連のすべてのリソースが存在するようにしておきます。 次のコマンドを使用して、新しいリソース グループを作成します。

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

**Output**

![新しいリソース グループ](media/azure-stack-key-vault-manage-powershell/image3.png)

次に、**New-AzureRMKeyVault** コマンドレットを使用して、前に作成したリソース グループ内にキー コンテナーを作成します。 このコマンドは、3 つの必須パラメーター (リソース グループ名、キー コンテナー名、地理的な場所) を読み取ります。

次のコマンドを実行して、キー コンテナーを作成します。

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Output**

![新しいキー コンテナー](media/azure-stack-key-vault-manage-powershell/image4.png)

このコマンドの出力は、作成したキー コンテナーのプロパティを示します。 アプリケーションがこのコンテナーにアクセスする場合は、`Vault URI` プロパティ (この例では `https://vault01.vault.local.azurestack.external`) を使用する必要があります。

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Active Directory フェデレーション サービス (AD FS) のデプロイ

AD FS のデプロイでは、"アクセス ポリシーが設定されていません。 ユーザーまたはアプリケーションに、このコンテナーを使用するアクセス許可がありません" という警告が表示される場合があります。 この問題を解決するには、[Set-AzureRmKeyVaultAccessPolicy](azure-stack-key-vault-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) コマンドレットを使用して、コンテナーのアクセス ポリシーを設定します。

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>キーとシークレットの管理

キー コンテナーを作成したら、次の手順を使用して、コンテナーでキーとシークレットを作成および管理します。

### <a name="create-a-key"></a>キーの作成

**Add-AzureKeyVaultKey** コマンドレットを使用して、キー コンテナーにソフトウェアで保護されたキーを作成またはインポートします。

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

`Destination` パラメーターは、そのキーがソフトウェアで保護されることを指定するために使用されます。 キーが正常に作成されると、新しく作成されたキーの詳細が出力されます。

**Output**

![新しいキー](media/azure-stack-key-vault-manage-powershell/image5.png)

これで、新しく作成されたキーの URI を使用してそのキーを参照できます。 既存のキーと同じ名前のキーを作成またはインポートした場合、元のキーは、新しいキーで指定される値で更新されます。 前のバージョンにアクセスするには、キーのバージョン固有の URI を使用します。 例: 

* `https://vault10.vault.local.azurestack.external:443/keys/key01` を使用して、常に現在のバージョンを取得します。
* `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` を使用して、この特定のバージョンを取得します。

### <a name="get-a-key"></a>キーの取得

**Get-AzureKeyVaultKey** コマンドレットを使用して、キーとその詳細を読み取ります。

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>シークレットの作成

**Set-AzureKeyVaultSecret** コマンドレットを使用して、コンテナー内のシークレットを作成または更新します。 シークレットがまだ存在しない場合は、作成されます。 既に存在する場合は、新しいバージョンのシークレットが作成されます。

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Output**

![シークレットの作成](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>シークレットの取得

**Get-AzureKeyVaultSecret** コマンドレットを使用して、キー コンテナー内のシークレットを読み取ります。 このコマンドは、シークレットのすべてのバージョンまたは特定のバージョンを返すことができます。

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

キーとシークレットを作成したら、そのキーとシークレットを外部アプリケーションが使用することを承認できます。

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>アプリケーションがキーまたはシークレットを使用することの承認

**Set-AzureRmKeyVaultAccessPolicy** コマンドレットを使用して、アプリケーションがキー コンテナー内のキーまたはシークレットにアクセスすることを承認します。 次の例では、コンテナー名は `ContosoKeyVault` であり、承認するアプリケーションのクライアント ID は `8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed` です。 アプリケーションを承認するには、次のコマンドを実行します。 オプションで、ユーザー、アプリケーション、またはセキュリティ グループのアクセス許可を設定するための `PermissionsToKeys` パラメーターを指定できます。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

その同じアプリケーションがコンテナーのシークレットを読み取ることを承認する場合は、次のコマンドレットを実行します。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>次の手順

* [Key Vault に格納されているパスワードを使用して VM をデプロイする](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Key Vault に格納されている証明書を使用して VM をデプロイする](azure-stack-key-vault-push-secret-into-vm.md)
