---
title: PowerShell を使用して Azure VMSS でマネージド サービス ID を構成する方法
description: PowerShell を使用して、Azure VMSS においてシステム割り当て ID とユーザー割り当て ID を構成する方法をステップ バイ ステップで説明します。
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
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257744"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>PowerShell を使用して、VMSS マネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用して、Azure 仮想マシン スケール セットにおいてマネージド サービス ID 操作を実行する方法を説明します。
- 仮想マシン スケール セットでシステム割り当て ID を有効および無効にする
- 仮想マシン スケール セットでユーザー割り当て ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - 仮想マシン スケール セットを作成して、仮想マシン スケール セットからシステム割り当てマネージドおよびユーザー ID を有効化および削除するための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。
    - ユーザー割り当て ID を作成するための[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。
    - 仮想マシン スケール セットからユーザー割り当て ID を割り当てたり削除するための[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。
- [最新バージョンの Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) をインストールします (まだインストールしていない場合)。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure PowerShell を使用してシステム割り当て ID を有効にする方法と削除する方法について説明します。

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にシステム割り当て ID を有効にする

システム割り当て ID を有効にして VMSS を作成する場合:

1. システム割り当て ID による VMSS の作成については、[New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) コマンドレット リファレンス記事の「*例 1*」をご覧ください。  パラメーター `-IdentityType SystemAssigned` を `New-AzureRmVmssConfig` コマンドレットに追加します。

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (省略可能) [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) コマンドレットで `-Name` および `-Type` パラメーターを使用して、マネージド サービス ID VMSS 拡張機能を追加します。 仮想マシン スケール セットに応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

    > [!NOTE]
    > Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにおいてシステム割り当て ID を有効にする

既存の Azure 仮想マシン スケール セットにおいてシステム割り当て ID を有効にする必要がある場合:

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシン共同作業者」など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```powershell
   Login-AzureRmAccount
   ```

2. まず、[`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss) コマンドレットを使用して、仮想マシン スケール セットのプロパティを取得します。 システム割り当て ID を有効にするには、[Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) コマンドレットで `-IdentityType` スイッチを使用します。

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) コマンドレットで `-Name` および `-Type` パラメーターを使用して、マネージド サービス ID VMSS 拡張機能を追加します。 仮想マシン スケール セットに応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当て ID を無効にする

システム割り当て ID は不要になったが、ユーザー割り当て ID はまだ必要な仮想マシン スケール セットがある場合は、次のコマンドレットを使用します。

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシン共同作業者」など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 次のコマンドレットを実行します。

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

システム割り当て ID は不要になった、ユーザー割り当て ID がない仮想マシン スケール セットがある場合は、次のコマンドを使用します。

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure PowerShell を使用して、仮想マシン スケール セットとの間でユーザー割り当て ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にユーザー割り当て ID を割り当てる

ユーザー割り当て ID による新しい仮想マシン スケール セットの作成は、PowerShell では現在サポートされていません。 既存の仮想マシン スケール セットにユーザー割り当て ID を追加する方法については、次のセクションをご覧ください。 アップデートは後ほどご確認ください。

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにユーザー ID を割り当てる

既存の Azure 仮想マシン スケール セットにユーザー割り当て ID を割り当てるには

1. `Connect-AzureRmAccount` を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシン共同作業者」など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```powershell
   Connect-AzureRmAccount
   ```

2. まず、`Get-AzureRmVM` コマンドレットを使用して、仮想マシン スケール セットのプロパティを取得します。 次に、ユーザー割り当て ID を仮想マシン スケール セットに割り当てるため、[Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) コマンドレットで `-IdentityType` および `-IdentityID` スイッチを使用します。 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、`<USER ASSIGNED ID1>`、`USER ASSIGNED ID2` を、実際の値に置き換えます。

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当てマネージド ID を削除する

仮想マシン スケール セットに複数のユーザー割り当て ID がある場合は、次のコマンドを使用して、最後の ID 以外の ID をすべて削除できます。 `<RESOURCE GROUP>` と `<VMSS NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<MSI NAME>` はユーザー割り当て ID の名前プロパティであり、仮想マシン スケール セット上に残す必要があります。 この情報は、`az vmss show` を使用して、仮想マシン スケール セットの ID セクションで見つけることができます。

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
仮想マシン スケール セットにシステム割り当て ID がなく、そこからユーザー割り当て ID をすべて削除する場合は、次のコマンドを使用します。

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
仮想マシン スケール セットにシステム割り当て ID とユーザー割り当て ID の両方がある場合は、システム割り当て ID のみを使用するように切り替えることによって、すべてのユーザー割り当て ID を削除できます。

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>関連コンテンツ

- 
  [管理対象サービス ID の概要](overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。
  
  - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md) 

















