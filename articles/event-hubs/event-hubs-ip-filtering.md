---
title: Azure Event Hubs の IP 接続フィルター | Microsoft Docs
description: IP フィルター処理を使用して特定の IP アドレスから Azure Event Hubs への接続をブロックします。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 0ecce667584f522b5bd6aac28291bda427f37608
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005354"
---
# <a name="use-ip-filters"></a>IP フィルターの使用

Azure Event Hubs が特定の既知のサイトからのみアクセスできるシナリオでは、"*IP フィルター*" 機能により、特定の IPv4 アドレスからのトラフィックの拒否または受け入れのルールを構成することができます。 たとえば、これらのアドレスは、企業の NAT ゲートウェイのアドレスである可能性があります。

## <a name="when-to-use"></a>いつ使用するか

特定の IP アドレスの Event Hubs エンドポイントをブロックすると有用な 2 つの重要なユース ケースは次のとおりです。

- イベント ハブが指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外のトラフィックをすべて拒否する必要がある場合。 たとえば、Event Hubs を [Azure Express Route][express-route] と共に使用して、オンプレミス インフラストラクチャへのプライベート接続を作成する場合が該当します。 
- Event Hubs の管理者によって疑わしいと識別された IP アドレスからのトラフィックを拒否する必要がある場合。

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、Event Hubs 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。

Event Hubs 名前空間上の拒否 IP 規則に一致する IP アドレスからの接続試行は未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。

## <a name="default-setting"></a>既定の設定

既定では、ポータルの Event Hubs の **[IP フィルター]** は空白になっています。 この既定の設定は、イベント ハブが任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

たとえば、範囲 70.37.104.0/24 内のアドレスを許可し、それ以外のアドレスをすべて拒否するには、グリッドの最初の規則でアドレス範囲 70.37.104.0/24 を許可する必要があります。 さらに、次の規則で範囲 0.0.0.0/0 を使用してすべてのアドレスを拒否する必要があります。

> [!NOTE]
> IP アドレスを拒否すると、他の Azure サービス (Azure Stream Analytics、Azure Virtual Machines、ポータルの Device Explorer など) が Event Hubs と対話できなくなる可能性があります。

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して仮想ネットワーク ルールを作成する

次の Resource Manager テンプレートでは、既存の Event Hubs 名前空間に仮想ネットワーク ルールを追加できます。

テンプレート パラメーター:

- **ipFilterRuleName** は一意であり、長さは最大 128 文字の大文字と小文字を区別しない英数字である必要があります。
- **ipFilterAction** は IP フィルター規則に適用するアクションとして **Reject** または **Accept** である必要があります。
- **ipMask** は、1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックです。 たとえば、CIDR 表記では、70.37.104.0/24 は 70.37.104.0 から 70.37.104.255 までの 256 個の IPv4 アドレスを表し、24 は範囲に対する有効プレフィックス ビット数を示します。

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
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次の手順

Event Hubs へのアクセスを Azure 仮想ネットワークに制約するには、次のリンクをご覧ください。

- [Event Hubs の仮想ネットワーク サービス エンドポイント][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md