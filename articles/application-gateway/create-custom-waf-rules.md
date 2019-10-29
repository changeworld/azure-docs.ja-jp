---
title: Azure Web アプリケーション ファイアウォール (WAF) v2 のカスタム規則を作成して使用する
description: この記事では、Azure Application Gateway で Web アプリケーション ファイアウォール (WAF) v2 のカスタム規則を作成する方法について説明します。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8cf82ce9ed4a9dc701c016f15224d6adfa299736
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263585"
---
# <a name="create-and-use-web-application-firewall-v2-custom-rules"></a>Web アプリケーション ファイアウォール v2 のカスタム規則を作成して使用する

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) v2 は、Web アプリケーションの保護を提供します。 この保護は、Open Web Application Security Project (OWASP) コア規則セットによって提供されます。 場合によっては、特定のニーズを満たすために、独自のカスタム規則を作成する必要があります。 WAF のカスタム規則の詳細については、[Web アプリケーション ファイアウォールのカスタム規則の概要](custom-waf-rules-overview.md)に関する記事を参照してください。

この記事では、WAF v2 で作成して使用できるカスタム規則のいくつかの例を示しています。 Azure PowerShell を使って、カスタム規則で WAF をデプロイする方法については、[Azure PowerShell を使用した Web アプリケーション ファイアウォールのカスタム規則の構成](configure-waf-custom-rules.md)に関する記事を参照してください。

> [!NOTE]
> アプリケーション ゲートウェイが WAF 層を使用していない場合は、アプリケーション ゲートウェイを WAF 層にアップグレードするオプションが右側のウィンドウに表示されます。

![WAF を有効にする][fig1]

## <a name="example-1"></a>例 1

*evilbot* という名前のボットがあることが分かっており、このボットによる Web サイトのクロールをブロックしたいとします。 この例では、要求ヘッダーで User-Agent の *evilbot* をブロックします。

ロジック: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

対応する JSON コードを次に示します。

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

このカスタム規則を使用してデプロイされた WAF を表示するには、[Azure PowerShell を使用した Web アプリケーション ファイアウォールのカスタム規則の構成](configure-waf-custom-rules.md)に関する記事を参照してください。

### <a name="example-1a"></a>例 1a

正規表現を使用して同じ処理を行うことができます。

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

対応する JSON コードを次に示します。

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>例 2

範囲 198.168.5.0/24 内の IP アドレスからのすべての要求をブロックしたいとします。

この例では、ある IP アドレス範囲が発信元であるすべてのトラフィックをブロックします。 規則の名前は *myrule1* で、優先度は 100 に設定されています。

ロジック: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

対応する JSON コードを次に示します。

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      }
    ]
  }
```

対応するコア規則セットの規則を次に示します。

  `SecRule REMOTE_ADDR "@ipMatch 192.168.5.0/24" "id:7001,deny"`

## <a name="example-3"></a>例 3

この例では、User-Agent *evilbot* と、範囲 192.168.5.0/24 内のトラフィックをブロックしたいとします。 この結果を得るためには、2 つの別々の一致条件を作成し、それらを両方とも同じ規則に入れます。 この方法を使用すると、User-Agent ヘッダー内の *evilbot* *と*範囲 192.168.5.0/24 の IP アドレスの両方が一致すると、要求が確実にブロックされます。

ロジック: p *かつ* q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

対応する JSON コードを次に示します。

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 10, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": false, 
              "matchValues": [ 
                "192.168.5.0/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-4"></a>例 4

この例では、要求が IP アドレス範囲 *192.168.5.0/24* の外の場合、またはユーザー エージェント文字列が *chrome* でない (つまり、ユーザーが Chrome ブラウザーを使用していない) 場合にブロックしたいとします。 このロジックでは "*または*" を使用しているため、次の例に示すように、2 つの条件は別々の規則内にあります。 トラフィックをブロックするには、*myrule1* と *myrule2* の両方が一致する必要があります。

ロジック: (p *かつ* q) *でない* = p *でない*または q *でない*

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.0/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

対応する JSON コードを次に示します。

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.0/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-5"></a>例 5

カスタム SQLI をブロックしたいとします。 ここで使用されるロジックは "*または*" であり、すべての値は *RequestUri* 内にあるため、すべての *MatchValue* はコンマ区切りリストに含めることができます。

ロジック: p *または* q *または* r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

対応する JSON コードを次に示します。

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 100
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

代替の Azure PowerShell コードを次に示します。

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 10 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 20 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 30 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

対応する JSON コードを次に示します。

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 10,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 20,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 30,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="next-steps"></a>次の手順

カスタム規則を作成したら、WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md#diagnostic-logging)に関するトピックを参照してください。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
