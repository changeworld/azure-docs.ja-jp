---
title: Azure PowerShell を使用して複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする
description: この記事では、Azure PowerShell を使用して、複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/2/2019
ms.author: victorh
ms.openlocfilehash: a5a53766df3338bb36913b589ebda970de55ec94
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491927"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure PowerShell を使用して複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする

> [!IMPORTANT]
> 複数のパブリック IP アドレスを使用する Azure Firewall は、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

最大 100 個のパブリック IP アドレスを使用する Azure Firewall をデプロイできます。

この機能により、次のシナリオが可能になります。

- **DNAT** - 複数の標準ポート インスタンスをバックエンド サーバーに変換できます。 たとえば、2 つのパブリック IP アドレスがある場合、両方の IP アドレス用の TCP ポート 3389 (RDP) を変換できます。
- **SNAT** -送信 SNAT 接続に追加のポートを使用できるので、SNAT ポートが不足する可能性が低減されます。 現時点では、Azure Firewall は、接続に使用する送信元パブリック IP アドレスをランダムに選択します。 ネットワークにダウンストリーム フィルターがある場合、ファイアウォールに関連付けられているすべてのパブリック IP アドレスを許可する必要があります。

以下の Azure PowerShell の例は、Azure Firewall のパブリック IP アドレスを追加、削除、構成する方法を示しています。

> [!NOTE]
> パブリック プレビュー期間中は、実行中のファイアウォールに対してパブリック IP アドレスを追加または削除すると、DNAT ルールを使用する既存の受信接続が 40 から 120 秒間機能しない場合があります。 ファイアウォールが割り当て解除または削除されない限り、ファイアウォールに割り当てられている最初のパブリック IP アドレスは削除できません。

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>既存のファイアウォールにパブリック IP アドレスを追加する

この例では、パブリック IP アドレス *azFwPublicIp1* がファイアウォールにアタッチされます。

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>既存のファイアウォールからパブリック IP アドレスを削除する

この例では、パブリック IP アドレス *azFwPublicIp1* がファイアウォールからデタッチされます。

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>2 つ以上のパブリック IP アドレスを使用するファイアウォールを作成する

この例では、2 つのパブリック IP アドレスで仮想ネットワーク *vnet* に接続されたファイアウォールを作成します。

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="next-steps"></a>次の手順

* [チュートリアル:Azure Firewall のログを監視する](./tutorial-diagnostics.md)
