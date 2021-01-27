---
title: 仮想マシン スケール セットで使用される既存の Azure Load Balancer を更新または削除する
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: このハウツー記事に従って、Azure Standard Load Balancer と Virtual Machine Scale Sets の使用を開始します。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790091"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets で使用される Azure Load Balancer を更新または削除する方法

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>仮想マシン スケール セットをスケールアウトするために Azure Load Balancer を設定する方法
  * Load Balancer に[インバウンド NAT プール](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest)が設定されていること、および仮想マシン スケール セットが Load Balancer のバックエンド プールに配置されていることを確認します。 新しい仮想マシン インスタンスが仮想マシン スケール セットに追加されると、Azure Load Balancer によって、インバウンド NAT プールに新しいインバウンド NAT 規則が自動的に作成されます。 
  * インバウンド NAT プールが適切に設定されているかどうかを確認するには、次の手順に従います。 
  1. Azure Portal ( https://portal.azure.com ) にサインインします。
  
  1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
  
  1. **[設定]** で、 **[インバウンド NAT 規則]** を選択します。
右ペインに、仮想マシン スケール セット内の個々のインスタンスごとに作成された規則の一覧が表示されている場合は、すべての設定が完了しているので、いつでもスケールアップできます。

## <a name="how-to-add-inbound-nat-rules"></a>インバウンド NAT 規則を追加する方法 
  * 個々のインバウンド NAT 規則を追加することはできません。 ただし、仮想マシン スケール セット内のすべてのインスタンスのフロントエンド ポート範囲とバックエンド ポートが定義されたインバウンド NAT 規則のセットを追加することは可能です。
  * 仮想マシン スケール セットのインバウンド NAT 規則のセット全体を追加するには、まず、Load Balancer でインバウンド NAT プールを作成し、次に、仮想マシン スケール セットのネットワーク プロファイルからインバウンド NAT プールを参照する必要があります。 CLI を使用した完全な例を次に示します。
  * 新しいインバウンド NAT プールのフロントエンド ポート範囲が、既存のインバウンド NAT プールと重複しないようにする必要があります。 既存のインバウンド NAT プールの設定を表示するには、この [CLI コマンド](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)を使用します。
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>インバウンド NAT 規則を更新する方法 
  * 個々のインバウンド NAT 規則を更新することはできません。 ただし、仮想マシン スケール セット内のすべてのインスタンスのフロントエンド ポート範囲とバックエンド ポートが定義されたインバウンド NAT 規則のセットを更新することは可能です。
  * 仮想マシン スケール セットのインバウンド NAT 規則のセット全体を更新するには、Load Balancer でインバウンド NAT プールを更新する必要があります。 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>インバウンド NAT 規則を削除する方法 
* 個々のインバウンド NAT 規則を削除することはできません。 ただし、インバウンド NAT 規則のセット全体を削除することは可能です。
* スケール セットで使用されているインバウンド NAT 規則のセット全体を削除するには、まず、スケール セットから NAT プールを削除する必要があります。 CLI を使用した完全な例を次に示します。
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>複数の IP 構成を追加する方法
1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[フロントエンド IP 構成]** を選択し、 **[追加]** を選択します。
   
1. **[フロントエンド IP アドレスの追加]** ページで値を入力し、 **[OK]** を選択します。

1. 新しい負荷分散規則が必要な場合は、このチュートリアルの[手順 5.](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) と[手順 6.](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) に従ってください。

1. 必要に応じて、新しく作成したフロントエンド IP 構成を使用して、インバウンド NAT 規則の新しいセットを作成します。 例については、[前のセクション] のこちらを参照してください。

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>仮想マシン スケール セットで使用されているフロントエンド IP 構成を削除する方法 
 1. スケール セットで使用されているフロントエンド IP 構成を削除するには、まず、フロントエンド IP 構成を参照するインバウンド NAT プール (インバウンド NAT 規則のセット) を削除する必要があります。 インバウンド規則を削除する方法については、前のセクションを参照してください。
 1. フロントエンド IP 構成を参照する負荷分散規則を削除します。 
 1. フロントエンド IP 構成を削除します。
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>仮想マシン スケール セットで使用されている Azure Load Balancer を削除する方法 
 1. スケール セットで使用されているフロントエンド IP 構成を削除するには、まず、フロントエンド IP 構成を参照するインバウンド NAT プール (インバウンド NAT 規則のセット) を削除する必要があります。 インバウンド規則を削除する方法については、前のセクションを参照してください。
 
 1. 仮想マシン スケール セットを含むバックエンド プールを参照する負荷分散規則を削除します。
 
 1. 仮想マシン スケール セットのネットワーク プロファイルから loadBalancerBackendAddressPool 参照を削除します。 CLI を使用した完全な例を次に示します。
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
最後に、Load Balancer リソースを削除します。
 
## <a name="next-steps"></a>次のステップ

Azure Load Balancer と仮想マシン スケール セットの詳細については、概念の詳細をご覧ください。

> [Azure Virtual Machine Scale Sets と Azure Load Balancer](load-balancer-standard-virtual-machine-scale-sets.md)