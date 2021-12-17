---
title: Azure PowerShell を使用して Bastion ホストを作成する | Microsoft Docs
description: PowerShell を使用して Azure Bastion ホストを作成する方法を説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: faa6fe1b7fdccf81f83b591c1f0258d27dc9fdd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071528"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure PowerShell を使用して Azure Bastion ホストを作成する

この記事では、PowerShell を利用し、Azure Bastion ホストを作成する方法について示します。 お使いの仮想ネットワークに Azure Bastion サービスをプロビジョニングすると、同じ仮想ネットワークのすべての VM でシームレスに RDP/SSH をご利用いただけます。 Azure Bastion デプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

任意で、次の方法を使用して Azure Bastion ホストを作成できます。
* [Azure Portal](./tutorial-create-host-portal.md)
* [Azure CLI](create-host-cli.md)

[!INCLUDE [About SKUs](../../includes/bastion-sku-note.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 > [!NOTE]
 > 現時点では、Azure プライベート DNS ゾーンでの Azure Bastion の使用はサポートされていません。 開始する前に、Bastion リソースをデプロイする予定の仮想ネットワークが、プライベート DNS ゾーンにリンクされていないことをご確認ください。
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションは、Azure PowerShell を使用して新しい Azure Bastion リソースを作成するのに役立ちます。

1. 仮想ネットワークと Azure Bastion サブネットを作成します。 名前の値として **AzureBastionSubnet** を使用して Azure Bastion サブネットを作成する必要があります。 この値によって、Azure でリソースをデプロイするサブネットを把握できます。 これは VPN ゲートウェイ サブネットとは異なります。

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Azure Bastion のパブリック IP アドレスを作成します。 このパブリック IP は (ポート 443 経由で) RDP/SSH がアクセスされる Bastion リソースのパブリック IP アドレスです。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 仮想ネットワークの AzureBastionSubnet 内に新しい Azure Bastion リソースを作成します。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>VM のパブリック IP アドレスの関連付けの解除

Azure Bastion では、クライアント VM への接続にパブリック IP アドレスは使用されません。 VM のパブリック IP アドレスが不要な場合は、「[Azure VM からのパブリック IP アドレスの関連付けの解除](../virtual-network/ip-services/remove-public-ip-address-vm.md)」の記事の手順を使用して、パブリック IP アドレスの関連付けを解除できます。

## <a name="next-steps"></a>次のステップ

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。
* Azure Bastion サブネットでネットワーク セキュリティ グループを使用する方法については、[NSG の使用](bastion-nsg.md)に関するページを参照してください。
