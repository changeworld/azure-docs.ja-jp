---
title: NSX-T ネットワーク セグメントを追加する
description: NSX-T Manager の Azure VMware Solution 用の NSX-T ネットワーク セグメントを追加する手順。
ms.topic: include
ms.date: 03/13/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1e6d50e1ca05ea15c4d6690965c112565dae76f5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638422"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. NSX-T Manager で、 **[Networking]\(ネットワーク\)**  >  **[Segments]\(セグメント\)** を選択してから、 **[Add Segment]\(セグメントの追加\)** を選択します。 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="NSX-T Manager に新しいセグメントを追加する方法を示すスクリーンショット。":::

1. セグメントの名前を入力します。

1. **[Connected Gateway]\(接続されているゲートウェイ\)** として Tier-1 ゲートウェイ (TNTxx-T1) を選択し、 **[Type]\(タイプ\)** は [Flexible]\(柔軟\) のままにします。

1. 事前構成済みのオーバーレイ **トランスポート ゾーン** (TNTxx-OVERLAY-TZ) を選択して、 **[Set Subnets]\(サブネットの設定\)** を選択します。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="新しい NSX-T ネットワーク セグメントを追加するためのセグメントの詳細を示すスクリーンショット。":::

1. ゲートウェイ IP アドレスを入力し、 **[Add]\(追加\)** を選択します。 

   >[!IMPORTANT]
   >IP アドレスは、重複しない RFC1918 アドレス ブロック上に存在する必要があります。これで新しいセグメント上の VM への接続が確保されます。

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="新しいセグメントのゲートウェイの IP アドレスを示すスクリーンショット。":::

1. **[Apply]\(適用\)** 、 **[Save]\(保存\)** の順に選択します。

1. **[No]\(いいえ\)** を選択して、セグメントの構成を続行するのを辞退します。 


