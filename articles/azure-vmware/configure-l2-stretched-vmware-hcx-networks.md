---
title: L2 拡張 VMware HCX ネットワークで DHCP を構成する
description: Azure VMware Solution VM から非 NSX-T DHCP サーバーに DHCP 要求を送信する方法について説明します。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 05/28/2021
ms.openlocfilehash: c59df75e70bf8913575b70b80048b14ae42d62a2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322744"
---
# <a name="configure-dhcp-on-l2-stretched-vmware-hcx-networks"></a>L2 拡張 VMware HCX ネットワークで DHCP を構成する

DHCP サーバーがオンプレミスのデータセンターにある場合、VMware HCX L2 ストレッチ ネットワーク上の仮想マシン (VM) に対して DHCP は機能しません。 これは、NSX では、既定ですべての DHCP 要求が L2 ストレッチを通過するときにブロックされるためです。 そのため、Azure VMware Solution VM から非 NSX-T DHCP サーバーに DHCP 要求を送信するには、L2 拡張 VMware HCX ネットワークで DHCP を構成する必要があります。

1. (省略可能) L2 拡張機能のセグメント名を検索する必要がある場合は、次のようにします。

   1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

   1. **[サービス]** で、 **[Network Extension]\(ネットワーク拡張機能\)** を選択します。

   1. Azure VMware Solution からオンプレミスへの DHCP 要求をサポートするネットワーク拡張機能を選択します。

   1. 宛先ネットワーク名を書き留めておきます。

      :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere クライアントでのネットワーク機能拡張のスクリーンショット。" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. NSX-T Manager で、 **[ネットワーク]**  >  **[セグメント]**  >  **[セグメント プロファイル]** の順に選択します。

1. **[セグメント プロファイルの追加]** 、 **[セグメント セキュリティ]** の順に選択します。

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="NSX-T でセグメント プロファイルを追加する方法のスクリーンショット。" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. 名前とタグを指定してから、 **[BPDU フィルター]** トグルをオンにし、すべての DHCP トグルをオフにします。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="[BPDU フィルター] オンに切り替えられ、DHCP がオフに切り替えられていることを示すスクリーンショット。" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::
    
   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="[Segment Security (セグメント セキュリティ)] フィールドのスクリーンショット。" lightbox="media/manage-dhcp/edit-segment-security.png":::
