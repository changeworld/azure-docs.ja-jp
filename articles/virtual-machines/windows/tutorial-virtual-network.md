---
title: "Azure Virtual Networks と Windows 仮想マシン | Microsoft Docs"
description: "チュートリアル - Azure PowerShell を使用した Azure Virtual Networks と Windows 仮想マシンの管理"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 7015a5317c631dad9079f2694051fa7fb28d232b
ms.lasthandoff: 04/26/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Azure PowerShell を使用した Azure Virtual Networks と Windows 仮想マシンの管理

このチュートリアルでは、仮想ネットワーク (VNet) で複数の仮想マシン (VM) を作成し、仮想マシン間のネットワーク接続を構成する方法について説明します。 完了すると、HTTP 接続用のポート 80 で、インターネットから "フロント エンド" の VM にアクセスできます。 SQL Server データベース搭載の "バックエンド" VM は分離され、ポート 1433 でフロントエンド VM からのみアクセスできるようになります。

このチュートリアルの手順は、最新バージョンの [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) モジュールを使用して行うことができます。

## <a name="create-vnet"></a>VNet を作成する

VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 VNet 内には、サブネット、これらのサブネットへの接続規則、VM からサブネットへの接続があります。

他の Azure リソースを作成する前に、[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-3.8.0) を使用してリソース グループを作成する必要があります。 次の例では、`myRGNetwork` という名前のリソース グループを `westus` の場所に作成します。

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location westus
```

サブネットは、VNet の子リソースで、IP アドレスのプレフィックスを使用して、CIDR ブロック内のアドレス空間のセグメントの定義に役立ちます。 NIC をサブネットに追加し、VM に接続して、さまざまなワークロードへの接続を提供できます。

[New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0) を使用してサブネットを作成します。

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

[New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork?view=azurermps-3.8.0) を使用して、`myFrontendSubnet` を使用する `myVNet` という名前の VNet を作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>フロントエンド VM を作成する

VM が VNet で通信するには、仮想ネットワーク インターフェイス (NIC) が必要です。 `myFrontendVM` にはインターネットからアクセスするため、パブリック IP アドレスも必要です。 

[New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress?view=azurermps-3.8.0) を使用してパブリック IP アドレスを作成します。

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

[New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface?view=azurermps-3.8.0) を使用して、NIC を作成します。


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、VM の管理者アカウントに必要なユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

[New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-3.8.0)、[Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem?view=azurermps-3.8.0)、[Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage?view=azurermps-3.8.0)、[Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-3.8.0)、[Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?view=azurermps-3.8.0)、[New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-3.8.0) を使用して、VM を作成します。 

```powershell
$frontendVM = New-AzureRmVMConfig -VMName myFrontendVM -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem -VM $frontendVM -Windows -ComputerName myFrontendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage -VM $frontendVM -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
$frontendVM = Set-AzureRmVMOSDisk -VM $frontendVM -Name myFrontendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface -VM $frontendVM -Id $frontendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $frontendVM
```

## <a name="install-web-server"></a>Web サーバーをインストールする

リモート デスクトップ セッションを使用して、`myFrontendVM` に IIS をインストールできます。 アクセスするには、VM のパブリック IP アドレスを取得する必要があります。

[Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-3.8.0) を使用して、`myFrontendVM` のパブリック IP アドレスを取得します。 次の例では、先ほど作成した `myPublicIPAddress` の IP アドレスを取得しています。

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myRGNetwork -Name myPublicIPAddress | select IpAddress
```

後の手順で使用できるように、この IP アドレスをメモしておきます。

次のコマンドを使用して、`myFrontendVM` とのリモート デスクトップ セッションを作成します。 `<publicIPAddress>` を先ほどメモしたアドレスに置き換えます。 メッセージが表示されたら、VM の作成時に使用した資格情報を入力します。

```
mstsc /v:<publicIpAddress>
``` 

`myFrontendVM` にログインしたら、1 行の PowerShell を使用して、IIS をインストールし、Web トラフィックを許可するローカル ファイアウォール規則を有効にできます。 PowerShell プロンプトを開き、次のコマンドを実行します。

[Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) を使用して、IIS Web サーバーをインストールするカスタム スクリプト拡張機能を実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

これで、VM を参照するためのパブリック IP アドレスを使用して IIS サイトにアクセスできるようになりました。

![IIS の既定のサイト](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>内部トラフィックを管理する

ネットワーク セキュリティ グループ (NSG) には、VNet に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG は、サブネット、または VM にアタッチされている個々の NIC に関連付けることができます。 ポートを介した VM へのアクセスの開始と終了は、NSG 規則を使用して行われます。 `myFrontendVM` を作成したときに、RDP 接続用の受信ポート 3389 が自動的に開かれています。

VM の内部通信は、NSG を使用して構成できます。 このセクションでは、ネットワークに追加のサブネットを作成し、このサブネットにポート 1433 での `myFrontendVM` から `myBackendVM` への接続を許可する NSG を割り当てる方法について説明します。 サブネットは、作成時に VM に割り当てられます。

バックエンド サブネットに対する NSG を作成することで、`myBackendVM` への内部トラフィックを `myFrontendVM` からのみに制限できます。 次の例では、[New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig?view=azurermps-3.8.0) を使用して `myBackendNSGRule` という名前の NSG 規則を作成します。

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecuritygroup?view=azurermps-3.8.0) を使用して、`myBackendNSG` という名前の新しいネットワーク セキュリティ グループを追加します。

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>バックエンド サブネットを追加する

[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0) を使用して、`myBackEndSubnet` を `myVNet` に追加します。

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myRGNetwork -Name myVNet
```

## <a name="create-back-end-vm"></a>バックエンド VM を作成する

SQL Server イメージを使用すると、最も簡単にバックエンド VM を作成できます。 このチュートリアルではデータベース サーバーを使用して VM を作成しますが、データベースへのアクセスに関する情報は提供しません。

`myBackendNic` を作成します。

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Get-Credential を使用して、VM の管理者アカウントに必要なユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

`myBackendVM` を作成します。

```powershell
$backendVM = New-AzureRmVMConfig -VMName myBackendVM -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem -VM $backendVM -Windows -ComputerName myBackendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage -VM $backendVM -PublisherName MicrosoftSQLServer -Offer SQL2016-WS2016 -Skus Enterprise -Version latest
$backendVM = Set-AzureRmVMOSDisk -VM $backendVM -Name myBackendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface -VM $backendVM -Id $backendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $backendVM
```

使用されているイメージには SQL Server がインストールされていますが、このチュートリアルでは使用しません。 これが含まれているのは、Web トラフィックを処理する VM と、データベースの管理を処理する VM の構成の方法を示すためです。


