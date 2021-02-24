---
title: NSX-T ネットワーク セグメントを追加する
description: Azure VMware Solution 用の NSX-T ネットワーク セグメントを追加する手順。
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335061"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. NSX-T Manager で、 **[Networking]\(ネットワーク\)**  >  **[Segments]\(セグメント\)** を選択してから、 **[Add Segment]\(セグメントの追加\)** を選択します。 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="新しいセグメントを追加する方法を示すスクリーンショット":::

1. **[Add Segment]\(セグメントの追加\)** を選択し、セグメントの名前を入力します。

1. **[Connected Gateway]\(接続されているゲートウェイ\)** として Tier1 ゲートウェイ (TNTxx-T1) を選択します。 **[Type]\(種類\)** は [Flexible]\(柔軟\) のままにします。

1. 事前構成済みのオーバーレイ **トランスポート ゾーン** (TNTxx-OVERLAY-TZ) を選択して、 **[Set Subnets]\(サブネットの設定\)** を選択します。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="セグメント名、接続されているゲートウェイ、種類、トランスポート ゾーンを設定し、[Set Subnets]\(サブネットの設定\) を選択します。":::

1. ゲートウェイの IP アドレスを入力し、 **[Add]\(追加\)** を選択します。 

   >[!IMPORTANT]
   >IP アドレスは、重複しない RFC1918 アドレス ブロック上に存在する必要があります。これで新しいセグメント上の VM への接続が確保されます。

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="新しいセグメントのゲートウェイの IP アドレスを設定し、[ADD]\(追加\) を選択します。":::

1. **[Apply]\(適用\)** 、 **[Save]\(保存\)** の順に選択します。

1. **[No]\(いいえ\)** を選択して、セグメントの構成を続行するのを辞退します。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="[NO]\(いいえ\) を選択して、新しく作成されたネットワーク セグメントのさらなる構成を辞退する。":::

1. 新しいネットワーク セグメントが存在することを確認します。 この例では、**ls01** が新しいネットワーク セグメントです。

   1. NSX-T Manager で、 **[Networking]\(ネットワーク\)**  >  **[Segments]\(セグメント\)** を選択します。 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="新しいネットワーク セグメントが NSX-T に存在することを確認する。":::

   1. vCenter で、 **[Networking]\(ネットワーク\) > [SDDC-Datacenter]\(SDDC データセンター\)** を選択します。

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="新しいネットワーク セグメントが vCenter に存在することを確認する。":::