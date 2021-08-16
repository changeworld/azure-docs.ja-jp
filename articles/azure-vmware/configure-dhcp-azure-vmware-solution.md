---
title: Azure VMware Solution で DHCP を構成して管理する
description: Azure VMware Solution のプライベート クラウド向けに DHCP を作成して管理する方法について説明します。
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 05/17/2021
ms.openlocfilehash: 28fb995cbc91d6e350e8d295c1f57d62c706e59e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104767"
---
# <a name="configure-and-manage-dhcp-in-azure-vmware-solution"></a>Azure VMware Solution で DHCP を構成して管理する

プライベート クラウド環境で実行されるアプリケーションとワークロードには、IP アドレスを割り当てるための DHCP サービスが必要です。  この記事では、Azure VMware Solution で DHCP を作成して管理する 2 つの方法について説明します。

- NSX-T を使用して DHCP サーバーをホストする場合は、[DHCP サーバーを作成](#create-a-dhcp-server)し、[そのサーバーにリレー](#create-dhcp-relay-service)する必要があります。 DHCP サーバーを作成するときは、さらにネットワーク セグメントを追加し、DHCP IP アドレスの範囲を指定します。   

- ネットワークでサードパーティの外部 DHCP サーバーを使用する場合は、[DHCP リレー サービスを作成](#create-dhcp-relay-service)する必要があります。 NSX-T とサードパーティのどちらを使用して DHCP サーバーをホストする場合でも、DHCP サーバーへのリレーを作成するときに、DHCP IP アドレスの範囲を指定する必要があります。

>[!IMPORTANT]
>DHCP サーバーがオンプレミスのデータセンターにある場合、VMware HCX L2 ストレッチ ネットワーク上の仮想マシン (VM) に対して DHCP は機能しません。  NSX では、既定ですべての DHCP 要求が L2 ストレッチを通過するときにブロックされます。 解決策については、「[L2 拡張 VMware HCX ネットワークで DHCP を構成する](configure-l2-stretched-vmware-hcx-networks.md)」の手順をご覧ください。


## <a name="create-a-dhcp-server"></a>DHCP サーバーを作成する

NSX-T を使用して DHCP サーバーをホストする場合は、DHCP サーバーを作成します。 次に、ネットワーク セグメントを追加し、DHCP IP アドレスの範囲を指定します。

1. NSX-T Manager で、 **[ネットワーク]**  >  **[DHCP]** を選択してから、 **[サーバーの追加]** を選択します。

1. **[サーバーの種類]** で **[DHCP]** を選択し、サーバー名と IP アドレスを入力してから、 **[保存]** を選択します。

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP サーバーの追加" border="true":::

1. **[第 1 層ゲートウェイ]** を選択し、第 1 層ゲートウェイの縦方向の省略記号を選択してから、 **[編集]** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="使用するゲートウェイの選択" border="true":::

1. **[No IP Allocation Set]\(IP 割り当てセットなし\)** を選択して、サブネットを追加します。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="サブネットの追加" border="true":::

1. **[種類]** で、 **[DHCP ローカル サーバー]** を選択します。 
   
1. **[DHCP サーバー]** で、 **[既定の DHCP]** を選択してから、 **[保存]** を選択します。

1. もう一度 **[保存]** を選択し、 **[編集を閉じる]** を選択します。

### <a name="add-a-network-segment"></a>ネットワーク セグメントの追加

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>DHCP リレー サービスの作成

サードパーティの外部 DHCP サーバーを使用する場合は、DHCP リレー サービスを作成する必要があります。 NSX-T Manager で DHCP IP アドレスの範囲も指定します。 

1. NSX-T Manager で、 **[ネットワーク]**  >  **[DHCP]** を選択してから、 **[サーバーの追加]** を選択します。

1. **[サーバーの種類]** で **[DHCP Relay]\(DHCP リレー\)** を選択し、サーバー名と IP アドレスを入力してから、 **[保存]** を選択します。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP リレー サービスの作成" border="true":::

1. **[第 1 層ゲートウェイ]** を選択し、第 1 層ゲートウェイの縦方向の省略記号を選択してから、 **[編集]** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="第 1 層ゲートウェイの編集" border="true":::

1. IP アドレスの割り当てを定義するには、 **[No IP Allocation Set]\(IP 割り当てセットなし\)** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="IP アドレスの割り当ての編集" border="true":::

1. **[種類]** で、 **[DHCP サーバー]** を選択します。 
   
1. **[DHCP サーバー]** で、 **[DHCP Relay]\(DHCP リレー\)** を選択してから、 **[保存]** を選択します。

1. もう一度 **[保存]** を選択し、 **[編集を閉じる]** を選択します。


## <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP アドレスの範囲を指定する

1. NSX-T Manager で、 **[ネットワーク]**  >  **[セグメント]** を選択します。 
   
1. セグメント名の縦方向の省略記号を選択し、 **[編集]** を選択します。
   
1. **[Set Subnets]\(サブネットの設定\)** を選択して、サブネットの DHCP IP アドレスを指定します。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="ネットワーク セグメント" border="true":::
      
1. 必要に応じてゲートウェイ IP アドレスを変更し、DHCP 範囲 IP を入力します。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="サブネットの編集" border="true":::
      
1. **[適用]** 、 **[保存]** の順に選択します。 DHCP サーバー プールがセグメントに割り当てられます。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="セグメントに割り当てられた DHCP サーバープール" border="true":::



## <a name="next-steps"></a>次の手順

Azure VMware Solution VM から非 NSX-T DHCP サーバーに DHCP 要求を送信する場合は、セキュリティ セグメント プロファイルを作成します。 解決策については、「[L2 拡張 VMware HCX ネットワークで DHCP を構成する](configure-l2-stretched-vmware-hcx-networks.md)」の手順をご覧ください。

