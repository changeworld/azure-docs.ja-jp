---
title: Azure 内で IPv6 を使用して仮想マシン スケール セットをデプロイする
titlesuffix: Azure Virtual Network
description: この記事では、Azure 仮想ネットワーク内で IPv6 を使用して仮想マシン スケール セットをデプロイする方法を示します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164153"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Azure 内で IPv6 を使用して仮想マシン スケール セットをデプロイする (プレビュー)

この記事では、Azure 仮想ネットワーク内でデュアル スタック (IPv4 + IPv6) 仮想マシン スケール セットをデュアル スタック外部ロード バランサーと共にデプロイする方法を示します。 IPv6 対応の仮想マシン スケール セットを作成するプロセスは、[こちら](ipv6-configure-standard-load-balancer-template-json.md)で説明している個々の VM の作成プロセスとほぼ同じです。 個々の VM について説明したものと同様の手順に従って作業を開始します。
1.  IPv4 および IPv6 パブリック IP を作成します。
2.  デュアル スタック ロード バランサーを作成します。  
3.  ネットワーク セキュリティ グループ (NSG) のルールを作成します。  

個々の VM とは異なる手順は、仮想マシン スケール セット リソース (networkProfile/networkInterfaceConfigurations) を使用するネットワーク インターフェイス (NIC) 構成の作成だけです。 JSON 構造体は、個々の VM で使用される Microsoft.Network/networkInterfaces オブジェクトのものと同様ですが、次の例に示すように、 **"primary": true** 属性を使用した NIC と IPv4 IpConfiguration のプライマリ インターフェイスとしての設定が追加されています。

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>サンプル仮想マシン スケール セット テンプレートの JSON

デュアル スタック (IPv4 + IPv6) 仮想マシン スケール セットをデュアル スタック外部ロード バランサーおよび仮想ネットワーク ビューのサンプル テンプレートと共にデプロイするには、[こちら](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)をご覧ください。
## <a name="next-steps"></a>次のステップ

Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](ipv6-overview.md)に関するページを参照してください。
