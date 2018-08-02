---
title: PowerShell を使用して Azure VM でマネージド サービス ID を構成する方法
description: PowerShell を使用して、Azure VM でマネージド サービス ID を構成する方法をステップ バイ ステップで説明します。
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
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 68c9ae7baa6b8fa1ebf672c28bf3c466b4b54860
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258394"
---
# <a name="configure-a-vm-managed-service-identity-using-powershell"></a>PowerShell を使用して、VM マネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>この記事では、PowerShell を使用して、Azure VM で管理対象サービス ID に対する次の操作を実行する方法を説明します。
- 

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - VM を作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、Azure VM から削除したりするための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - ユーザー割り当て ID を VM に割り当てたり、VM から削除したりするための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。
- [最新バージョンの Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) をインストールします (まだインストールしていない場合)。

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure PowerShell を使用してシステム割り当て ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Azure VM の作成中にシステム割り当て ID を有効にする

システム割り当て ID を有効にして Azure VM を作成する場合:

1. 次のいずれかの Azure VM クイックスタートを参照して、必要なセクション (「Azure へのログイン」、「リソース グループの作成」、「ネットワーク グループの作成」、「VM の作成」) のみを実行してください。
    
    「VM の作成」セクションに到達したときに、[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットの構文にわずかな変更を加えます。 必ず `-AssignIdentity:$SystemAssigned` パラメーターを追加し、システム割り当て ID を有効にして VM のプロビジョニングを行います。次に例を示します。
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)

2. (省略可能) [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type` パラメーターを使用して、マネージド サービス ID VM 拡張機能を追加します。 VM の種類に応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>既存の Azure VM においてシステム割り当て ID を有効にする

既存の仮想マシンにおいてシステム割り当て ID を有効にする必要がある場合:

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```powershell
   Login-AzureRmAccount
   ```

2. 最初に、`Get-AzureRmVM` コマンドレットを使用して VM プロパティを取得します。 システム割り当て ID を有効にするには、[Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) コマンドレットで `-AssignIdentity` スイッチを使用します。

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (省略可能) [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type` パラメーターを使用して、マネージド サービス ID VM 拡張機能を追加します。 VM の種類に応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。 既存の VM の場所に一致する正しい `-Location` パラメーターを指定してください。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Azure VM でシステム割り当て ID を無効にする

システム割り当て ID は不要になったが、ユーザー割り当て ID はまだ必要な仮想マシンがある場合は、次のコマンドレットを使用します。

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```powershell
   Login-AzureRmAccount
   ```

2. `Get-AzureRmVM` コマンドレットを使用して VM プロパティを取得し、`-IdentityType` パラメーターを `UserAssigned` に設定します。

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

システム割り当て ID は不要になった、ユーザー割り当て ID がない仮想マシンがある場合は、次のコマンドを使用します。

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

マネージド サービス ID VM 拡張機能を削除するには、-Name スイッチと [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) コマンドレットを使用して、拡張機能を追加したときに使用したのと同じ名前を指定します。

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure PowerShell を使用して、VM との間でユーザー割り当て ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>作成中にユーザー割り当て ID を VM に割り当てる

Azure VM の作成時にユーザー割り当て ID を VM に割り当てる場合:

1. 次のいずれかの Azure VM クイックスタートを参照して、必要なセクション (「Azure へのログイン」、「リソース グループの作成」、「ネットワーク グループの作成」、「VM の作成」) のみを実行してください。 
  
    「VM の作成」セクションに到達したときに、[`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットの構文にわずかな変更を加えます。 `-IdentityType UserAssigned` および `-IdentityID ` パラメーターを追加し、ユーザー割り当て ID を使用して VM のプロビジョニングを行います。  `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、および `<MSI NAME>` を独自の値に置き換えます。  例: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)

2. (省略可能) [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type` パラメーターを使用して、マネージド サービス ID VM 拡張機能を追加します。 VM の種類に応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。 既存の VM の場所に一致する正しい `-Location` パラメーターを指定してください。
      > [!NOTE]
    > Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>ユーザー ID を既存の Azure VM に割り当てる

ユーザー割り当て ID を既存の Azure VM に割り当てる場合:

1. `Connect-AzureRmAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```powershell
   Connect-AzureRmAccount
   ```

2. [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) コマンドレットを使用して、ユーザー割り当て ID を作成します。  出力の `Id` は次の手順で必要になるので書き留めます。

   > [!IMPORTANT]
   > ユーザー割り当て ID の作成には、英数字およびハイフン (0-9、a-z、A-Z、-) 文字のみがサポートされます。 さらに、VM/VMSS への割り当てが適切に動作するためには、名前の長さは 24 文字以下にする必要があります。 アップデートは後ほどご確認ください。 詳細については、[よく寄せられる質問と既知の問題](known-issues.md)に関する記事をご覧ください。

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. `Get-AzureRmVM` コマンドレットを使用して VM プロパティを取得します。 次に、ユーザー割り当て ID を Azure VM に割り当てるため、[Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) コマンドレットで `-IdentityType` および `-IdentityID` スイッチを使用します。  `-IdentityId` パラメーターの値は、前の手順で書き留めた `Id` です。  `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、および `<USER ASSIGNED IDENTITY NAME>` を独自の値に置き換えます。

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type` パラメーターを使用して、マネージド サービス ID VM 拡張機能を追加します。 VM の種類に応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。 既存の VM の場所に一致する正しい `-Location` パラメーターを指定してください。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当ての管理対象 ID を削除する

VM に複数のユーザー割り当て ID がある場合は、次のコマンドを使用して、最後の ID 以外の ID をすべて削除できます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<MSI NAME>` はユーザー割り当て ID の名前プロパティであり、VM 上に残す必要があります。 この情報は、`az vm show` を使用し、VM の ID セクションで見つけることができます。

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
VM にシステム割り当て ID がなく、そこからユーザー割り当て ID をすべて削除する場合は、次のコマンドを使用します。

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
VM にシステム割り当て ID とユーザー割り当て ID の両方がある場合は、システム割り当て ID のみを使用するように切り替えることによって、すべてのユーザー割り当て ID を削除できます。

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>関連コンテンツ

- 
  [管理対象サービス ID の概要](overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。
  
  - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md) 
