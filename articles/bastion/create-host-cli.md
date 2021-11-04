---
title: Azure CLI を使用して Bastion ホストを作成する | Azure Bastion
description: Azure CLI で bastion ホストを作成、削除する方法を説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d855327b67d9918b62ab451259dd2c7025c885b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087155"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure CLI を使用して Azure Bastion ホストを作成する

この記事では、Azure CLI を使用して Azure Bastion ホストを作成する方法について示します。 お使いの仮想ネットワークに Azure Bastion サービスをプロビジョニングすると、同じ仮想ネットワークのすべての VM でシームレスに RDP/SSH をご利用いただけます。 Azure Bastion デプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

任意で、次の方法を使用して Azure Bastion ホストを作成できます。
* [Azure Portal](./tutorial-create-host-portal.md)
* [Azure PowerShell](bastion-create-host-powershell.md)

[!INCLUDE [About skus](../../includes/bastion-sku-note.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 > [!NOTE]
 > 現時点では、Azure プライベート DNS ゾーンでの Azure Bastion の使用はサポートされていません。 開始する前に、Bastion リソースをデプロイする予定の仮想ネットワークが、プライベート DNS ゾーンにリンクされていないことをご確認ください。
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションでは、Azure CLI を使用して新しい Azure Bastion リソースを作成する方法について説明します。

> [!NOTE]
> 例に示すように、すべてのコマンドで `--resource-group` と共に `--location` パラメーターを使用すると、リソースが確実にまとめてデプロイされます。

1. 仮想ネットワークと Azure Bastion サブネットを作成します。 名前の値として **AzureBastionSubnet** を使用して Azure Bastion サブネットを作成する必要があります。 この値によって、Azure でリソースをデプロイするサブネットを把握できます。 これは VPN ゲートウェイ サブネットとは異なります。

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Azure Bastion のパブリック IP アドレスを作成します。 このパブリック IP は (ポート 443 経由で) RDP/SSH がアクセスされる Bastion リソースのパブリック IP アドレスです。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. 仮想ネットワークの AzureBastionSubnet 内に新しい Azure Bastion リソースを作成します。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>VM のパブリック IP アドレスの関連付けの解除

Azure Bastion では、クライアント VM への接続にパブリック IP アドレスは使用されません。 VM のパブリック IP アドレスが不要な場合は、「[Azure VM からのパブリック IP アドレスの関連付けの解除](../virtual-network/ip-services/remove-public-ip-address-vm.md)」の記事の手順を使用して、パブリック IP アドレスの関連付けを解除できます。

## <a name="next-steps"></a>次のステップ

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。
* Azure Bastion サブネットでネットワーク セキュリティ グループを使用する方法については、[NSG の使用](bastion-nsg.md)に関するページを参照してください。
