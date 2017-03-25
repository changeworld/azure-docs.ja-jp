---
title: "Azure 仮想マシンの複数の IP アドレス - PowerShell | Microsoft Docs"
description: "PowerShell を使用して Resource Manager で仮想マシンに複数の IP アドレスを割り当てる方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: acf5ae8dc98213fe435f8feafe4a8ef246f545b9
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>PowerShell を使用して仮想マシンに複数の IP アドレスを割り当てる

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

この記事では、PowerShell を使用して Azure Resource Manager デプロイメント モデルで仮想マシン (VM) を作成する方法を説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>複数の IP アドレスを持つ VM を作成する

以下の手順は、シナリオの説明に従って複数の IP アドレスを持つ VM を作成する方法を示しています。 変数名と IP アドレスの種類は、実際の実装に合わせて変更してください。

1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。 まだ PowerShell をインストール、構成していない場合は、「 [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 」の手順を実行してください。
2. [Windows VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順 1 ～ 4 を実行します。 手順 5 (パブリック IP リソースとネットワーク インターフェイスの作成) は実行しないでください。 その記事で使用されている変数の名前を変更した場合は、残りの手順でも変数の名前を変更します。 Linux VM を作成するには、Windows ではなく Linux オペレーティング システムを選択します。
3. 次のコマンドを入力して、Windows VM の作成に関する記事の手順 4 (VNet の作成) で作成したサブネット オブジェクトを格納する変数を作成します。

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
4. NIC に割り当てる IP 構成を定義します。 構成は、必要に応じて追加、削除、または変更できます。 このシナリオで説明する構成は次のとおりです。

    **IPConfig&1;**

    次のコマンドを入力して、以下を作成します。
    - 静的パブリック IP アドレスが割り当てられたパブリック IP アドレス リソース
    - パブリック IP アドレス リソースと動的プライベート IP アドレスが割り当てられた IP 構成

    ```powershell
    $myPublicIp1    = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    上記のコマンドの `-Primary` スイッチに注意してください。 NIC に複数の IP 構成を割り当てる場合は、1 つの構成を *プライマリ*として割り当てる必要があります。

    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

    **IPConfig-2**

    次の **$IPAddress** 変数の値を、作成したサブネットで使用できる有効なアドレスに変更します。 アドレス 10.0.0.5 がサブネットで使用可能かどうかを確認するには、`Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet` コマンドを入力します。 このアドレスを使用できる場合、出力で *True* が返されます。 使用できない場合は、出力で *False* が返され、使用可能なアドレスの一覧が返されます。 次のコマンドを入力して、新しいパブリック IP アドレス リソース、および静的パブリック IP アドレスと静的プライベート IP アドレスが割り当てられた新しい IP 構成を作成します。
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = "10.0.0.5"
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    次のコマンドを入力して、動的プライベート IP アドレスが割り当てられ、パブリック IP アドレスは割り当てられていない IP 構成を作成します。

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
5. 次のコマンドを入力して、前の手順で定義した IP 構成を使用して NIC を作成します。

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > この記事では、すべての IP 構成を&1; つの NIC に割り当てていますが、複数の IP 構成を VM の任意の NIC に割り当てることもできます。 複数の NIC が接続された VM の作成方法については、[複数の NIC が接続された VM の作成](virtual-network-deploy-multinic-arm-ps.md)に関する記事をご覧ください。

6. [VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順 6 を実行します。 

    > [!WARNING]
    > 次の場合、VM の作成に関する記事の手順 6 は失敗します。
    > - この記事の手順 6 で、$myNIC という名前の変数を別の変数に変更した場合。
    > - この記事と VM の作成に関する記事のこれまでの手順を完了していない場合。
    >
7. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソースを表示します。

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

## <a name="add"></a>VM に IP アドレスを追加する

プライベート IP アドレスとパブリック IP アドレスを NIC に追加するには、次の手順を実行します。 次のセクションの例では、この記事の[シナリオ](#Scenario)で説明している&3; つの IP 構成を使用した VM を既に所有していることを前提としていますが、必須ではありません。

1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。 まだ PowerShell をインストール、構成していない場合は、「 [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 」の手順を実行してください。
2. 次の $ 変数の "値" を、IP アドレスを追加する NIC の名前、NIC が属するリソース グループ、NIC の場所に変更します。

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    変更する NIC の名前がわからない場合は、次のコマンドを入力してから、前述の変数の値を変更します。

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. 変数を作成し、次のコマンドを入力して、既存の NIC に設定します。

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
4. 次のコマンドで、*myVNet* と *mySubnet* を VNet の名前と NIC の接続先のサブネットの名前にそれぞれ変更します。 VNet オブジェクトと NIC の接続先のサブネット オブジェクトを取得するコマンドを入力します。

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    VNet 名または NIC の接続先のサブネット名がわからない場合は、次のコマンドを入力します。
    ```powershell
    $mynic.IpConfigurations
    ```
    返された出力で次のようなテキストを探します。
    ```powershell
    Subnet   : {
                 "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    ```
    この出力では、*myVnet* が VNet、*mySubnet* が NIC の接続先のサブネットです。

5. 要件に基づいて、以下のいずれかのセクションの手順を実行します。

    **プライベート IP アドレスを追加する**

    NIC にプライベート IP アドレスを追加するには、IP 構成を作成する必要があります。 次のコマンドでは、静的 IP アドレス 10.0.0.7 が割り当てられた構成を作成します。 動的プライベート IP アドレスを追加する場合は、コマンドを入力する前に、`-PrivateIpAddress 10.0.0.7` を削除します。 静的 IP アドレスを指定するときは、サブネットの未使用のアドレスを指定する必要があります。 まず `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` コマンドを入力してアドレスをテストし、このアドレスが使用可能かどうかを確認することをお勧めします。 この IP アドレスを使用できる場合、出力で *True* が返されます。 使用できない場合は、出力で *False* が返され、使用可能なアドレスの一覧が返されます。

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    一意の構成名とプライベート IP アドレス (静的 IP アドレスを使用する構成の場合) を使用して、必要な数だけ構成を作成します。

    この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

    **パブリック IP アドレスを追加する**

    パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレス リソースを関連付けます。 必要に応じて、以下のいずれかのセクションの手順を実行します。

    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
    >

    - **パブリック IP アドレス リソースを新しい IP 構成に関連付ける**
    
        すべての IP 構成にプライベート IP アドレスが必要であるため、パブリック IP アドレスを新しい IP 構成に追加するときは、必ずプライベート IP アドレスも追加する必要があります。 既存のパブリック IP アドレス リソースを追加することも、新しいリソースを作成することもできます。 新しいパブリック IP アドレス リソースを作成するには、次のコマンドを入力します。
    
        ```powershell
        $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Static
        ```

         動的プライベート IP アドレスと、関連付けられた *myPublicIp3* パブリック IP アドレス リソースで新しい IP 構成を作成するには、次のコマンドを入力します。

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
         $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
        ```

    - **パブリック IP アドレス リソースを既存の IP 構成に関連付ける**

        パブリック IP アドレス リソースは、このリソースがまだ関連付けられていない IP 構成にのみ関連付けることができます。 IP 構成にパブリック IP アドレスが関連付けられているかどうかを確認するには、次のコマンドを入力します。

        ```powershell
        $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        次のような出力が表示されます。<br>

            Name       PrivateIpAddress PublicIpAddress                                           Primary
            
            IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
            IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
            IpConfig-3 10.0.0.6                                                                     False

        *IpConfig-3* の **PublicIpAddress** 列が空白であるため、現在、この構成にはパブリック IP アドレス リソースは関連付けられていません。 IpConfig-3 に既存のパブリック IP アドレス リソースを追加することも、次のコマンドを入力して新しいリソースを作成することもできます。

        ```powershell
        $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Static
        ```

        パブリック IP アドレス リソースを、*IPConfig-3* という名前の既存の IP 構成に関連付けるには、次のコマンドを入力します。
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
        ```

6. 次のコマンドを入力して、新しい IP 構成で NIC を設定します。

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

7. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソースを表示します。

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

