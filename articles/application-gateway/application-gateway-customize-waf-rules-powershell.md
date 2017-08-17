---
title: "Azure Application Gateway での Web アプリケーション ファイアウォール ルールのカスタマイズ - PowerShell | Microsoft Docs"
description: "このページでは、PowerShell を使用して Application Gateway で Web アプリケーション ファイアウォール ルールをカスタマイズする方法について説明します。"
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
ms.openlocfilehash: 7ed79af5654939c86ca9449be2a59aa0be1136dc
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-powershell"></a>PowerShell を使用した Web アプリケーション ファイアウォール ルールのカスタマイズ

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Application Gateway Web アプリケーション ファイアウォールは、Web アプリケーションを保護します。 こうした保護は、OWASP CRS ルールセットによって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。  このため、アプリケーション ゲートウェイには、Web アプリケーション ファイアウォール対応アプリケーション ゲートウェイに対するルール グループとルールをカスタマイズする機能が用意されています。 特定のルール グループおよびルールの詳細については、[Web アプリケーション ファイアウォール CRS のルール グループおよびルール](application-gateway-crs-rulegroups-rules.md)に関するページをご覧ください

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

以下の例は、WAF 対応アプリケーション ゲートウェイに対して構成可能なルールとルール グループを表示する方法を示しています。

### <a name="view-rule-groups"></a>ルール グループの表示

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

以下は、前の例から返される応答を簡略化したものです。

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>ルールの無効化

次の例は、アプリケーション ゲートウェイに対するルール `910018` および `910017` を無効にします。

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>次のステップ

無効になっているルールを構成したら、[Application Gateway 診断](application-gateway-diagnostics.md#diagnostic-logging)に関するページで WAF ログの表示方法を確認します

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
