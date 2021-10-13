---
title: Azure VMware Solution を使用して NSX-T ネットワーク セグメントを追加する
description: Azure portal の Azure VMware Solution 用の NSX-T ネットワーク セグメントを追加する手順。
ms.topic: include
ms.date: 07/16/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 812266ef6c7ed937c21c1c78c2c9b2aa5ff1043d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638692"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->


>[!NOTE]
>DHCP の使用を計画している場合は、NSX-T ネットワーク セグメントを構成する前に、[DHCP サーバーまたは DHCP リレーを構成](../configure-dhcp-azure-vmware-solution.md)する必要があります。 

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[セグメント]**  >  **[追加]** を選択します。 

2. 新しい論理セグメントの詳細を指定し、 **[OK]** を選択します。

   :::image type="content" source="../media/networking/add-new-nsxt-segment.png" alt-text="Azure portal に新しい NSX-T セグメントを追加する方法を示すスクリーンショット。":::

   - **[セグメント名]** - vCenter に表示されるセグメントの名前。

   - **[サブネット ゲートウェイ]** - セグメントのサブネットのゲートウェイ IP アドレスとサブネット マスク。 VM は論理セグメントに接続され、このセグメントに接続されているすべての VM は同じサブネットに属しています。  また、この論理セグメントに接続されているすべての VM は、同じセグメントの IP アドレスを持つ必要があります。

   - **[DHCP]** (省略可能) - 論理セグメントの DHCP 範囲。 セグメントで DHCP を使用するように [DHCP サーバーまたは DHCP リレー](../configure-dhcp-azure-vmware-solution.md)を構成する必要があります。    

   >[!NOTE]
   >**[接続されているゲートウェイ]** は既定で選択され、読み取り専用です。  Tier-1 ゲートウェイとセグメント情報の種類を示しています。 
   >
   >- **[T1]** - NSX-T Manager の Tier-1 ゲートウェイの名前。 プライベート クラウドには、アクティブ/アクティブ モードで NSX-T Tier-0 ゲートウェイが、アクティブ/スタンバイ モードで既定の NSX-T Tier-1 ゲートウェイがあります。  Azure VMware Solution コンソールを使用して作成されたセグメントは、既定の Tier-1 ゲートウェイにのみ接続し、これらのセグメントのワークロードは East-West および North-South 接続を取得します。 NSX-T Manager を通じてのみ、より多くの Tier-1 ゲートウェイを作成できます。 NSX-T Manager コンソールから作成された Tier-1 ゲートウェイは、Azure VMware Solution コンソールには表示されません。 
   >
   >- **[種類]** -Azure VMware Solution でサポートされるオーバーレイ セグメント。

このセグメントは、Azure VMware Solution、NSX-T Manager、および vCenter に表示されるようになりました。
