---
title: シナリオ:NVA を経由するトラフィックのルーティング
titleSuffix: Azure Virtual WAN
description: NVA を経由するトラフィックのルーティング
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142973"
---
# <a name="scenario-route-traffic-through-an-nva"></a>シナリオ:NVA を経由するトラフィックのルーティング

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 この NVA シナリオでの目標は、NVA (ネットワーク仮想アプライアンス) を経由するブランチへのトラフィックを VNet にルーティングし、VNet へのトラフィックをブランチにルーティングすることです。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

> [!NOTE]
> これらのルーティング機能の一部は、まだロールアウト中の可能性があります。お客様のリージョンでロールアウトがまだの場合は、当面、以下の版の記事にある手順を使用してください。
>* [Azure portal に関する記事](virtual-wan-route-table-nva-portal.md)
>* [PowerShell に関する記事](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>シナリオのアーキテクチャ

**図 1** には、2 つのハブ **Hub 1** と **Hub 2** があります。

* **Hub 1** と **Hub 2** は、NVA VNet の **VNET 2** および **VNET 4** に直接接続されています。

* **VNET 5** と **VNET 6** は、**VNET 2** とピアリングされています。

* **VNET 7** と **VNET 8** は、**VNET 4** とピアリングされています。

* **VNET 5、6、7、8** は間接的なスポークであり、仮想ハブに直接接続されてはいません。

**図 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="図 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

NVA 経由のルーティングを設定するには、次のような手順を検討します。

1. NVA スポークの VNet 接続を明らかにします。 **図 1** では、**VNET 2 の接続 (eastusconn)** および **VNET 4 の接続 (weconn)** です。

   UDR が設定されていることを確認します。
   * VNET 5 および 6 から VNET 2 NVA IP に
   * VNET 7 および 8 から VNET 4 NVA IP に 
   
   VNET 5、6、7、8 を仮想ハブに直接接続する必要はありません。 VNET 5、6、7、8 の NSG で、ブランチ (VPN/ER/P2S) またはリモート VNet に接続されている VNet に対するトラフィックが許可されていることを確認します。 たとえば、VNET 5、6 の NSG では、リモート ハブ 2 に接続されているオンプレミスのアドレス プレフィックスと VNET 7、8 に対するトラフィックが許可される必要があります。 

2. VNET 2、5、6 に対する集約静的ルート エントリを、Hub 1 の既定のルート テーブルに追加します。 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="例":::

3. VNET 2 の仮想ネットワーク接続で、VNET 5、6 に対する静的ルートを構成します。 仮想ネットワーク接続に対するルーティング構成の設定については、[仮想ハブルーティング](how-to-virtual-hub-routing.md#routing-configuration)に関する記事を参照してください。

4. VNET 4、7、8 に対する集約静的ルート エントリを、Hub 1 の既定のルート テーブルに追加します。

5. Hub 2 の既定のルート テーブルについて、ステップ 2、3、4 を繰り返します。

これで、ルーティング構成が次の図に示すように変更されます

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="結果":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
