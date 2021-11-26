---
title: Azure Firewall を仮想 WAN ハブにインストールする
titleSuffix: Azure Virtual WAN
description: 仮想 WAN ハブ内で Azure Firewall を構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 20ae45cd4e6ab7d1e165cd67f0cec62318197258
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723724"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>仮想 WAN ハブ内で Azure Firewall を構成する

**セキュリティ保護付きハブ** は、Azure Firewall を備えた Azure Virtual WAN ハブです。 この記事では、Azure Virtual WAN ポータル ページから直接 Azure Firewall をインストールして、仮想 WAN ハブをセキュリティ保護付きハブに変換する手順について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順では、1 つ以上のハブがある仮想 WAN が既にデプロイされていること想定します。

新しい仮想 WAN と新しいハブを作成するには、次の記事の手順に従います。

* [仮想 WAN を作成する](virtual-wan-site-to-site-portal.md#openvwan)
* [ハブを作成する](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>仮想ハブの表示

仮想 WAN の **[概要]** ページには、仮想ハブとセキュリティ保護付きハブの一覧が表示されます。 次の図は、セキュリティ保護付きハブがない仮想 WAN を示しています。

:::image type="content" source="./media/howto-firewall/overview.png" alt-text="スクリーンショットには、仮想ハブの一覧を含む仮想 WAN の [概要] ページが示されています。" lightbox="./media/howto-firewall/overview.png":::

## <a name="convert-to-secured-hub"></a>セキュリティ保護付きハブへの変換

1. 仮想 WAN の **[概要]** ページで、セキュリティ保護付きハブに変換するハブを選択します。 仮想ハブ ページに、Azure Firewall をこのハブにデプロイするための 2 つのオプションが表示されます。 どちらかのオプションを選択します。

   :::image type="content" source="./media/howto-firewall/security.png" alt-text="スクリーンショットには、仮想 WAN の [概要] ページが示されています。ここで、[Convert to secure hub]\(セキュリティ保護付きハブに変換\) または [Azure Firewall] を選択できます。" lightbox="./media/howto-firewall/security.png":::

1. いずれかのオプションを選択すると、 **[Convert to secure hub]\(セキュリティ保護付きハブに変換\)** ページが表示されます。 変換するハブを選択し、ページの下部にある **[次へ:Azure Firewall]** を選択します。

   :::image type="content" source="./media/howto-firewall/select-hub.png" alt-text="ハブが選択されている [セキュリティで保護されたハブに変換] のスクリーンショット。" lightbox="./media/howto-firewall/select-hub.png":::
1. ワークフローが完了したら、 **[確認]** を選択します。

   :::image type="content" source="./media/howto-firewall/confirm.png" alt-text="スクリーンショットには、[確認] が選択された [Convert to secure hub]\(セキュリティ保護付きハブに変換\) ペインが示されています。" lightbox="./media/howto-firewall/confirm.png":::
1. ハブがセキュリティ保護付きハブに変換されたら、仮想 WAN の **[概要]** ページで確認できます。

   :::image type="content" source="./media/howto-firewall/secured-hub.png" alt-text="セキュリティで保護されたハブ表示のスクリーンショット。" lightbox="./media/howto-firewall/secured-hub.png":::

## <a name="view-hub-resources"></a>ハブ リソースの表示

仮想 WAN の **[概要]** ページで、セキュリティ保護付きハブを選択します。 ハブ ページには、Azure Firewall を含むすべての仮想ハブ リソースが表示されます。

セキュリティ保護付きハブから Azure Firewall 設定を表示するには、 **[セキュリティ]** で、 **[Secured virtual hub settings]\(セキュリティ保護付き仮想ハブの設定\)** を選択します。

:::image type="content" source="./media/howto-firewall/hub-settings.png" alt-text="セキュリティ保護付き仮想ハブ設定のスクリーンショット。" lightbox="./media/howto-firewall/hub-settings.png":::

## <a name="configure-additional-settings"></a>追加の設定の構成

仮想ハブ用の追加の Azure Firewall 設定を構成するには、**Azure Firewall Manager** へのリンクを選択します。 ファイアウォール ポリシーの詳細については、[Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub) に関するページを参照してください。

:::image type="content" source="./media/howto-firewall/additional-settings.png" alt-text="Azure Firewall Manager で、[セキュリティ保護付き仮想ハブのセキュリティ プロバイダー ルート設定を管理する] を選択した場合の概要のスクリーンショット。" lightbox="./media/howto-firewall/additional-settings.png":::

ハブの **[概要]** ページに戻るには、次の図の矢印で示されているようなパスをクリックします。

:::image type="content" source="./media/howto-firewall/arrow.png" alt-text="概要ページに戻る方法を示すスクリーンショット。" lightbox="./media/howto-firewall/arrow.png":::

## <a name="upgrade-to-azure-firewall-premium"></a>Azure Firewall Premium へのアップグレード
Azure Firewall Standard は、こちらの[手順](../firewall/premium-migrate.md#migrate-a-secure-hub-firewall)に従って、いつでも Premium にアップグレードできます。 この操作では、ごく短いもののダウンタイムが発生するため、メンテナンス期間が必要となります。 

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。