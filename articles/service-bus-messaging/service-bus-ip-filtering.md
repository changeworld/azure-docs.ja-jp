---
title: Azure Service Bus のファイアウォール ルール | Microsoft Docs
description: ファイアウォール ルールを使用して、特定の IP アドレスから Azure Service Bus への接続を許可する方法です。
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9887d5448eabd272ab2528e4fc758265f2ada977
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980351"
---
# <a name="azure-service-bus---use-firewall-rules"></a>Azure Service Bus - ファイアウォール ルールを使用する

Azure Service Bus が特定の既知のサイトからのみアクセスできるシナリオでは、ファイアウォール ルールにより、特定の IPv4 アドレスからのトラフィックの受け入れのルールを構成することができます。 たとえば、これらのアドレスは、企業の NAT ゲートウェイのアドレスである可能性があります。

## <a name="when-to-use"></a>使用する場合

指定した IP アドレス範囲からのトラフィックのみを受信し、それ以外のすべてを拒否するように、Service Bus を設定する必要がある場合は、"*ファイアウォール*" を利用して Service Bus エンドポイントを他の IP アドレスからブロックすることができます。 たとえば、Service Bus を [Azure Express Route][express-route] と共に使用して、オンプレミス インフラストラクチャへのプライベート接続を作成する場合が該当します。 

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、Service Bus 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。

Service Bus 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。

## <a name="default-setting"></a>既定の設定

既定では、Service Bus のポータルの **[IP フィルター]** グリッドは空白になっています。 この既定の設定は、名前空間が任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

>[!WARNING]
> ファイアウォール ルールを実装すると、他の Azure サービスが Service Bus と対話するのを禁止できます。
>
> IP フィルター処理 (ファイアウォール ルール) が実装されているときは信頼できる Microsoft サービスはサポートされませんが、近日中に使用できるようになります。
>
> IP フィルター処理では動作しない Azure の一般的なシナリオは次のとおりです (網羅的なリストでは**ない**ことに注意してください)
> - Azure Stream Analytics
> - Azure Event Grid との統合
> - Azure IoT Hub ルート
> - Azure IoT Device Explorer
>
> 次の Microsoft サービスが仮想ネットワーク上に存在する必要があります
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して仮想ネットワークとファイアウォール ルールを作成する

> [!IMPORTANT]
> ファイアウォールと仮想ネットワークは、Service Bus の **Premium** レベルでのみサポートされます。

次の Resource Manager テンプレートでは、既存の Service Bus 名前空間に仮想ネットワーク規則を追加できます。

テンプレート パラメーター:

- **ipMask** は、1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックです。 たとえば、CIDR 表記では、70.37.104.0/24 は 70.37.104.0 から 70.37.104.255 までの 256 個の IPv4 アドレスを表し、24 は範囲に対する有効プレフィックス ビット数を示します。

> [!NOTE]
> 可能な拒否ルールはありませんが、Azure Resource Manager テンプレートには、接続を制限しない **"Allow"** に設定された既定のアクション セットがあります。
> 仮想ネットワークまたはファイアウォールのルールを作成するときは、***"defaultAction"*** を変更する必要があります。
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> から
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

テンプレートをデプロイするには、[Azure Resource Manager][lnk-deploy] の手順に従います。

## <a name="next-steps"></a>次のステップ

Service Bus へのアクセスを Azure 仮想ネットワークに 制約するには、次のリンクをご覧ください。

- [Service Bus の仮想ネットワーク サービス エンドポイント][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
