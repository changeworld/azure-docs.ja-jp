---
title: Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する方法
description: Azure PowerShell を使用して、ユーザー割り当てマネージド ID を作成、一覧表示、削除する手順を説明します。
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
ms.openlocfilehash: b8cd0de938676204bc6fdc7785c49442d6dc8663
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344262"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure リソースのマネージド ID は、Azure Active Directory で管理される ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure PowerShell を使って、ユーザー割り当てマネージド ID を作成、一覧表示、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- [最新バージョンの Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) をインストールします (まだインストールしていない場合)。
- PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 
- PowerShell をローカルで実行している場合は、次の操作を行う必要もあります。 
    - `Login-AzureRmAccount` を実行して、Azure との接続を作成します。
    - [PowerShellGet の最新バージョン](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)をインストールします。
    - `Install-Module -Name PowerShellGet -AllowPrerelease` を実行して `PowerShellGet` モジュールのプレリリース バージョンを取得します (`AzureRM.ManagedServiceIdentity` モジュールをインストールするには、このコマンドを実行した後に現在の PowerShell セッションから `Exit` することが必要になる場合があります)。
    - `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` を実行して `AzureRM.ManagedServiceIdentity` モジュールのプレリリース バージョンをインストールして、この記事のユーザー割り当てマネージド ID 操作を実行します。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - ユーザー割り当てマネージド ID の作成、読み取り (一覧表示)、更新、および削除するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - [マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。ユーザー割り当てマネージド ID のプロパティを読み取ります (一覧表示します)。

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当て ID を作成するには、[New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) コマンドを使います。 `ResourceGroupName` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-Name` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID を一覧表示するには、[Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) コマンドを使います。  `-ResourceGroupName` パラメーターでは、ユーザー割り当てマネージド ID を作成したリソース グループを指定します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
応答内のユーザー割り当てマネージド ID には、キー `Type` に対して返された `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値が含まれます。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、[Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) コマンドを使います。  `-ResourceGroupName` パラメーターではユーザー割り当て ID を作成したリソース グループを指定し、`-Name` パラメーターではその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 ID の割り当ては、別に削除する必要があります。

## <a name="next-steps"></a>次の手順

Azure PowerShell MSI コマンドの詳細については、「[AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity)」を参照してください。
