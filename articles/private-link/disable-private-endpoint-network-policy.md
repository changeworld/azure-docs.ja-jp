---
title: プライベート エンドポイントのネットワーク ポリシーを無効にする
titleSuffix: Azure Private Link
description: プライベート エンドポイントのネットワーク ポリシーを無効にする方法について学習します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 07/14/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90bb1584c94f58d96151e32c7b9c9e02989a840d
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634424"
---
# <a name="disable-network-policies-for-private-endpoints"></a>プライベート エンドポイントのネットワーク ポリシーを無効にする

NSG (ネットワーク セキュリティ グループ) などのネットワーク ポリシーは、プライベート エンドポイントではサポートされません。 特定のサブネットにプライベート エンドポイントをデプロイするには、そのサブネット上で明示的な無効化設定が必要です。 この設定は、プライベート エンドポイントにのみ適用されます。 サブネット内の他のリソースについては、ネットワーク セキュリティ グループのセキュリティ規則に基づいてアクセスが制御されます。
 
ポータルを使用してプライベート エンドポイントを作成する場合、`PrivateEndpointNetworkPolicies` 設定は作成プロセスの一環として自動的に無効になります。 他のクライアントを使用したデプロイでは、この設定を変更するための追加の手順が必要になります。 

この設定は、次の方法で無効にすることができます。

* Azure portal の Cloud Shell。
* Azure PowerShell
* Azure CLI
* Azure Resource Manager のテンプレート
 
次の例では、仮想ネットワークの `PrivateEndpointNetworkPolicies` を無効にする方法を説明します。ここでは、**myResourceGroup** という名前のリソース グループでホストされる "**既定の**" サブネットを含む **myVNet** という名前の仮想ネットワークを想定しています。

## <a name="azure-powershell"></a>Azure PowerShell

ここでは、Azure PowerShell を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。 ポリシーを無効にするには、[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) と [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用します。

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI
ここでは、Azure CLI を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。 ポリシーを無効にするには、[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使用します。

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Resource Manager テンプレート
ここでは、Azure Resource Manager テンプレートを使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイント](private-endpoint-overview.md)について学習します
 
