---
title: Azure Firewall の SNAT プライベート IP アドレス範囲
description: SNAT の IP アドレス範囲を構成できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6235e5ec34987c0a383ea776aabf0a00e345ce63
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693686"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall の SNAT プライベート IP アドレス範囲

Azure Firewall では、パブリック IP アドレスへのすべてのアウトバウンド トラフィックに対して自動 SNAT が提供されます。 既定では、宛先の IP アドレスが [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) のプライベート IP アドレスの範囲または [IANA RFC 6598](https://tools.ietf.org/html/rfc6598) の共有アドレス スペースに含まれているときは、Azure Firewall でネットワーク ルールによる SNAT を行いません。 アプリケーション ルールは、宛先 IP アドレスに関係なく、常に[透過プロキシ](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)を使用して適用されます。

このロジックは、トラフィックをインターネットに直接ルーティングする場合に適しています。 ただし、[強制トンネリング](forced-tunneling.md)を有効にすると、SNAT によってインターネットへのトラフィックに AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスが適用され、対象のオンプレミスのファイアウォールからソースが隠されます。

組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 ただし、パブリック IP アドレス範囲の SNAT が **行われない** ように、Azure Firewall を構成することができます。 たとえば、個々の IP アドレスを指定するには、`192.168.1.10` のように指定します。 IP アドレスの範囲を指定するには、`192.168.1.0/24` のように指定します。

- 宛先 IP アドレスに関係なく、SNAT を **行わない** ように Azure Firewall を構成するには、プライベート IP アドレス範囲として **0.0.0.0/0** を使用します。 この構成では、Azure Firewall がトラフィックをインターネットに直接ルーティングすることはできません。 

- 宛先アドレスに関係なく、**常に** SNAT を行うようにファイアウォールを構成するには、プライベート IP アドレス範囲として **255.255.255.255/32** を使用します。

> [!IMPORTANT]
> 指定したプライベート アドレス範囲は、ネットワーク規則にのみ適用されます。 現時点では、アプリケーション ルールでは常に SNAT が使用されます。

> [!IMPORTANT]
> 独自のプライベート IP アドレス範囲を指定し、既定の IANA RFC 1918 アドレス範囲をそのまま使用する場合は、カスタム リストに IANA RFC 1918 の範囲がまだ含まれていることを確認してください。 

SNAT のプライベート IP アドレスを構成するには、次の方法を使用します。 SNAT のプライベート アドレスは、ご自身の構成に適した方法を使用して構成する必要があります。 ファイアウォール ポリシーに関連するファイアウォールでは、ポリシーで範囲を指定する必要があり、`AdditionalProperties` は使用しないでください。


|メソッド            |クラシック ルールの使用  |ファイアウォール ポリシーの使用  |
|---------|---------|---------|
|Azure portal     | [サポート対象](#classic-rules-3)| [サポート対象](#firewall-policy-1)|
|Azure PowerShell     |[`PrivateRange` を構成する](#classic-rules)|現在はサポートされていません|
|Azure CLI|[`--private-ranges` を構成する](#classic-rules-1)|現在はサポートされていません|
|ARM テンプレート     |[ファイアウォール プロパティに `AdditionalProperties` を構成する](#classic-rules-2)|[ファイアウォール ポリシーに `snat/privateRanges` を構成する](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure PowerShell
### <a name="classic-rules"></a>クラシック ルール

Azure PowerShell を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

> [!NOTE]
> ファイアウォール ポリシーに関連するファイアウォールでは、ファイアウォールの `PrivateRange` プロパティは無視されます。 「[SNAT のプライベート IP アドレス範囲を構成する - ARM テンプレート](#firewall-policy)」で説明されているように、`firewallPolicies` で `SNAT` プロパティを使用する必要があります。

#### <a name="new-firewall"></a>新しいファイアウォール

クラシック ルールが使用される新しいファイアウォールの場合、Azure PowerShell コマンドレットは次のようになります。

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> `New-AzFirewall` を使用して Azure Firewall をデプロイするには、既存の VNet とパブリック IP アドレスが必要です。 デプロイの詳細なガイドについては、「[Azure PowerShell を使用して Azure Firewall のデプロイと構成を行う](deploy-ps.md)」を参照してください。

> [!NOTE]
> IANAPrivateRanges は Azure Firewall の現在の既定値に拡張されますが、他の範囲は追加されます。 プライベート範囲の指定で IANAPrivateRanges の既定値を維持するには、次の例に示すように、`PrivateRange` の指定に残す必要があります。

詳細については、「[New-AzFirewall](/powershell/module/az.network/new-azfirewall)」をご覧ください。

#### <a name="existing-firewall"></a>既存のファイアウォール

クラシック ルールが使用される既存のファイアウォールを構成するには、次の Azure PowerShell コマンドレットを使用します。

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure CLI
### <a name="classic-rules"></a>クラシック ルール

Azure CLI を使用して、クラシック ルールが使用されるファイアウォールのプライベート IP アドレス範囲を指定できます。 

#### <a name="new-firewall"></a>新しいファイアウォール

クラシック ルールが使用される新しいファイアウォールの場合、Azure CLI コマンドは次のようになります。

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Azure CLI コマンド `az network firewall create` を使用して Azure Firewall をデプロイするには、パブリック IP アドレスと IP 構成を作成するための追加の構成手順が必要です。 デプロイの詳細なガイドについては、「[Azure CLI を使用して Azure Firewall のデプロイと構成を行う](deploy-cli.md)」を参照してください。

> [!NOTE]
> IANAPrivateRanges は Azure Firewall の現在の既定値に拡張されますが、他の範囲は追加されます。 プライベート範囲の指定で IANAPrivateRanges の既定値を維持するには、次の例に示すように、`private-ranges` の指定に残す必要があります。

#### <a name="existing-firewall"></a>既存のファイアウォール

クラシック ルールが使用される既存のファイアウォールを構成するには、Azure CLI コマンドは次のようになります。

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT のプライベート IP アドレス範囲を構成する - ARM テンプレート
### <a name="classic-rules"></a>クラシック ルール

ARM テンプレートのデプロイ中に SNAT を構成するには、`additionalProperties` プロパティに次を追加します。

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>ファイアウォール ポリシー

ファイアウォール ポリシーに関連する Azure Firewall では、2020-11-01 API バージョン以降、SNAT のプライベート範囲がサポートされています。 現時点では、テンプレートを使用して、ファイアウォール ポリシー上の SNAT のプライベート範囲を更新できます。 次のサンプルでは、ファイアウォールは、ネットワーク トラフィックに対して **常に** SNAT を実行するように構成されます。

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure portal
### <a name="classic-rules"></a>クラシック ルール

Azure portal を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

1. 対象のリソース グループを選択し、ファイアウォールを選択します。
2. **[概要]** ページの **[プライベート IP 範囲]** で、既定値 **[IANA RFC 1918]** を選択します。

   **[Edit Private IP Prefixes]\(プライベート IP プレフィックスの編集\)** ページが開きます。

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="[Edit private IP prefixes]\(プライベート IP プレフィックスの編集\)":::

1. 既定では、 **[IANAPrivateRanges]** が構成されています。
2. お使いの環境に合わせてプライベート IP アドレス範囲を編集し、 **[保存]** を選択します。

### <a name="firewall-policy"></a>ファイアウォール ポリシー

1.  対象のリソース グループを選択し、次にファイアウォール ポリシーを選択します。
2.  **[設定]** 列で **[プライベート IP 範囲 (SNAT)]** を選択します。

    既定では、 **[Use the default Azure Firewall Policy SNAT behavior]\(Azure Firewall ポリシーの既定の SNAT 動作を使用する\)** が選択されています。 
3. SNAT の構成をカスタマイズするには、このチェックボックスをオフにし、 **[Perform SNAT]\(SNAT を実行する\)** の下で、ご使用環境で SNAT を実行する条件を選択します。
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="プライベート IP 範囲 (SNAT)":::


4.   **[適用]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall の強制トンネリング](forced-tunneling.md)について学習します。
