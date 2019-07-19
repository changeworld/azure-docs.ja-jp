---
title: Azure PowerShell で、可用性ゾーンを使用する Azure Firewall をデプロイする
description: この記事では、Azure PowerShell で、可用性ゾーンを使用する Azure Firewall をデプロイする方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276568"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure PowerShell で可用性ゾーンを使用する Azure Firewall をデプロイする

> [!IMPORTANT]
> 可用性ゾーンを使用する Azure Firewall は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Firewall は、デプロイ時に、可用性を高めるために複数の可用性ゾーンにまたがるように構成できます。

この機能により、次のシナリオが可能になります。

- 可用性を稼働率 99.99% に向上させることができます。 詳細については、Azure Firewall の[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) に関するページをご覧ください。 2 つ以上の可用性ゾーンを選択すると、稼働率 99.99% の SLA が提供されます。
- サービス標準の 99.95% の SLA を使用して、近接性の理由から Azure Firewall を特定のゾーンに関連付けることもできます。

Azure Firewall の可用性ゾーンの詳細については、「[Azure Firewall とは](overview.md)」をご覧ください。

次の Azure PowerShell の例では、可用性ゾーンを使用する Azure Firewall をデプロイする方法を示します。

## <a name="create-a-firewall-with-availability-zones"></a>可用性ゾーンを使用するファイアウォールを作成する

この例では、ゾーン 1、2、および 3 内にファイアウォールを作成します。

標準パブリック IP アドレスの作成時には、特定のゾーンは指定されません。 これにより、ゾーン冗長 IP アドレスが既定で作成されます。 すべてのゾーンまたは単一のゾーン内に、標準パブリック IP アドレスを構成できます。

ファイアウォールをゾーン 1 内に、IP アドレスをゾーン 2 内に構成することはできないことを知っておくことが重要です。 ただし、ファイアウォールをゾーン 1 内に、IP アドレスをすべてのゾーン内に設定するか、近接させる目的のために単一のゾーン内にファイアウォールと IP アドレスを構成することはできます。

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>次の手順

- [チュートリアル:Azure Firewall のログを監視する](./tutorial-diagnostics.md)
