---
title: "PowerShell を使用して Azure VM で MSI を構成する方法"
description: "PowerShell を使用して、Azure VM で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>PowerShell を使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用して Azure Windows VM の MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

まだ [Azure PowerShell バージョン 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) がインストールされていなければ、それもインストールします。

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM の作成中に MSI を有効にする

MSI 対応の新しい Windows 仮想マシンのリソースは、指定した構成パラメーターを使用して、新しいリソース グループ内に作成されます。 また、これら数多くのコマンドレットを実行してから結果が返されるまでに 30 秒以上、最終的な VM の作成が完了するまでには数分間かかる可能性があることに注意してください。

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```powershell
   Login-AzureRmAccount
   ```

2. `New-AzureRmResourceGroup` コマンドレットを使用して、VM とその関連リソースの管理およびデプロイ用に[リソース グループ](../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. VM のネットワーキングリソースを作成します。

   a. 仮想ネットワーク、サブネット、パブリック IP アドレスを作成します。 これらのリソースは、仮想マシンへのネットワーク接続を提供し、その仮想マシンをインターネットに接続するために使用されます。

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. ネットワーク セキュリティ グループとネットワーク セキュリティ グループの規則を作成します。 ネットワーク セキュリティ グループは、受信規則と送信規則を使用して仮想マシンを保護します。 この場合、受信規則はポート 3389 に作成され、受信リモート デスクトップ接続を許可します。 さらにポート 80 に対して、受信 Web トラフィックを許可する受信規則を作成します。

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. 仮想マシン用の仮想ネットワーク カードを作成します。 ネットワーク カードは、仮想マシンをサブネット、ネットワーク セキュリティ グループ、パブリック IP アドレスに接続します。

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. VM を作成します。

   a. 構成可能な VM オブジェクトを作成します。 これらの設定 (仮想マシンのイメージ、サイズ、認証構成など) は、仮想マシンのデプロイ時に使用されます。 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットで使用される `-IdentityType "SystemAssigned"` パラメーターを使用すると、VM は MSI でプロビジョニングされます。 `Get-Credential` コマンドレットは、仮想マシンのユーザー名とパスワードとして構成される資格情報のプロンプトを出します。

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. 新しい VM を次のようにプロビジョニングします。

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type "ManagedIdentityExtensionForWindows"` パラメーターを使用して、MSI VM 拡張機能を追加します。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>既存の Azure VM で MSI を有効にする

既存の仮想マシンで MSI を有効にする必要がある場合は、次のようにします。

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 次のように、VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```powershell
   Login-AzureRmAccount
   ```

2. 最初に、`Get-AzureRmVM` コマンドレットを使用して VM プロパティを取得します。 MSI を有効にするには、[Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) コマンドレットで `-IdentityType` スイッチを使用します。

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type "ManagedIdentityExtensionForWindows"` パラメーターを使用して、MSI VM 拡張機能を追加します。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。 既存の VM の場所に一致する正しい `-Location` パラメーターを指定してください。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM から MSI を削除する

MSI が不要になった仮想マシンがある場合、`RemoveAzureRmVMExtension` コマンドレットを使用して VM から MSI を削除することができます。

1. [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) コマンドレットで `-Name "ManagedIdentityExtensionForWindows"` スイッチを使用します。

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>関連コンテンツ

- [管理対象サービス ID の概要](msi-overview.md)
- この記事の出典元は、MSI 固有の手順を含めるように変更されたクイック スタート「[PowerShell を使用して Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-powershell.md)」です。 

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

















