---
title: Azure Site Recovery を使用して複数の IP アドレスのフェールオーバーを構成する
description: Azure VM のセカンダリ IP 構成のフェールオーバーを構成する方法について説明します
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: rishjai
ms.openlocfilehash: a7437650c11797d5a00c7c8684138632555b05a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478311"
---
# <a name="configure-failover-of-multiple-ip-addresses-with-azure-site-recovery"></a>Azure Site Recovery を使用して複数の IP アドレスのフェールオーバーを構成する

VM に接続された各 NIC には、1 つ以上の IP 構成が関連付けられています。 各構成には、1 つの静的または動的プライベート IP アドレスが割り当てられています。 また、1 つのパブリック IP アドレス リソースが関連付けられている場合もあります。 パブリック IP アドレス リソースには、動的または静的なパブリック IP アドレスが割り当てられています。 Azure における IP アドレスの詳細については、[Azure 内の IP アドレス](../virtual-network/ip-services/public-ip-addresses.md)に関する記事を参照してください。

現在、Site Recovery では、プライマリ IP 構成のフェールオーバーが自動的に構成されます。 この記事では、Site Recovery を使用してセカンダリ IP 構成のフェールオーバーを構成する方法を説明します。 これは Azure VM でのみサポートされています。

## <a name="configure-secondary-ip-address-failover-via-azure-portal"></a>Azure portal でセカンダリ IP アドレスのフェールオーバーを構成する

Site Recovery では、VM のレプリケーションを有効にすると、プライマリ IP 構成のフェールオーバーが自動的に構成されます。 セカンダリ IP 構成は、レプリケーションの有効化が完了した後に手動で構成する必要があります。 このためには、1 つ以上のセカンダリ IP 構成を持つ保護された VM が必要です。

記載されている手順に従います。
1. [レプリケートされたアイテム] ページの **[ネットワーク]** ブレードに移動します。

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-tab.png" alt-text="[レプリケートされたアイテム] ブレード":::
    

2. 次の強調表示されたテキストが表示されます。  **[編集]** をクリックして変更します。
 
    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-edit.png" alt-text="[ネットワーク] タブの編集モード" lightbox="./media/concepts-multiple-ip-address-failover/network-edit-expanded.png":::    

3. [+ IP 構成] をクリックします。 [すべての IP 構成を追加する] か、[IP 構成を選択して追加する] の 2 つのオプションが表示されます。

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-ip-configurations.png" alt-text="IP 構成を追加する":::

4. **[すべてのセカンダリ IP 構成を追加する]** をクリックすると、そのすべてが下のグリッドに表示され、自由に構成できます。

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations.png" alt-text="すべての IP 構成を追加する" lightbox="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations-expanded.png":::    

5. または、 **[セカンダリ IP 構成を選択して追加する]** をクリックすると、フェールオーバー用に構成する IP 構成を選択して追加できるブレードが開きます。

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/select-and-add-ip-configurations.png" alt-text="IP 構成を選択して追加する":::

これで、追加した IP 構成ごとに、フェールオーバーとテスト フェールオーバー用にプライベート IP、パブリック IP、バックエンド プールの値を個別に構成できます。 完了したら、忘れずに変更を保存してください。


## <a name="next-steps"></a>次のステップ
- [Azure Site Recovery を使ったAzure Traffic Manager](../site-recovery/concepts-traffic-manager-with-site-recovery.md)について詳しく見る
- Traffic Manager の[ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)の詳細を確認する。
- アプリケーションのフェールオーバーを自動化するための[復旧計画](site-recovery-create-recovery-plans.md)の詳細を確認する。
