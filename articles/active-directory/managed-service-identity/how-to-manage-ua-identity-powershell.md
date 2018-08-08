---
title: Azure PowerShell を使用してユーザー割り当て MSI を作成、一覧表示、削除する方法
description: Azure PowerShell を使って、ユーザーによって割り当てられたマネージド サービス ID (MSI) を作成、一覧表示、削除する手順を説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: a9f684eccefab3e43d9b2b7a364b245a53519f76
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389687"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Azure PowerShell を使用して、ユーザー割り当て ID を作成、一覧表示、削除する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

管理対象サービス ID は、Azure Active Directory の管理対象 ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure PowerShell を使って、ユーザー割り当て ID を作成、一覧表示、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- [最新バージョンの Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) をインストールします (まだインストールしていない場合)。
- PowerShell をローカルにインストールして使う場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。
- この記事の管理操作を実行するには、お使いのアカウントに次のロールが割り当てられている必要があります。
    - ユーザー割り当て ID の作成、読み取り (一覧)、更新、および削除するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - ユーザー割り当て ID のプロパティを読み取る (一覧表示する) ための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。

> [!NOTE]
> ユーザー割り当て ID はまだプレビュー段階ですが、最初に次のコマンドを使用して AzureRM.ManagedServiceIdentity モジュールを手動でインストールする必要があります。 
```azurepowershell-interactive
Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease
```

## <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する

ユーザー割り当て ID を作成するには、[New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) コマンドを使います。 `ResourceGroupName` パラメーターではユーザー割り当て ID を作成するリソース グループを指定し、`-Name` パラメーターではその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>ユーザー割り当て ID を一覧表示する

ユーザー割り当て ID を一覧表示するには、[Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) コマンドを使います。  `-ResourceGroupName` パラメーターでは、ユーザー割り当て ID を作成したリソース グループを指定します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
応答では、ユーザー ID に対してはキー `Type` に値 `"Microsoft.ManagedIdentity/userAssignedIdentities"` が返されます。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>ユーザー割り当て ID を削除する

ユーザー割り当て ID を削除するには、[Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) コマンドを使います。  `-ResourceGroupName` パラメーターではユーザー割り当て ID を作成したリソース グループを指定し、`-Name` パラメーターではその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> ユーザー割り当て ID を削除しても、それが割り当てられていたすべてのリソースから参照が削除されることはありません。 ID の割り当ては、別に削除する必要があります。

## <a name="related-content"></a>関連コンテンツ

Azure PowerShell MSI コマンドについて詳しくは、「[AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity)」をご覧ください。


 
