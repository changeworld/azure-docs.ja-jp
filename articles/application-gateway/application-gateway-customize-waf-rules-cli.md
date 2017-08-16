---
title: "Azure Application Gateway での Web アプリケーション ファイアウォール ルールのカスタマイズ - Azure CLI 2.0 | Microsoft Docs"
description: "このページでは、Azure CLI 2.0 を使用して Application Gateway で Web アプリケーション ファイアウォール ルールをカスタマイズする方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cfc01caf5c8ca11f790a6640ec57e3d14c1b7767
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Azure CLI 2.0 を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Application Gateway Web アプリケーション ファイアウォールは、Web アプリケーションを保護します。 こうした保護は、OWASP CRS ルールセットによって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。  このため、アプリケーション ゲートウェイには、Web アプリケーション ファイアウォール対応アプリケーション ゲートウェイに対するルール グループとルールをカスタマイズする機能が用意されています。 特定のルール グループおよびルールの詳細については、[Web アプリケーション ファイアウォール CRS のルール グループおよびルール](application-gateway-crs-rulegroups-rules.md)に関するページをご覧ください

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

以下の例は、WAF 対応アプリケーション ゲートウェイに対して構成可能なルールとルール グループを表示する方法を示しています。

### <a name="view-rule-groups"></a>ルール グループの表示

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

以下は、前の例から返される応答を簡略化したものです。

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>ルール グループ内のルールの表示

次の例は、指定されたルール グループ内のルールを表示する方法を示しています。

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

以下の出力は、前の例から返される応答を簡略化したものです。

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>ルールの無効化

次の例は、アプリケーション ゲートウェイに対するルール `910018` および `910017` を無効にします。

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>次のステップ

無効になっているルールを構成したら、[Application Gateway 診断](application-gateway-diagnostics.md#diagnostic-logging)に関するページで WAF ログの表示方法を確認します

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
