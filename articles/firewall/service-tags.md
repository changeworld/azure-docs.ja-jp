---
title: Azure Firewall サービス タグの概要
description: サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32d5daf67fe7ab30f3ac1b132bd74edd78e8712
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110700494"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall サービス タグ

サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Firewall サービス タグは、ネットワーク ルールの宛先フィールドで使用できます。 特定の IP アドレスの代わりにそれらを使用できます。

## <a name="supported-service-tags"></a>サポートされるサービス タグ

Azure ファイアウォール ネットワーク ルール内で使用できるサービス タグの一覧については、「[仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)」を参照してください。

## <a name="configuration"></a>構成

Azure Firewall では、PowerShell、Azure CLI、または Azure portal を使用したサービス タグの構成がサポートされています。

### <a name="configure-via-azure-powershell"></a>Azure PowerShell 経由で構成する

この例ではまず、前に作成した Azure Firewall インスタンスのコンテキストを取得する必要があります。

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

次に、新しい規則を作成する必要があります。  送信元または送信先には、この記事で前述したように、利用するサービス タグのテキスト値を指定できます。

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

次に、作成した新しいネットワーク規則で、Azure Firewall 定義を含む変数を更新する必要があります。

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

最後に、実行中の Azure Firewall インスタンスに対するネットワーク規則の変更内容をコミットする必要があります。
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>次のステップ

Azure Firewall ルールの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。
