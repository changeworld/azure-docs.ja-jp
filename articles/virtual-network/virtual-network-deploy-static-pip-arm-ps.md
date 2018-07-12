---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure PowerShell | Microsoft Docs
description: PowerShell を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696585"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>PowerShell を使用して静的パブリック IP アドレスを持つ VM を作成する

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイ モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルをお勧めします。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>スクリプトを開始する
使用するすべての PowerShell スクリプトは、 [ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1)からダウンロードできます。 以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

デプロイに使用する値に基づいて、以下の変数の値を変更します。 以下の値は、この記事で使用するシナリオに対応しています。

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>VM に必要なリソースを作成する
VM を作成するには、VM で使用するリソース グループ、VNet、パブリック IP、NIC が必要です。

1. 新しいリソース グループを作成します。

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. VNet とサブネットを作成します。

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. パブリック IP リソースを作成します。 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. パブリック IP を使用して、上記で作成したサブネットに VM のネットワーク インターフェイス (NIC) を作成します。 VNet を Azure から取得する最初のコマンドレットに注意してください。これが必要なのは、既存の VNet を変更するために `Set-AzureRmVirtualNetwork` が実行されたためです。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. VM の OS ドライブをホストするストレージ アカウントを作成します。

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>VM の作成
必要なリソースがすべて揃ったので、新しい VM を作成できます。

1. VM の構成オブジェクトを作成します。

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. VM ローカル管理者アカウントの資格情報を取得します。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. VM 構成オブジェクトを作成します。

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. VM のオペレーティング システム イメージを設定します。

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. OS ディスクを構成します。

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. NIC を VM に追加します。

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. VM を作成します。

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. スクリプト ファイルを保存します。

## <a name="run-the-script"></a>スクリプトを実行する

必要な変更を行った後、前記のスクリプトを実行します。 仮想マシンの作成には、数分かかります。

## <a name="set-ip-addresses-within-the-operating-system"></a>オペレーティング システム内で IP アドレスを設定する

仮想マシンのオペレーティング システム内で Azure の仮想マシンに割り当てられるパブリック IP アドレスは、手動で割り当てないでください。 VM のオペレーティング システム内で Azure 仮想マシンに割り当てられるプライベート IP は、[Windows VM に複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-powershell.md)場合など、必要でない限り静的に割り当てないことをお勧めします。 実際にオペレーティング システム内でプライベート IP アドレスを手動で設定する場合は、それが Azure [ネットワーク インターフェイス](virtual-network-network-interface-addresses.md#change-ip-address-settings)に割り当てられているプライベート IP アドレスと同じアドレスであるようにしてください。そうしないと、仮想マシンへの接続が失われる可能性があります。 詳細については、[プライベート IP アドレス](virtual-network-network-interface-addresses.md#private)設定に関するページを参照してください。

## <a name="next-steps"></a>次の手順

この記事で作成した VM では、すべてのネットワーク トラフィックを送受信できます。 ネットワーク セキュリティ グループ内に受信と送信のセキュリティ規則を定義して、ネットワーク インターフェイスかサブネット、またはその両方で送受信可能なトラフィックを制限できます。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループの概要](security-overview.md)に関するページを参照してください。