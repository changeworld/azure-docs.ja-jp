---
title: "ネットワーク仮想アプライアンス経由でネットワーク トラフィックをルーティングするユーザー定義ルートを作成する - Azure CLI | Microsoft Docs"
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
ms.openlocfilehash: b41754e74708a095d95eee31da5e304771a6ae05
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>ユーザー定義ルートを作成する - Azure CLI

このチュートリアルでは、ネットワーク仮想アプライアンスを通じて 2 つの[仮想ネットワーク](virtual-networks-overview.md) サブネット間でトラフィックをルーティングするユーザー定義ルートを作成する方法を説明します。 ネットワーク仮想アプライアンスは、ファイアウォールなどのネットワーク アプリケーションが実行されている仮想マシンです。 Azure 仮想ネットワークにデプロイできる事前構成されたネットワーク仮想アプライアンスの詳細については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) のページをご覧ください。

仮想ネットワークでサブネットを作成すると、次の図に示すように、既定の[システム ルート](virtual-networks-udr-overview.md#system-routes)が作成され、すべてのサブネットのリソースが相互に通信できます。

![既定のルート](./media/create-user-defined-route/default-routes.png)

このチュートリアルでは、次の図に示すように、パブリック サブネット、プライベート サブネット、および DMZ サブネットを使用して仮想ネットワークを作成します。 一般的には、パブリック サブネットには Web サーバーが、プライベート サブネットにはアプリケーションまたはデータベース サーバーがデプロイされます。 DMZ サブネットには、ネットワーク仮想アプライアンスとして機能する仮想マシンを作成し、必要に応じて、ネットワーク仮想アプライアンス経由で通信する仮想マシンを各サブネットに作成します。 次の図に示すように、パブリック サブネットとプライベート サブネットの間のトラフィックすべてが、アプライアンス経由でルーティングされます。

![ユーザー定義のルート](./media/create-user-defined-route/user-defined-routes.png)

この記事では、Resource Manager デプロイメント モデルを使用してユーザー定義ルートを作成する手順について説明します。ユーザー定義ルートを作成するときは、このデプロイメント モデルを使用することをお勧めします。 ユーザー定義ルート (クラシック) を作成する必要がある場合は、[ユーザー定義ルート (クラシック) の作成](virtual-network-create-udr-classic-cli.md)に関するページをご覧ください。 Azure 展開モデルの知識がない場合は、[Azure 展開モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの概要](virtual-networks-udr-overview.md#user-defined-routes)に関する記事をご覧ください。

## <a name="create-routes-and-network-virtual-appliance"></a>ルートとネットワーク仮想アプライアンスの作成

Windows、Linux、または macOS のどこからコマンドを実行しても、Azure CLI コマンドは同じです。 ただし、オペレーティング システム シェル間ではスクリプトに違いがあります。 以下の手順のスクリプトは、必要な Azure CLI コマンドのインストールと実行を Bash シェルで行います。 PC で [Azure CLI をインストールして構成する](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)か、スクリプトの **[お試しください]** ボタンをクリックして Azure Cloud Shell でスクリプトを実行することができます。
 
1. **前提条件**: 「[仮想ネットワークの作成](#create-a-virtual-network)」の手順を実行して、2 つのサブネットで仮想ネットワークを作成します。
2. コンピューターから Azure CLI を実行する場合は、`az login` コマンドを使用して Azure にログインします。 Cloud Shell を使用する場合は、自動的にログインされます。
3. 残りの手順で使用される変数をいくつか設定します。

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. 前提条件で作成した仮想ネットワークに *DMZ* サブネットを作成します。

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. NVA 仮想マシンを作成します。 CLI によって作成されるネットワーク インターフェイスに、パブリックおよびプライベートの静的 IP アドレスを割り当てます。 静的アドレスは、仮想マシンの有効期間中は変わりません。 NVA には、Linux または Windows オペレーティング システムが実行されている仮想マシンを指定できます。 仮想マシンを作成するには、いずれかのオペレーティング システムのスクリプトをコピーし、CLI に貼り付けます。 Windows VM を作成している場合は、スクリプトをテキスト エディターに貼り付けて、*AdminPassword* 変数の値を変更し、変更したテキストを CLI に貼り付けます。

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. NVA のネットワーク インターフェイスで IP 転送を有効にします。 ネットワーク インターフェイスで IP 転送を有効にすると、Azure は送信元/送信先 IP アドレスをチェックしなくなります。 この設定を有効にしないと、トラフィックを受信する NIC 以外の IP アドレスを宛先とするトラフィックが、Azure によって破棄されます。

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. "*パブリック*" サブネットのルート テーブルを作成します。

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. 既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックがルーティングされます。 ルートを作成することで、Azure の既定のルーティングを変更します。パブリック サブネットからプライベート サブネットへのトラフィックは、直接ではなく、NVA 経由でプライベート サブネットにルーティングされます。

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. *myRouteTable-Public* ルート テーブルを "*パブリック*" サブネットに関連付けます。 ルート テーブルをサブネットに関連付けると、サブネットからのすべての送信トラフィックが、ルート テーブルのルートに従ってルーティングされます。 ルート テーブルは 0 個または複数のサブネットに関連付けることができますが、サブネットに関連付けることができるルート テーブルは 0 個または 1 つだけです。

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. "*プライベート*" サブネットのルート テーブルを作成します。

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. "*プライベート*" サブネットから "*パブリック*" サブネットへのトラフィックを NVA 仮想マシン経由でルーティングするためのルートを作成します。

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. ルート テーブルを "*プライベート*" サブネットに関連付けます。

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **省略可能:** パブリック サブネットとプライベート サブネットに仮想マシンを作成し、「[ルーティングの検証](#validate-routing)」の手順を実行して、仮想マシン間の通信がネットワーク仮想アプライアンス経由でルーティングされていることを検証します。
14. **省略可能**: 「[リソースの削除](#delete-resources)」の手順を実行して、このチュートリアルで作成したリソースを削除します。

## <a name="validate-routing"></a>ルーティングの検証

1. 「[ルートとネットワーク仮想アプライアンスの作成](#create-routes-and-network-virtual-appliance)」の手順を実行します (まだ実行していない場合)。
2. 次のボックスの **[お試しください]** ボタンをクリックすると、Azure Cloud Shell が開きます。 メッセージが表示されたら、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して Azure にログインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。 Azure Cloud Shell は、Azure コマンド ライン インターフェイスがプレインストールされた無料の Bash シェルです。 

    次のスクリプトにより、"*パブリック*" サブネットと "*プライベート*" サブネットに 1 つずつ、2 つの仮想マシンが作成されます。 また、このスクリプトによって、NVA のオペレーティング システム内のネットワーク インターフェイスで IP 転送が有効になり、オペレーティング システムが、ネットワーク インターフェイス経由でトラフィックをルーティングできるようになります。 運用 NVA は、通常、ルーティング前にトラフィックを検査しますが、このチュートリアルのシンプルな NVA は、検査せずにトラフィックをルーティングします。 

    次の **Linux** または **Windows** のスクリプトで **[コピー]** をクリックし、スクリプトの内容をテキスト エディターに貼り付けます。 *adminPassword* 変数のパスワードを変更し、スクリプトを Azure Cloud Shell に貼り付けます。 「[ルートとネットワーク仮想アプライアンスの作成](#create-routes-and-network-virtual-appliance)」の手順 5. でネットワーク仮想アプライアンスを作成したときに選択したオペレーティング システムに対して、スクリプトを実行します。 

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

ポータル、PowerShell、または Azure Resource Manager テンプレートを使用して仮想ネットワークを作成する方法の詳細については、[仮想マシンの作成](virtual-networks-create-vnet-arm-pportal.md)に関するページをご覧ください。

## <a name="delete-resources"></a>リソースを削除する

このチュートリアルが完了したら、使用料がかからないように、作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。 CLI セッションで次のコマンドを入力します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

- [可用性の高いネットワーク仮想アプライアンス](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成します。
- ネットワーク仮想アプライアンスには、多くの場合、複数のネットワーク インターフェイスと IP アドレスが割り当てられています。 [ネットワーク インターフェイスを既存の仮想マシンに追加](virtual-network-network-interface-vm.md#vm-add-nic)し、[IP アドレスを既存のネットワーク インターフェイスに追加](virtual-network-network-interface-addresses.md#add-ip-addresses)する方法を確認します。 すべての仮想マシンのサイズに 2 つ以上のネットワーク インターフェイスを接続できますが、各仮想マシンのサイズがサポートするのは、ネットワーク インターフェイスの最大数です。 各仮想マシンのサイズがサポートするネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンのサイズをご覧ください。 
- [サイト間 VPN 接続](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)によってオンプレミスのトンネル トラフィックを強制的に実施するユーザー定義ルートを作成します。
