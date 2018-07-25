---
title: Azure Service Bus の Virtual Network サービス エンドポイントと規則 | Microsoft Docs
description: 仮想ネットワークに Microsoft.ServiceBus サービス エンドポイントを追加します。
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: clemensv
ms.openlocfilehash: f8874a1d9db754485e9624596465560981bd6425
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858047"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Azure Service Bus で仮想ネットワーク サービス エンドポイントを使用する

Service Bus と[仮想ネットワーク (VNet) サービス エンドポイント][vnet-sep]の統合により、メッセージング機能へのアクセスを、仮想ネットワークにバインドされている仮想マシンなどのワークロードから保護し、ネットワーク トラフィック パスを両端で保護できます。 

少なくとも 1 つの仮想ネットワーク サブネット サービス エンドポイントにバインドするように構成した後、それぞれの Service Bus 名前空間は、承認された仮想ネットワークを除くどこからのトラフィックも受け入れなくなります。 仮想ネットワークの観点からは、サービス エンドポイントに Service Bus 名前空間をバインドすると、仮想ネットワーク サブネットからメッセージング サービスへの分離されたネットワーク トンネルが構成されます。

その結果、メッセージング サービス エンドポイントの監視可能なネットワーク アドレスがパブリック IP 範囲内にあるにもかかわらず、サブネットにバインドされたワークロードとそれぞれの Service Bus 名前空間の間にプライベートな分離された関係が確立されます。

## <a name="enable-service-endpoints-with-service-bus"></a>Service Bus でサービス エンドポイントを有効にする

仮想ネットワークは、[Premium レベル](service-bus-premium-messaging.md)の Service Bus 名前空間でのみサポートされます。 

Service Bus で VNet サービス エンドポイントを使用するときの重要な考慮事項は、Standard レベルと Premium レベルの Service Bus 名前空間が混在するアプリケーションではこれらのエンドポイントを有効にしないほうがよい、ということです。 Standard レベルは VNet をサポートせず、エンドポイントは Premium レベルの名前空間のみに限定されることがその理由です。 Standard 名前空間へのトラフィックは VNet によってブロックされます。 

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet の統合によって有効になる高度なセキュリティのシナリオ 

厳格でコンパートメント化されたセキュリティを要求し、仮想ネットワーク サブネットがコンパートメント化されたサービス間のセグメント化を提供するソリューションでも、一般に、それらのコンパートメント内に存在するサービス間の通信パスが必要です。

TCP/IP 上で HTTPS を搬送するものを含め、コンパートメント間の直接の IP ルートには、ネットワーク レイヤーから上のレイヤーの脆弱性を悪用されるリスクがあります。 メッセージング サービスは完全に隔離された通信パスを提供し、そこではメッセージがパーティ間を移動するときにディスクにも書き込まれます。 両方とも同じ Service Bus インスタンスにバインドされている 2 つの異なる仮想ネットワーク内のワークロードは、それぞれのネットワークの分離境界の整合性を維持しながら、メッセージを介して効率よく確実に通信できます。
 
つまり、セキュリティ対策されたクラウド ソリューションは、信頼性が高くスケーラブルな Azure の業界をリードする非同期メッセージング機能にアクセスできるだけでなく、メッセージングを使用して、セキュリティで保護されたソリューション コンパートメント間に通信パスを作成できます。この通信パスは、HTTPS やその他の TLS で保護されたソケット プロトコルを含むピアツーピア通信モードよりも本質的に安全です。

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus を仮想ネットワークにバインドする

"*仮想ネットワーク ルール*" は、Azure Service Bus サーバーが特定の仮想ネットワーク サブネットからの接続を許可するかどうかを制御するファイアウォール セキュリティ機能です。

仮想ネットワークへの Service Bus 名前空間のバインドは、2 ステップのプロセスです。 まず、仮想ネットワーク サブネットに**仮想ネットワーク サービス エンドポイント**を作成し、[サービス エンドポイントの概要][vnet-sep]に関する記事で説明しているように、"Microsoft.ServiceBus" に対して有効にする必要があります。 サービス エンドポイントを追加した後、Service Bus 名前空間を "*仮想ネットワーク ルール*" にバインドします。

仮想ネットワーク ルールは、Service Bus 名前空間と仮想ネットワーク サブネットの名前付きの関連付けです。 ルールが存在する間、サブネットにバインドされているすべてのワークロードには、Service Bus 名前空間へのアクセス権が付与されます。 Service Bus 自体は送信接続を確立することはなく、アクセス許可を取得する必要はないので、このルールを有効にすることでサブネットへのアクセス権が付与されることはありません。

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して仮想ネットワーク ルールを作成する

次の Resource Manager テンプレートでは、既存の Service Bus 名前空間に仮想ネットワーク ルールを追加できます。

テンプレート パラメーター:

* **namespaceName**: Service Bus 名前空間。
* **vnetRuleName**: 作成する Virtual Network ルールの名前。
* **virtualNetworkingSubnetId**: 仮想ネットワーク サブネットの Resource Manager の完全修飾パス。たとえば、仮想ネットワークの既定のサブネットの場合は `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` です。

テンプレート:

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次の手順

仮想ネットワークについて詳しくは、以下のリンクをご覧ください。

- [Azure 仮想ネットワーク サービス エンドポイント][vnet-sep]
- [Azure Service Bus IP フィルタリング][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md