---
title: 既存の Azure Application Gateway で HTTP ヘッダーを書き換える
description: この記事では、Azure PowerShell を使用して、既存の Azure Application Gateway で HTTP ヘッダーを書き換える方法に関する情報を提供します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 8429618a945ec70c52b925887790aa469e23ef89
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730391"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイで HTTP ヘッダーを書き換える

Azure PowerShell を使用して、既存の[自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)で [HTTP 要求および応答ヘッダーを書き換えるルール](rewrite-http-headers.md)を構成できます

> [!IMPORTANT] 
> 自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU は、現在、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 既存のアプリケーション ゲートウェイの構成を取得する
> * http ヘッダーの書き換えルールの構成を指定する
> * http ヘッダーの書き換えのため、上の構成でアプリケーション ゲートウェイを更新する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Azure PowerShell をローカルで実行する必要があります。 Az モジュール バージョン 1.0.0 以降がインストールされている必要があります。 バージョンを確認するには、`Import-Module Az`、`Get-Module Az` の順に実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Login-AzAccount` を実行して Azure との接続を作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**http ヘッダーの書き換えルールの構成を指定する**

http ヘッダーの書き換えに必要な新しいオブジェクトを構成します。

- **RequestHeaderConfiguration**: このオブジェクトは、書き換えようとしている要求ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。
- **ResponseHeaderConfiguration**: このオブジェクトは、書き換えようとしている応答ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。
- **ActionSet**: このオブジェクトには、上で指定した要求ヘッダーと応答ヘッダーの構成が格納されます。 
- **RewriteRule**: このオブジェクトには、上で指定したすべての *ActionSet* が格納されます。 
- **RewriteRuleSet**: このオブジェクトは、すべての *RewriteRule* を含み、基本またはパス ベースの要求ルーティング規則にアタッチする必要があります。

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>既存のアプリケーション ゲートウェイの構成を取得する

```azurepowershell
$appgw1 = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>既存の要求ルーティング規則の構成を取得する

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>http ヘッダーの書き換えのためにこの構成でアプリケーション ゲートウェイを更新する

```azurepowershell
$appgw1 = Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
$appgw2 = Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
$appgw3 =  Set-AzApplicationGateway -ApplicationGateway $appgw2
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [URL パスベースのルーティング規則のあるアプリケーション ゲートウェイを作成する](./tutorial-url-route-powershell.md)
