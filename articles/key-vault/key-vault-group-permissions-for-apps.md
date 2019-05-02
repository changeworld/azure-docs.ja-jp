---
title: さまざまなアプリケーションに Azure のキー コンテナーへのアクセス許可を付与する - Azure Key Vault | Microsoft Docs
description: さまざまなアプリケーションにキー コンテナーへのアクセス許可を付与する方法を説明します。
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 39624595b4dd4e72ab1d34082a6ba44a1b3636d0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721982"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>複数のアプリケーションにキー コンテナーへのアクセスを許可する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

アクセス制御ポリシーを使用して、複数のアプリケーションにキー コンテナーへのアクセスを許可できます。 アクセス制御ポリシーは、最大 1024 個のアプリケーションをサポートでき、次のように構成します。

1. Azure Active Directory セキュリティ グループを作成します。 
2. アプリケーションに関連付けられているすべてのサービス プリンシパルをセキュリティ グループに追加します。
3. キー コンテナーへのアクセスをセキュリティ グループに許可します。

## <a name="prerequisites"></a>前提条件

前提条件は次のとおりです。
* [Azure PowerShell をインストールします](/powershell/azure/overview)。
* [Azure Active Directory V2 PowerShell モジュール](https://www.powershellgallery.com/packages/AzureAD)をインストールします。
* Azure Active Directory テナントでグループを作成/編集するためのアクセス許可。 アクセス許可を持っていない場合は、Azure Active Directory 管理者に連絡する必要があります。 Key Vault のアクセス ポリシーのアクセス許可について詳しくは、[Azure Key Vault のキー、シークレット、証明書](about-keys-secrets-and-certificates.md)に関するページをご覧ください。

## <a name="granting-key-vault-access-to-applications"></a>Key Vault のアクセス権をアプリケーションに付与する

PowerShell で次のコマンドを実行します。

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

アプリケーションのグループに異なるアクセス許可セットを付与する必要がある場合は、そのようなアプリケーション用に別の Azure Active Directory セキュリティ グループを作成します。

## <a name="next-steps"></a>次の手順

[キー コンテナーをセキュリティ保護する](key-vault-secure-your-key-vault.md)方法について確認します。
