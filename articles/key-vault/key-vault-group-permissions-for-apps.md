---
title: さまざまなアプリケーションに Azure のキー コンテナーへのアクセス許可を付与する | Microsoft Docs
description: さまざまなアプリケーションにキー コンテナーへのアクセス許可を付与する方法を説明します。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14e6e8bb723eb236f8fb315454b8697a3bd947ef
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286429"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>さまざまなアプリケーションにキー コンテナーへのアクセス許可を付与する

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>Q: キー コンテナーにアクセスする必要があるいくつかのアプリケーションがあります。これらのアプリケーション (最大 1024) が Key Vault でアクセスできるようにするにはどうすればよいでしょうか。

Key Vault のアクセス制御ポリシーは、最大 1024 個のエントリをサポートします。 ただし、Azure Active Directory セキュリティ グループを作成することができます。 関連するすべてのサービス プリンシパルをこのセキュリティ グループに追加し、そのセキュリティ グループに Key Vault へのアクセスを許可してください。

前提条件は次のとおりです。
* [Azure Active Directory V2 PowerShell モジュールのインストール](https://www.powershellgallery.com/packages/AzureAD)。
* [Azure PowerShell のインストール](/powershell/azure/overview)。
* 次のコマンドを実行するには、Azure Active Directory テナント内でグループを作成または編集するためのアクセス許可が必要です。 アクセス許可を持っていない場合は、Azure Active Directory 管理者に連絡する必要があります。

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

アプリケーションのグループに異なるアクセス許可セットを付与する必要がある場合は、そのようなアプリケーション用に別の Azure Active Directory セキュリティ グループを作成します。

## <a name="next-steps"></a>次の手順

[キー コンテナーをセキュリティ保護する](key-vault-secure-your-key-vault.md)方法について確認します。
