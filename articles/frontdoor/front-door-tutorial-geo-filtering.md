---
title: チュートリアル - Azure Front Door Service のドメインに geo フィルタリングを構成する | Microsoft Docs
description: このチュートリアルでは、単純な geo フィルタリング ポリシーを作成して、既存の Front Door フロントエンド ホストに関連付ける方法を説明します。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: c4032f7c33cec7b7a7864ccff07a05b87c945949
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988603"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Front Door に使用する geo フィルタリング ポリシーを設定する方法
このチュートリアルでは、Azure PowerShell を使用して、サンプル geo フィルタリング ポリシーを作成し、それを既存の Front Door フロントエンド ホストに関連付ける方法を説明します。 このサンプル geo フィルタリング ポリシーでは、他のすべての国 (米国を除く) からの要求がブロックされます。

## <a name="1-set-up-your-powershell-environment"></a>1.PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、AzureRM をインストールします。
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) のサポートは間もなく開始されます。

Front Door モジュールをインストールする前に、現在のバージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

AzureRM.FrontDoor モジュールをインストールします。 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2.geo フィルタリングの一致条件を定義する
まず、"米国" からではない要求を選択するサンプル一致条件を作成します。 一致条件を作成する際のパラメーターについては、PowerShell [ガイド](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject)を参照してください。 2 文字の国別コードと国とのマッピングについては、[こちら](/Protection/GeoFiltering)を参照してください。

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>手順 3.アクションおよび優先度と共に geo フィルタリングの一致条件をルールに追加する

次に、一致条件、アクション、優先度に基づく CustomRule オブジェクト `nonUSBlockRule` を作成します。  CustomRule には、複数の MatchCondition を含めることができます。  この例では、Action を Block に、Priority を 1 (最高優先度) に設定しています。

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

CustomRuleObject を作成する際のパラメーターについては、PowerShell [ガイド](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject)を参照してください。

## <a name="4-add-rules-to-a-policy"></a>4.ポリシーにルールを追加する
この手順では、前の手順で得た `nonUSBlockRule` を含む `geoPolicy` ポリシー オブジェクトを指定のリソース グループに作成します。 実際の ResourceGroupName $resourceGroup については、`Get-AzureRmResourceGroup` を使用して検索してください。

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

ポリシーを作成する際のパラメーターについては、PowerShell [ガイド](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy)を参照してください。

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5.Front Door フロントエンド ホストにポリシーをリンクさせる
最後の手順は、保護ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクさせて、Front Door のプロパティを更新することです。 まず、[Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor) を使用して Front Door オブジェクトを取得した後、そのフロントエンド WebApplicationFirewallPolicyLink プロパティを `geoPolicy` の resourceId に設定します。

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

次の[コマンド](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor)を使用して Front Door オブジェクトを更新します。

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Front Door フロントエンド ホストに保護ポリシーをリンクさせるために必要な WebApplicationFirewallPolicyLink プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンド ホストに適用されます。

## <a name="next-steps"></a>次の手順

- [Front Door によるアプリケーション層セキュリティ](front-door-application-security.md)について学習します。
- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
