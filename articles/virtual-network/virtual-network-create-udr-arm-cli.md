---
title: リソース マネージャーで Azure CLI を使用してルーティングを制御し、仮想アプライアンスを使用する | Microsoft Docs
description: Azure CLI を使用してルーティングを制御し仮想アプライアンスを使用する方法を説明する
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Azure CLI でユーザー定義のルート (UDR) を作成する
[!INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。[クラシック デプロイ モデルで UDR を作成](virtual-network-create-udr-classic-cli.md)することもできます。

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の Azure CLI のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。このドキュメントに表示されているコマンドを実行するには、まず[このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)をデプロイしてテスト環境を構築してから **[Azure にデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えてから、ポータルの指示に従います。

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. **`azure network route-table create`** コマンドを実行して、フロントエンドのサブネットのルート テーブルを作成します。
   
        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
   
    出力:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    パラメーター:
   
   * **-g (または --resource-group)**.UDR の作成場所となるリソース グループの名前です。ここでは、*TestRG* です。
   * **-l (または --location)**。新しい UDR が作成される Azure リージョンです。ここでは、*westus* です。
   * **-n (または --name)**。新しい UDR の名前です。ここでは、*UDR-FrontEnd* です。
2. バックエンドのサブネット (192.168.2.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、**`azure network route-table route create`** コマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。
   
        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
   
    出力:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    パラメーター:
   
   * **-r (または --route-table-name)**。ルートが追加されるルート テーブルの名前です。ここでは、*UDR-FrontEnd* です。
   * **-a (または --address-prefix)**。パケットの送信先であるサブネットのアドレス プレフィックスです。ここでは、*192.168.2.0/24* です。
   * **-y (または --next-hop-type)**。トラフィックの送信先となるオブジェクトの種類です。指定できる値は *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet*、または *None* です。
   * **-p (または --next-hop-ip-address**)。次のホップ先の IP アドレスです。ここでは、*192.168.0.4* です。
3. **`azure network vnet subnet set`** コマンドを実行して、上記で作成したルート テーブルを **FrontEnd** サブネットに関連付けます。
   
        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
   
    出力:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    パラメーター:
   
   * **-e (または --vnet-name)**。サブネットが置かれている VNet の名前です。ここでは、*TestVNet* です。

## バックエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. バックエンドのサブネットのルート テーブルを作成するには、**`azure network route-table create`** コマンドを実行します。
   
        azure network route-table create -g TestRG -n UDR-BackEnd -l westus
2. フロントエンドのサブネット (192.168.1.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、**`azure network route-table route create`** コマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。
   
        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
3. **`azure network vnet subnet set`** コマンドを実行して、上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。
   
        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## FW1 で IP 転送を有効にする
**FW1** で使用される NIC での IP 転送を有効にするには、次の手順に従います。

1. **`azure network nic show`** コマンドを実行します。**[IP 転送の有効化]**の値に注意します。この値は、*false* に設定されている必要があります。
   
        azure network nic show -g TestRG -n NICFW1
   
    出力:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. **`azure network nic set`** コマンドを実行して、IP 転送を有効にします。
   
        azure network nic set -g TestRG -n NICFW1 -f true
   
    出力:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    パラメーター:
   
   * **-f (または --enable-ip-forwarding)**。*true* または *false*。

<!---HONumber=AcomDC_0831_2016-->