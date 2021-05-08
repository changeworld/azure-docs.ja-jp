---
title: Application Gateway とバックエンド プール間のトラフィックをセキュリティで保護する
titleSuffix: Azure Virtual WAN
description: 'ルーティングのシナリオ: セキュリティで保護された Virtual WAN のハブに接続されているスポーク VNet にデプロイされたアプリケーション ゲートウェイを経由するトラフィックをセキュリティで保護します。'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315342"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>シナリオ: Application Gateway とバックエンド プール間のトラフィックをセキュリティで保護する

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオでは、ユーザーのトラフィックは、セキュリティで保護された Virtual WAN のハブ (Azure Firewall が装備された Virtual WAN のハブ) に接続されているスポーク VNet にデプロイされたアプリケーション ゲートウェイを介して Azure に入ります。 目標は、セキュリティで保護された仮想ハブ内の Azure Firewall を使用して、アプリケーション ゲートウェイとバックエンド プールとの間のトラフィックを検査することです。

このシナリオには、アプリケーション ゲートウェイとバックエンド プールが同じ VNet 内にあるか、異なる VNet 内にあるかに応じて、2 つの設計パターンがあります。

* **シナリオ 1:** アプリケーション ゲートウェイとバックエンド プールは、Virtual WAN のハブ (別個のサブネット) とピアリングされた同じ仮想ネットワーク内にある。
* **シナリオ 2:** アプリケーション ゲートウェイとバックエンド プールは、Virtual WAN のハブとピアリングされた異なる仮想ネットワーク内にある。

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>シナリオ 1 - 同じ VNet

このシナリオでは、アプリケーション ゲートウェイとバックエンド プールは、Virtual WAN のハブ (別個のサブネット) とピアリングされた同じ仮想ネットワーク内にあります。

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="シナリオ 1 の図。" lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>ワークフロー

現時点では、Virtual WAN のルート テーブルからスポーク仮想ネットワークにアドバタイズされたルートは、スポーク VNet のサブネットではなく、仮想ネットワーク全体に適用されます。 このため、このシナリオを有効にするには、ユーザー定義ルートが必要です。 ユーザー定義ルート (UDR) の詳細については、[仮想ネットワークのカスタム ルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)に関する記事を参照してください。


1. Azure Firewall Manager で、アプリケーション ゲートウェイとバックエンド プールが含まれるスポーク仮想ネットワークで **[Enable Secure Internet Traffic]\(セキュリティで保護されたインターネット トラフィックを有効にする\)** と **[Enable Secure Private Traffic]\(セキュリティで保護されたプライベート トラフィックを有効にする\)** を選択します。
1. アプリケーション ゲートウェイ サブネット上のユーザー定義ルート (UDR) を構成します。

   * アプリケーション ゲートウェイがトラフィックをインターネットに直接送信できるようにするには、次の UDR を指定します。

     * **アドレス プレフィックス**: 0.0.0.0.0/0
     * **ネクスト ホップ:** インターネット

   * アプリケーション ゲートウェイが Virtual WAN のハブの Azure Firewall 経由でバックエンド プールにトラフィックを送信できるようにするには、次の UDR を指定します。

      * **アドレス プレフィックス:** バックエンド プール サブネット (10.2.0.0/24)
      * **ネクスト ホップ:** Azure Firewall のプライベート IP

1. バックエンド プール サブネット上のユーザー定義ルート (UDR) を構成します。

   * **アドレス プレフィックス:** Application Gateway サブネット
   * **ネクスト ホップ:** Azure Firewall のプライベート IP

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>シナリオ 2 - 異なる VNet

アプリケーション ゲートウェイとバックエンド プールは、Virtual WAN のハブとピアリングされた異なる仮想ネットワーク内にあります。

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="シナリオ 2 の図。" lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>ワークフロー

現時点では、Virtual WAN のルート テーブルからスポーク仮想ネットワークにアドバタイズされたルートは、スポーク VNet のサブネットではなく、仮想ネットワーク全体に適用されます。 このため、このシナリオを有効にするには、ユーザー定義ルートが必要です。 ユーザー定義ルート (UDR) の詳細については、[仮想ネットワークのカスタム ルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)に関する記事を参照してください。

1. **Azure Firewall Manager** で、両方のスポーク仮想ネットワークで **[Enable Secure Internet Traffic]\(セキュリティで保護されたインターネット トラフィックを有効にする\)** と **[Enable Secure Private Traffic]\(セキュリティ保護されたプライベート トラフィックを有効にする\)** を選択します。

1. アプリケーション ゲートウェイ サブネット上のユーザー定義ルート (UDR) を構成します。 アプリケーション ゲートウェイがトラフィックをインターネットに直接送信できるようにするには、次の UDR を指定します。

   * **アドレス プレフィックス**: 0.0.0.0.0/0
   * **ネクスト ホップ:** インターネット

## <a name="next-steps"></a>次のステップ

* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
* ユーザー定義ルートの詳細については、[仮想ネットワークのカスタム ルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)に関する記事を参照してください。
* Virtual WAN のセキュリティ保護付き仮想ハブの詳細については、[セキュリティ保護付き仮想ハブ](../firewall-manager/secured-virtual-hub.md)に関する記事を参照してください。
