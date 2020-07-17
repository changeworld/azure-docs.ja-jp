---
title: Azure Service Bus の IP ファイアウォール規則を構成する
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
ms.openlocfilehash: 9601689bbce9566b52664058911e9c45647152d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116820"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Azure Service Bus の IP ファイアウォール規則を構成する
既定では、要求が有効な認証と承認を受けている限り、Service Bus 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

この機能は、Azure Service Bus へのアクセスを特定の既知のサイトからのみに制限したいシナリオで役立ちます。 ファイアウォール規則を使用すると、特定の IPv4 アドレスから送信されたトラフィックを受け入れる規則を構成できます。 たとえば、[Azure Express Route][express-route] で Service Bus を使用する場合、お使いのオンプレミスのインフラストラクチャ IP アドレスまたは会社の NAT ゲートウェイのアドレスからのトラフィックのみ許可する**ファイアウォール規則**を作成できます。 

> [!IMPORTANT]
> ファイアウォールと仮想ネットワークは、Service Bus の **Premium** レベルでのみサポートされます。 **Premier** レベルへのアップグレードを選択できない場合は、Shared Access Signature (SAS) トークンのセキュリティを維持し、承認されたユーザーとのみ共有することをお勧めします。 SAS 認証については、[認証と承認](service-bus-authentication-and-authorization.md#shared-access-signature)に関するページを参照してください。

## <a name="ip-firewall-rules"></a>IP ファイアウォール規則
この IP ファイアウォール規則は、Service Bus 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Service Bus 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

>[!WARNING]
> ファイアウォール ルールを実装すると、他の Azure サービスが Service Bus と対話するのを禁止できます。
>
> IP フィルター処理 (ファイアウォール ルール) が実装されているときは信頼できる Microsoft サービスはサポートされませんが、近日中に使用できるようになります。
>
> IP フィルター処理では動作しない Azure の一般的なシナリオは次のとおりです (網羅的なリストでは**ない**ことに注意してください)
> - Azure Event Grid との統合
> - Azure IoT Hub ルート
> - Azure IoT Device Explorer
>
> 仮想ネットワーク上には、次の Microsoft サービスが必要です
> - Azure App Service
> - Azure Functions

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal を使用して、Service Bus 名前空間の IP ファイアウォール規則を作成する方法について説明します。 

1. [Azure portal](https://portal.azure.com) で、ご利用の **Service Bus 名前空間**に移動します。
2. 左側のメニューで、 **[ネットワーク]** オプションを選択します。 既定では、 **[すべてのネットワーク]** オプションが選択されています。 Service Bus 名前空間では、すべての IP アドレスからの接続を受け入れます。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。 

    ![ファイアウォールで [すべてのネットワーク] のオプションが選択されている](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. ページの上部で、 **[選択されたネットワーク]** オプションを選択します。 **[ファイアウォール]** セクションで、次の手順のようにします。
    1. 現在のクライアント IP にその名前空間へのアクセスを許可するには、 **[クライアント IP アドレスを追加する]** オプションを選択します。 
    2. **[アドレス範囲]** に、特定の IPv4 アドレスまたは IPv4 アドレスの範囲を CIDR 表記で入力します。 
    3. **信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する**かどうかを指定します。 

        ![ファイアウォールで [すべてのネットワーク] のオプションが選択されている](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. ツール バーの **[保存]** を選択して設定を保存します。 ポータルの通知に確認が表示されるまで、数分間お待ちください。

## <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用
このセクションには、仮想ネットワークとファイアウォール規則を作成するサンプル Azure Resource Manager テンプレートが含まれています。


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
          "trustedServiceAccessEnabled": false,          
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
