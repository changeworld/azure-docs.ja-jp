---
title: Azure PowerShell で SQL Server Windows VM を作成する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell で Windows SQL Server 2017 仮想マシンを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: bebb153d5ff840a0eed7d6afffccd03a5236592d
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "42022620"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>クイック スタート: Azure PowerShell で SQL Server Windows 仮想マシンを作成する

このクイック スタートでは、Azure PowerShell で SQL Server 仮想マシンを作成する方法について説明します。

> [!TIP]
> このクイック スタートでは、SQL VM を迅速にプロビジョニングしてそこに接続する方法について説明します。 SQL VM を作成するためのその他の Azure PowerShell オプションについては、[Azure PowerShell を使用した SQL Server VM のプロビジョニング ガイド](virtual-machines-windows-ps-sql-create.md)を参照してください。

> [!TIP]
> SQL Server の仮想マシンに関するご質問については、[よくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)に関するページをご覧ください。

## <a id="subscription"></a>Azure サブスクリプションを取得する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a id="powershell"></a>Azure PowerShell を入手する

このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="configure-powershell"></a>PowerShell の構成

1. PowerShell を開いて **Connect-AzureRmAccount** コマンドを実行し、Azure アカウントへのアクセスを確立します。

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. 資格情報を入力するためのサインイン画面が表示されます。 Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

## <a name="create-a-resource-group"></a>リソース グループの作成

1. 一意のリソース グループ名を持つ変数を定義します。 クイック スタートの以降の手順を簡略化するために、コマンドの残りの部分では、この名前を他のリソース名のベースとして使用します。

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. すべての VM リソースのターゲット Azure リージョンの場所を定義します。

   ```PowerShell
   $Location = "East US"
   ```

1. リソース グループを作成します。

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>ネットワーク設定の構成

1. 仮想ネットワーク、サブネット、パブリック IP アドレスを作成します。 これらのリソースは、仮想マシンへのネットワーク接続を提供し、その仮想マシンをインターネットに接続するために使用されます。

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. ネットワーク セキュリティ グループを作成します。 リモート デスクトップ (RDP) および SQL Server 接続を許可するように規則を構成します。

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. ネットワーク インターフェイスを作成します。

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>SQL VM の作成

1. 資格情報を定義して VM にサインインします。 ユーザー名は "azureadmin" です。 コマンドを実行する前に、パスワードを変更してください。

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. 仮想マシン構成オブジェクトを作成してから、VM を作成します。 次のコマンドでは、Windows Server 2016 で SQL Server 2017 Developer Edition VM を作成します。

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > VM の作成には数分かかります。

## <a name="install-the-sql-iaas-agent"></a>SQL Iaas Agent のインストール

ポータル統合および SQL VM の機能を取得するには、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)をインストールする必要があります。 新しい VM に Agent をインストールするには、VM の作成後に次のコマンドを実行します。

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>VM へのリモート デスクトップ接続

1. 次のコマンドを使用して、新しい VM のパブリック IP アドレスを取得します。

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. 返された IP アドレスを取得し、コマンド ライン パラメーターとして **mstsc** に渡し、新しい VM へのリモート デスクトップ セッションを開始します。

   ```
   mstsc /v:<publicIpAddress>
   ```

1. 資格情報を求められたら、別のアカウントの資格情報の入力を選択します。 先頭にバックスラッシュを付けたユーザー名 (例: `\azureadmin`) と、このクイック スタートの前の手順で設定したパスワードを入力します。

## <a name="connect-to-sql-server"></a>SQL Server への接続

1. リモート デスクトップ セッションにログインしたら、スタート メニューから **SQL Server Management Studio 2017** を起動します。

1. **[サーバーに接続]** ダイアログでは、既定の設定をそのまま使用します。 サーバー名は VM の名前です。 認証は **[Windows 認証]** に設定されます。 **[接続]** をクリックします。

これで、SQL Server にローカル接続されました。 リモート接続する場合は、ポータルから、または手動で[接続を構成する](virtual-machines-windows-sql-connect.md)必要があります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

VM を継続的に実行する必要がない場合は、使用しないときに停止することで、不要な料金の発生を回避できます。 次のコマンドでは、VM を停止しますが、後から使用できるように残しておきます。

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

仮想マシンに関連付けらているすべてのリソースは、**Remove-AzureRmResourceGroup** コマンドを使用して完全に削除することもできます。 これを行うと仮想マシンも完全に削除されるため、このコマンドは注意して使用してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure PowerShell を使用して SQL Server 2017 仮想マシンを作成しました。 新しい SQL Server にデータを移行する方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [SQL VM にデータベースを移行する](virtual-machines-windows-migrate-sql.md)
