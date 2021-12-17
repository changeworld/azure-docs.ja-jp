---
title: プライベート エンドポイントのネットワーク ポリシーを管理する
titleSuffix: Azure Private Link
description: プライベート エンドポイントのネットワーク ポリシーを管理する方法について学習します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 08/26/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 03fa5b7e05a842addd88a890ee41829834910650
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036457"
---
# <a name="manage-network-policies-for-private-endpoints"></a>プライベート エンドポイントのネットワーク ポリシーを管理する

NSG (ネットワーク セキュリティ グループ) などのネットワーク ポリシーは、プライベート エンドポイントでは以前、サポートされていませんでした。 特定のサブネットにプライベート エンドポイントをデプロイするには、そのサブネット上で明示的な無効化設定が必要でした。 この設定は、プライベート エンドポイントにのみ適用されます。 サブネット内の他のリソースについては、ネットワーク セキュリティ グループのセキュリティ規則に基づいてアクセスが制御されます。

> [!IMPORTANT]
> プライベート エンドポイントに対する NSG と UDR のサポートは、一部のリージョンでパブリック プレビュー段階です。 詳細については、「[Private Link UDR のサポートのパブリック プレビュー](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/)」と「[Private Link ネットワーク セキュリティ グループのサポートのパブリック プレビュー](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/)」を参照してください。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ポータルを使用してプライベート エンドポイントを作成する場合、`PrivateEndpointNetworkPolicies` 設定は作成プロセスの一環として自動的に無効になります。 他のクライアントを使用したデプロイでは、この設定を変更するための追加の手順が必要になります。 

この設定は、次の方法で無効と有効を切り替えることができます。

* Azure portal の Cloud Shell。
* Azure PowerShell
* Azure CLI
* Azure Resource Manager のテンプレート
 
次の例では、仮想ネットワークの `PrivateEndpointNetworkPolicies` の無効と有効を切り替える方法を説明します。ここでは、**myResourceGroup** という名前のリソース グループでホストされる "**既定の**" サブネットを含む **myVNet** という名前の仮想ネットワークを想定しています。

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="disable-network-policy"></a>ネットワーク ポリシーを無効にする

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

### <a name="enable-network-policy"></a>ネットワーク ポリシーを有効にする

ここでは、Azure PowerShell を使用して、サブネットのプライベート エンドポイント ポリシーを有効にする方法について説明します。 ポリシーを有効にするには、[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) と [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用します。

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Enabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI

### <a name="disable-network-policy"></a>ネットワーク ポリシーを無効にする

ここでは、Azure CLI を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。 ポリシーを無効にするには、[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使用します。

```azurecli
az network vnet subnet update \
  --disable-private-endpoint-network-policies true \
  --name default \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork
  
```

### <a name="enable-network-policy"></a>ネットワーク ポリシーを有効にする

ここでは、Azure CLI を使用して、サブネットのプライベート エンドポイント ポリシーを有効にする方法について説明します。 ポリシーを有効にするには、[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使用します。

```azurecli
az network vnet subnet update \
  --disable-private-endpoint-network-policies false \
  --name default \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork
  
```
## <a name="resource-manager-template"></a>Resource Manager テンプレート

### <a name="disable-network-policy"></a>ネットワーク ポリシーを無効にする

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

### <a name="enable-network-policy"></a>ネットワーク ポリシーを有効にする

ここでは、Azure Resource Manager テンプレートを使用して、サブネットのプライベート エンドポイント ポリシーを有効にする方法について説明します。

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
                                    "privateEndpointNetworkPolicies": "Enabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイント](private-endpoint-overview.md)について学習します
 
