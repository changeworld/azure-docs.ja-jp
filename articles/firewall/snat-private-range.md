---
title: Azure Firewall の SNAT プライベート IP アドレス範囲
description: ファイアウォールによって IP アドレスへのトラフィックの SNAT が行われないようなプライベート範囲を IP アドレスに構成することができます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444832"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall の SNAT プライベート IP アドレス範囲

宛先 IP アドレスが [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) に従ったプライベート IP アドレス範囲内にある場合、Azure Firewall では SNAT は行われません。 

組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 ただし、パブリック IP アドレス範囲の SNAT が**行われない**ように、Azure Firewall を構成することができます。

## <a name="configure-snat-private-ip-address-ranges"></a>SNAT のプライベート IP アドレス範囲を構成する

Azure PowerShell を使って、ファイアウォールで SNAT が行われない IP アドレスの範囲を指定できます。

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
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>テンプレート

`additionalProperties` セクションに以下を追加できます。

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>次のステップ

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。