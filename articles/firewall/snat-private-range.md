---
title: Azure Firewall の SNAT プライベート IP アドレス範囲
description: SNAT の IP アドレス範囲を構成できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/01/2020
ms.author: victorh
ms.openlocfilehash: 28ec61c4aefeacb8014e0a5d48d0259cf7fcf7f3
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267029"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall の SNAT プライベート IP アドレス範囲

Azure Firewall では、パブリック IP アドレスへのすべてのアウトバウンド トラフィックに対して自動 SNAT が提供されます。 既定では、宛先 IP アドレスが [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) に従ったプライベート IP アドレス範囲内にある場合、Azure Firewall では、ネットワーク規則を使用した SNAT は行われません。 アプリケーション ルールは、宛先 IP アドレスに関係なく、[透過プロキシ](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)を使用して常に適用されます。

このロジックは、トラフィックをインターネットに直接ルーティングする場合に適しています。 ただし、[強制トンネリング](forced-tunneling.md)を有効にすると、SNAT によってインターネットへのトラフィックに AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスが適用され、対象のオンプレミスのファイアウォールからソースが隠されます。

組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 ただし、パブリック IP アドレス範囲の SNAT が**行われない**ように、Azure Firewall を構成することができます。

宛先 IP アドレスに関係なく SNAT を行わないように Azure Firewall を構成するには、**0.0.0.0/0** をプライベート IP アドレス範囲として使用します。 この構成では、Azure Firewall がトラフィックをインターネットに直接ルーティングすることはできません。 宛先アドレスに関係なく常に SNAT を行うようにファイアウォールを構成するには、**255.255.255.255/32** をプライベート IP アドレス範囲として使用します。

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure PowerShell

Azure PowerShell を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

### <a name="new-firewall"></a>新しいファイアウォール

新しいファイアウォールの場合、Azure PowerShell のコマンドは次のようになります。

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges は Azure Firewall の現在の既定値に拡張されますが、他の範囲は追加されます。

詳細については、「[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)」をご覧ください。

### <a name="existing-firewall"></a>既存のファイアウォール

既存のファイアウォールを構成するには、次の Azure PowerShell コマンドを使います。

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>テンプレート

`additionalProperties` セクションに以下を追加できます。

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure portal

Azure portal を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

1. 対象のリソース グループを選択し、ファイアウォールを選択します。
2. **[概要]** ページの **[プライベート IP 範囲]** で、既定値 **[IANA RFC 1918]** を選択します。

   **[Edit Private IP Prefixes]\(プライベート IP プレフィックスの編集\)** ページが開きます。

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="[Edit private IP prefixes]\(プライベート IP プレフィックスの編集\)":::

1. 既定では、 **[IANAPrivateRanges]** が構成されています。
2. お使いの環境に合わせてプライベート IP アドレス範囲を編集し、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall の強制トンネリング](forced-tunneling.md)について学習します。