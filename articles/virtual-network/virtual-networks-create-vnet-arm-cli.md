---
title: "Azure CLI を使用した仮想ネットワークの作成 | Microsoft Docs"
description: "Azure CLI を使用して仮想ネットワークを作成する方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: bd5f3b3cd46ce347896ed9ef229e438b2a3c830f
ms.openlocfilehash: 8e37a69f3263b6674b0e394f8a452272db5ebd0c


---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Azure CLI を使用した仮想ネットワークの作成

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure には、Azure Resource Manager とクラシックという 2 種類のデプロイメント モデルがあります。 Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../resource-manager-deployment-model.md)に関する記事を参照してください。
 
この記事では、Azure コマンドライン インターフェイスを使用して Resource Manager デプロイメント モデルで VNet を作成する方法について説明します。 他のツールを使用して Resource Manager で VNet を作成することや、次の一覧から別のオプションを選択してクラシック デプロイメント モデルで VNet を作成することもできます。

> [!div class="op_single_selector"]
- [ポータル](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [テンプレート](virtual-networks-create-vnet-arm-template-click.md)
- [ポータル (クラシック)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (クラシック)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (クラシック)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure CLI を使用して仮想ネットワークを作成するには、次の手順を実行します。

1. [Azure CLI のインストールと構成](../xplat-cli-install.md)に関する記事の手順に従って、Azure CLI をインストールして構成します。

2. 次のコマンドを実行して、VNet とサブネットを作成します。

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    予想される出力:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    使用されるパラメーター:

   * **--vnet**。 作成する VNet の名前です。 ここでは、 *TestVNet*
   * **-e (または --address-space)**。 VNet のアドレス空間です。 ここでは、 *192.168.0.0*
   * **-i (または -cidr)**。 CIDR 形式のネットワーク マスクです。 ここでは、 *16*です。
   * **-n (または --subnet-name**)。 最初のサブネットの名前です。 ここでは、 *FrontEnd*です。
   * **-p (または --subnet-start-ip)**。 サブネットの開始 IP アドレス、またはサブネット アドレス空間です。 ここでは、 *192.168.1.0*です。
   * **-r (または --subnet-cidr)**。 サブネットの CIDR 形式のネットワーク マスクです。 ここでは、 *24*です。
   * **-l (または --location)**。 VNet が作成される Azure リージョンです。 ここでは、 *Central US*です。
3. 次のコマンドを実行して、サブネットを作成します。

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    予想される出力:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    使用されるパラメーター:

   * **-t (または --vnet-name)**。 サブネットの作成先となる VNet の名前です。 ここでは、 *TestVNet*です。
   * **-n (または --name)**。 新しいサブネットの名前です。 ここでは、 *BackEnd*です。
   * **-a (または --address-prefix)**。 サブネットの CIDR ブロックです。 ここでは、 *192.168.2.0/24*です。
4. 次のコマンドを実行して新しい VNet のプロパティを表示します。

    ```azurecli
    azure network vnet show
    ```
   
    予想される出力:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>次のステップ

次の接続方法を確認してください。

- 仮想マシン (VM) を仮想ネットワークに接続する方法。[Linux VM の作成](../virtual-machines/virtual-machines-linux-quick-create-cli.md)に関する記事をご覧ください。 この記事の手順で VNet とサブネットを作成する代わりに、VM を接続する既存の VNet とサブネットを選択できます。
- 仮想ネットワークを他の仮想ネットワークに接続する方法。[VNet の接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事をご覧ください。
- サイト間の仮想プライベート ネットワーク (VPN) または ExpressRoute 回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続する方法。 詳しくは、[サイト間 VPN を使用したオンプレミスのネットワークへの VNet の接続](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関する記事と「[ExpressRoute 回線への VNet のリンク](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)」をご覧ください。


<!--HONumber=Nov16_HO3-->


