---
title: Azure Stack ユーザー サブスクリプションの所有者の更新 | Microsoft Docs
description: Azure Stack ユーザー サブスクリプションの課金の所有者を変更します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: 46dd9c786fddb2f6ce9fb8a761a1b9f135a82d74
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959198"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Azure Stack ユーザー サブスクリプションの所有者の変更

Azure Stack オペレーターは、PowerShell を使用して、ユーザー サブスクリプションの課金の所有者を変更することができます。 所有者を変更する理由の 1 つに、組織を離れるユーザーの後任を指定することなどがあります。   

サブスクリプションに割り当てられる "*所有者*" には、次の 2 種類があります。

- **課金の所有者**: 既定では、課金の所有者は、オファーからサブスクリプションを取得し、そのサブスクリプションの請求関係を所有するユーザー アカウントです。 このアカウントはサブスクリプションの管理者でもあります。 サブスクリプションでこの所有者に指定できるユーザー アカウントは 1 つだけです。 課金の所有者は、多くの場合、組織またはチームのリーダーです。 

  課金の所有者を変更するには、PowerShell コマンドレット [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) を使用します。  

- **RBAC ロールを介して追加された所有者** - [ロールベースのアクセス制御](azure-stack-manage-permissions.md) (RBAC) システムを使用して、追加のユーザーに**所有者**ロールを付与できます。 課金の所有者を補完するために、任意の数の追加ユーザー アカウントを所有者として追加できます。 追加の所有者は、サブスクリプションの管理者でもあり、課金の所有者を削除するアクセス許可を除き、サブスクリプションのすべての特権を保有します。 

  追加の所有者は PowerShell を使用して管理できます。[Azure PowerShell を使用したロールベースのアクセス制御の管理](/azure/role-based-access-control/role-assignments-powershell)に関するページを参照してください。

## <a name="change-the-billing-owner"></a>課金の所有者の変更

ユーザー サブスクリプションの課金の所有者を変更するには、次のスクリプトを実行します。 スクリプトを実行するために使用するコンピューターでは、Azure Stack に接続し、Azure Stack PowerShell モジュール 1.3.0 以降を実行する必要があります。 詳細については、[Azure Stack PowerShell のインストール](azure-stack-powershell-install.md)に関するページを参照してください。 

> [!Note]
>  マルチテナントの Azure Stack では、新しい所有者を既存の所有者と同じディレクトリに配置する必要があります。 別のディレクトリに配置されているユーザーにサブスクリプションの所有権を与えるには、最初に[そのユーザーをゲストとして自分のディレクトリに招待する](../active-directory/b2b/add-users-administrator.md)必要があります。 

スクリプトを実行する前に、次の値を置き換えてください。 
 
- **$ArmEndpoint**: ご使用の環境用の Resource Manager エンドポイントを指定します。  
- **$TenantId**: テナント ID を指定します。 
- **$SubscriptionId**: サブスクリプション ID を指定します。
- **$OwnerUpn**: 新しい課金の所有者として追加するアカウントを **user@example.com** として指定します。  

```PowerShell   
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>次の手順

[ロールベースのアクセス制御の管理](azure-stack-manage-permissions.md)
