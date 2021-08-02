---
title: L2 拡張 VMware HCX ネットワークで DHCP を構成する
description: Azure VMware Solution VM から非 NSX-T DHCP サーバーに DHCP 要求を送信する方法について説明します。
ms.topic: how-to
ms.date: 05/28/2021
ms.openlocfilehash: fb72fef74d83c2e860cb0d2ad661a91c40197f5a
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746971"
---
# <a name="configure-dhcp-on-l2-stretched-vmware-hcx-networks"></a>L2 拡張 VMware HCX ネットワークで DHCP を構成する

DHCP サーバーがオンプレミスのデータセンターにある場合、VMware HCX L2 ストレッチ ネットワーク上の仮想マシン (VM) に対して DHCP は機能しません。  NSX では、既定ですべての DHCP 要求が L2 ストレッチを通過するときにブロックされます。 Azure VMware Solution VM から非 NSX-T DHCP サーバーに DHCP 要求を送信するには、L2 拡張 VMware HCX ネットワークで DHCP を構成する必要があります。

1. (省略可能) L2 拡張機能のセグメント名を検索する必要がある場合は、次のようにします。

   1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

   1. **[サービス]** で、 **[Network Extension]\(ネットワーク拡張機能\)** を選択します。

   1. Azure VMware Solution からオンプレミスへの DHCP 要求をサポートするネットワーク拡張機能を選択します。

   1. 宛先ネットワーク名を書き留めておきます。

      :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere クライアントでのネットワーク機能拡張のスクリーンショット" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. NSX-T Manager で、 **[ネットワーク]**  >  **[セグメント]**  >  **[セグメント プロファイル]** の順に選択します。

1. **[セグメント プロファイルの追加]** 、 **[セグメント セキュリティ]** の順に選択します。

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="NSX-T でセグメント プロファイルを追加する方法のスクリーンショット" lightbox="media/manage-dhcp/add-segment-profile.png":::
1. 名前とタグを指定してから、 **[BPDU フィルター]** トグルをオンにし、すべての DHCP トグルをオフにします。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="[BPDU フィルター] トグルがオンになり、DHCP トグルがオフになっていることを示すスクリーンショット" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::
    
   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="[Segment Security]\(セグメント セキュリティ\) フィールドのスクリーンショット" lightbox="media/manage-dhcp/edit-segment-security.png":::
