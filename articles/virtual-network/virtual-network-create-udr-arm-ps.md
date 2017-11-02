---
title: "PowerShell を使用してネットワーク仮想アプライアンス経由でネットワーク トラフィックをルーティングするユーザー定義ルートを作成する | Microsoft Docs"
description: "ネットワーク仮想アプライアンス経由でネットワーク トラフィックをルーティングすることで、Azure の既定のルーティングを上書きするユーザー定義ルートを作成する方法を説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 9696a74ac02688f9004156f6f16b39b37756751d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---powershell"></a>PowerShell を使用してユーザー定義ルートを作成する

このチュートリアルでは、ネットワーク仮想アプライアンスを通じて 2 つの[仮想ネットワーク](virtual-networks-overview.md) サブネット間でトラフィックをルーティングするユーザー定義ルートを作成する方法を説明します。 ネットワーク仮想アプライアンスは、ファイアウォールなどのネットワーク アプリケーションが実行されている仮想マシンです。 Azure 仮想ネットワークにデプロイできる事前構成されたネットワーク仮想アプライアンスの詳細については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) のページをご覧ください。

仮想ネットワークでサブネットを作成すると、次の図に示すように、既定の[システム ルート](virtual-networks-udr-overview.md#system-routes)が作成され、すべてのサブネットのリソースが相互に通信できます。

![既定のルート](./media/create-user-defined-route/default-routes.png)

このチュートリアルでは、次の図に示すように、パブリック サブネット、プライベート サブネット、および DMZ サブネットを使用して仮想ネットワークを作成します。 一般的には、パブリック サブネットには Web サーバーが、プライベート サブネットにはアプリケーションまたはデータベース サーバーがデプロイされます。 DMZ サブネットには、ネットワーク仮想アプライアンスとして機能する仮想マシンを作成し、必要に応じて、ネットワーク仮想アプライアンス経由で通信する仮想マシンを各サブネットに作成します。 次の図に示すように、パブリック サブネットとプライベート サブネットの間のトラフィックすべてが、アプライアンス経由でルーティングされます。

![ユーザー定義のルート](./media/create-user-defined-route/user-defined-routes.png)

この記事では、Resource Manager デプロイメント モデルを使用して、ユーザー定義ルートを作成する手順について説明します。ユーザー定義ルートを作成するときは、このデプロイメント モデルを使用することをお勧めします。 ユーザー定義ルート (クラシック) を作成する必要がある場合は、[ユーザー定義ルート (クラシック) の作成](virtual-network-create-udr-classic-ps.md)に関するページをご覧ください。 Azure 展開モデルの知識がない場合は、[Azure 展開モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの概要](virtual-networks-udr-overview.md#user-defined)に関する記事をご覧ください。

## <a name="create-routes-and-network-virtual-appliance"></a>ルートとネットワーク仮想アプライアンスの作成

PC で最新バージョンの PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) モジュールをインストールして構成するか、スクリプトの **[お試しください]** ボタンをクリックして、Azure Cloud Shell でスクリプトを実行することができます。 Cloud Shell には PowerShell AzureRM モジュールがインストールされています。
 
1. **前提条件**: 「[仮想ネットワークの作成](#create-a-virtual-network)」の手順を実行して、2 つのサブネットで仮想ネットワークを作成します。
2. コンピューターから PowerShell を実行する場合は、`login-azurermaccount` コマンドを使用して、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)で Azure にログインします。 Cloud Shell を使用する場合は、自動的にログインされます。 Cloud Shell では、前提条件の仮想ネットワークを作成するときに使用した Bash シェルから切り替えるには、再起動が必要になる可能性があります。
3. 手順で使用される変数をいくつか設定します。

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. 既存の仮想ネットワークに DMZ サブネットを作成します。

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. ネットワーク仮想アプライアンスの仮想マシンに対して静的パブリック IP アドレスを作成します。

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. NVA 仮想マシンを作成します。 NVA には、Linux または Windows オペレーティング システムが実行されている仮想マシンを指定できます。 仮想マシンを作成するには、いずれかのオペレーティング システムのスクリプトをコピーし、PowerShell セッションに貼り付けます。 Windows VM を作成している場合は、スクリプトをテキスト エディターに貼り付けて、$cred 変数の "値" を変更し、変更したテキストを PowerShell セッションに貼り付けます。

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. 既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックがルーティングされます。 ルートを作成することで、Azure の既定のルーティングを変更します。"*パブリック*" サブネットからのトラフィックは、"*プライベート*" サブネットに直接ルーティングされるのではなく、NVA にルーティングされます。

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. "*パブリック*" サブネットのルート テーブルを作成します。

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. ルート テーブルを パブリック サブネットに関連付けます。 ルート テーブルをサブネットに関連付けると、サブネットからのすべての送信トラフィックが、ルート テーブルのルートに従ってルーティングされます。 ルート テーブルは 0 個または複数のサブネットに関連付けることができますが、サブネットに関連付けることができるルート テーブルは 0 個または 1 つだけです。

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. "*プライベート*" サブネットから "*パブリック*" サブネットへの NVA 仮想マシン経由のトラフィック ルートを作成します。

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. "*プライベート*" サブネットのルート テーブルを作成します。

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. ルート テーブルを "*プライベート*" サブネットに関連付けます。

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **省略可能:** パブリック サブネットとプライベート サブネットに仮想マシンを作成し、「[ルーティングの検証](#validate-routing)」の手順を実行して、仮想マシン間の通信がネットワーク仮想アプライアンス経由でルーティングされていることを検証します。
15. **省略可能**: 「[リソースの削除](#delete-resources)」の手順を実行して、このチュートリアルで作成したリソースを削除します。

## <a name="validate-routing"></a>ルーティングの検証

1. 「[ルートとネットワーク仮想アプライアンスの作成](#create-routes-and-network-virtual-appliance)」の手順を実行します (まだ実行していない場合)。
2. 次のボックスの **[お試しください]** ボタンをクリックすると、Azure Cloud Shell が開きます。 メッセージが表示されたら、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して Azure にログインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。 Azure Cloud Shell は、Azure コマンド ライン インターフェイスがプレインストールされた無料の Bash シェルです。 

    次のスクリプトにより、"*パブリック*" サブネットと "*プライベート*" サブネットに 1 つずつ、2 つの仮想マシンが作成されます。 また、このスクリプトによって、NVA のオペレーティング システム内のネットワーク インターフェイスで IP 転送が有効になり、オペレーティング システムが、ネットワーク インターフェイス経由でトラフィックをルーティングできるようになります。 運用 NVA は、通常、ルーティング前にトラフィックを検査しますが、このチュートリアルのシンプルな NVA は、検査せずにトラフィックをルーティングします。 

    次の **Linux** または **Windows** のスクリプトで **[コピー]** をクリックし、スクリプトの内容をテキスト エディターに貼り付けます。 *adminPassword* 変数のパスワードを変更し、スクリプトを Azure Cloud Shell に貼り付けます。 「[ルートとネットワーク仮想アプライアンスの作成](#create-routes-and-network-virtual-appliance)」の手順 7. でネットワーク仮想アプライアンスを作成したときに選択したオペレーティング システムに対して、スクリプトを実行します。 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. パブリック サブネットとプライベート サブネットの仮想マシン間の通信を検証します。 

    - *myVm-Public* 仮想マシンのパブリック IP アドレスへの [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) または[リモート デスクトップ](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) 接続を確立します。
    - *myVm-Public* 仮想マシンのコマンド プロンプトで `ping myVm-Private` を入力します。 NVA がパブリック サブネットからプライベート サブネットへのトラフィックをルーティングするため、応答が届きます。
    - *myVm-Public* 仮想マシンから、パブリック サブネットとプライベート サブネットの仮想マシン間で traceroute を実行します。 パブリック サブネットとプライベート サブネットの仮想マシンにインストールしたオペレーティング システムに応じて、適切なコマンドを入力します。
        - **Windows**: コマンド プロンプトで、`tracert myvm-private` コマンドを実行します。
        - **Ubuntu**: `tracepath myvm-private` コマンドを実行します。
      トラフィックは、10.0.2.4 (NVA) を経由してから、10.0.1.4 (プライベート サブネットの仮想マシン) に到達します。 
    - 前の手順を完了するには、*myVm-Private* 仮想マシンに接続し、*myVm-Public* 仮想マシンに ping を実行します。 traceroute には、10.0.2.4 経由で 10.0.0.4 (パブリック サブネットの仮想マシン) に到達する通信が示されます。
    - **省略可能**: Azure Network Watcher の次ホップ機能を使用して、Azure 内で 2 つの仮想マシン間の次ホップを検証します。 Network Watcher を使用するには、使用するリージョンに対して [Azure Network Watcher インスタンスを作成](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)しておく必要があります。 このチュートリアルでは、米国東部リージョンが使用されます。 リージョンに対して Network Watcher インスタンスを有効にすると、次のコマンドを入力して、パブリック サブネットとプライベート サブネットの仮想マシン間の次ホップ情報を確認できます。
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       出力により *10.0.2.4* が **nextHopIpAddress** として、*VirtualAppliance* が **nextHopType** として返されます。

> [!NOTE]
> このチュートリアルでは、概念を説明するために、パブリック IP アドレスがパブリック サブネットとプライベート サブネットの仮想マシンに割り当てられ、Azure 内ですべてのネットワーク ポート アクセスが、両方の仮想マシンに対して有効になっています。 運用環境で使用する仮想マシンを作成するときは、ネットワーク仮想アプライアンスをプライベート サブネットの前にデプロイするか、ネットワーク セキュリティ グループをサブネットまたはネットワーク インターフェイス、あるいはその両方に割り当てることで、パブリック IP アドレスが仮想マシンに割り当てられないようにして、プライベート サブネットへのネットワーク トラフィックをフィルターします。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関するページをご覧ください。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

このチュートリアルでは、2 つのサブネットを持つ既存の仮想ネットワークが必要です。 仮想ネットワークをすばやく作成するには、次のボックスの **[お試しください]** ボタンをクリックします。 **[お試しください]** ボタンをクリックすると、[Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) が開きます。 Cloud Shell は PowerShell または Bash シェルを実行しますが、このセクションでは、Bash シェルを使用して仮想ネットワークを作成します。 Bash シェルには、Azure コマンド ライン インターフェイスがインストールされています。 Cloud Shell でメッセージが表示されたら、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して Azure にログインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。 このチュートリアルで使用される仮想ネットワークを作成するには、次のボックスの **[コピー]** をクリックし、スクリプトを Azure Cloud Shell に貼り付けます。

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

ポータル、PowerShell、または Azure Resource Manager テンプレートを使用して仮想マシンを作成する方法の詳細については、[仮想マシンの作成](virtual-networks-create-vnet-arm-pportal.md)に関するページをご覧ください。

## <a name="delete-resources"></a>リソースを削除する

このチュートリアルが完了したら、使用料がかからないように、作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。 PowerShell で次のコマンドを入力します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

- [可用性の高いネットワーク仮想アプライアンス](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成します。
- ネットワーク仮想アプライアンスには、多くの場合、複数のネットワーク インターフェイスと IP アドレスが割り当てられています。 [ネットワーク インターフェイスを既存の仮想マシンに追加](virtual-network-network-interface-vm.md#vm-add-nic)し、[IP アドレスを既存のネットワーク インターフェイスに追加](virtual-network-network-interface-addresses.md#add-ip-addresses)する方法を確認します。 すべての仮想マシンのサイズに 2 つ以上のネットワーク インターフェイスを接続できますが、各仮想マシンのサイズがサポートするのは、ネットワーク インターフェイスの最大数です。 各仮想マシンのサイズがサポートするネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンのサイズをご覧ください。 
- [サイト間 VPN 接続](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)によってオンプレミスのトンネル トラフィックを強制的に実施するユーザー定義ルートを作成します。
