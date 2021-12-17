---
title: Azure VMware Solution 用の DHCP の構成
description: NSX-T Manager を使用して DHCP サーバーをホストするか、サードパーティの外部 DHCP サーバーを使用して、DHCP を構成する方法について説明します。
ms.topic: how-to
ms.custom: contperf-fy21q2, contperf-fy22q1
ms.date: 09/13/2021
ms.openlocfilehash: 10234147303ab9959fa1a907b0c558be9e3fe0f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600963"
---
# <a name="configure-dhcp-for-azure-vmware-solution"></a>Azure VMware Solution 用の DHCP の構成

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]

このハウツー記事では、次のいずれかの方法で、NSX-T Manager を使用して Azure VMware Solution 用の DHCP を構成します。 


- [Azure portal を使用して DHCP サーバーまたはリレーを作成する](#use-the-azure-portal-to-create-a-dhcp-server-or-relay)

- [NSX-T を使用して DHCP サーバーをホストする](#use-nsx-t-to-host-your-dhcp-server)

- [サードパーティの外部 DHCP サーバーを使用する](#use-a-third-party-external-dhcp-server)

>[!TIP]
>NSX-T 操作の簡略化されたビューを使用して DHCP を構成する場合は、「[Azure VMware Solution 用の DHCP の構成](configure-dhcp-azure-vmware-solution.md)」を参照してください。


>[!IMPORTANT]
>2021 年 7 月 1 日以降に作成されたクラウドの場合、ご使用の環境の既定の第 1 層ゲートウェイで DHCP を構成するには、NSX-T 操作の簡略化されたビューを使用する必要があります。
>
>DHCP サーバーがオンプレミスのデータセンターにある場合、VMware HCX L2 ストレッチ ネットワーク上の仮想マシン (VM) に対して DHCP は機能しません。  NSX では、既定ですべての DHCP 要求が L2 ストレッチを通過するときにブロックされます。 解決策については、「[L2 拡張 VMware HCX ネットワークで DHCP を構成する](configure-l2-stretched-vmware-hcx-networks.md)」の手順をご覧ください。

## <a name="use-the-azure-portal-to-create-a-dhcp-server-or-relay"></a>Azure portal を使用して DHCP サーバーまたはリレーを作成する

Azure portal の Azure VMware Solution から、DHC サーバーまたはリレーを直接作成できます。 DHCP サーバーまたはリレーは、Azure VMware Solution をデプロイすると作成される Tier-1 ゲートウェイに接続されます。 DHCP 範囲を指定したすべてのセグメントが、この DHCP の一部になります。 DHCP サーバーまたは DHCP リレーを作成した後、それを使用するには、セグメント レベルでサブネットまたは範囲を定義する必要があります。

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[DHCP]**  >  **[追加]** を選択します。

2. **[DHCP サーバー]** または **[DHCP リレー]** を選択してから、サーバーまたはリレーの名前と 3 つの IP アドレスを指定します。 

   >[!NOTE]
   >DHCP リレーの場合、構成を成功させるために必要な IP アドレスは 1 つだけです。

   :::image type="content" source="media/networking/add-dhcp-server-relay.png" alt-text="Azure VMware Solution に DHCP サーバーまたは DHCP リレーを追加する方法を示すスクリーンショット。":::

4. [論理セグメントに DHCP 範囲を指定](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment)して DHCP 構成を完了し、 **[OK]** を選択します。



## <a name="use-nsx-t-to-host-your-dhcp-server"></a>NSX-T を使用して DHCP サーバーをホストする
NSX-T を使用して DHCP サーバーをホストする場合は、DHCP サーバーとリレー サービスを作成します。 次に、ネットワーク セグメントを追加し、DHCP IP アドレスの範囲を指定します。

### <a name="create-a-dhcp-server"></a>DHCP サーバーを作成する

1. NSX-T Manager で、 **[ネットワーク]**  >  **[DHCP]** を選択してから、 **[サーバーの追加]** を選択します。

1. **[サーバーの種類]** で **[DHCP]** を選択し、サーバー名と IP アドレスを入力して、 **[保存]** を選択します。

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="NSX-T Manager に DHCP サーバーを追加する方法を示すスクリーンショット。" border="true":::

1. **[第 1 層ゲートウェイ]** を選択し、第 1 層ゲートウェイの縦方向の省略記号を選択してから、 **[編集]** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="DHCP サーバーを使用するために第 1 層ゲートウェイを編集する方法を示すスクリーンショット。" border="true":::

1. **[No IP Allocation Set]\(IP 割り当てセットなし\)** を選択して、サブネットを追加します。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="DHCP サーバーを使用するために第 1 層ゲートウェイにサブネットを追加する方法を示すスクリーンショット。" border="true":::

1. **[種類]** で、 **[DHCP ローカル サーバー]** を選択します。 
   
1. **[DHCP サーバー]** で、 **[既定の DHCP]** を選択してから、 **[保存]** を選択します。

1. もう一度 **[保存]** を選択し、 **[編集を閉じる]** を選択します。

### <a name="add-a-network-segment"></a>ネットワーク セグメントの追加

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

### <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP アドレスの範囲を指定する
 
DHCP サーバーへのリレーを作成するときは、DHCP IP アドレスの範囲も指定します。

>[!NOTE]
>IP アドレス範囲は、サブスクリプションやオンプレミス ネットワークにある他の仮想ネットワークで使用されている IP 範囲と重複しないようにする必要があります。

1. NSX-T Manager で、 **[ネットワーク]**  >  **[セグメント]** を選択します。 
   
1. セグメント名の縦方向の省略記号を選択し、 **[編集]** を選択します。
   
1. **[Set Subnets]\(サブネットの設定\)** を選択して、サブネットの DHCP IP アドレスを指定します。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP サーバーを使用するための DHCP IP アドレスを指定するようにサブネットを設定する方法を示すスクリーンショット。" border="true":::
      
1. 必要に応じてゲートウェイ IP アドレスを変更し、DHCP 範囲 IP を入力します。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="DHCP サーバーを使用するためのゲートウェイ IP アドレスと DHCP 範囲を示すスクリーンショット。" border="true":::
      
1. **[適用]** 、 **[保存]** の順に選択します。 DHCP サーバー プールがセグメントに割り当てられます。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP サーバーを使用するために、DHCP サーバー プールがセグメントに割り当てられていることを示すスクリーンショット。" border="true":::


## <a name="use-a-third-party-external-dhcp-server"></a>サードパーティの外部 DHCP サーバーを使用する

サードパーティの外部 DHCP サーバーを使用する場合は、NSX-T Manager に DHCP リレー サービスを作成します。 DHCP IP アドレスの範囲も指定します。


>[!IMPORTANT]
>2021 年 7 月 1 日以降に作成されたクラウドの場合、ご使用の環境の既定の第 1 層ゲートウェイで DHCP を構成するには、NSX-T 操作の簡略化されたビューを使用する必要があります。


### <a name="create-dhcp-relay-service"></a>DHCP リレー サービスの作成

非 NSX ベースの DHCP サービスには、DHCP リレーを使用します。 たとえば、Azure VMware Solution、Azure IaaS、またはオンプレミスで DHCP を実行している VM があるとします。

1. NSX-T Manager で、 **[ネットワーク]**  >  **[DHCP]** を選択してから、 **[サーバーの追加]** を選択します。

1. **[サーバーの種類]** で **[DHCP Relay]\(DHCP リレー\)** を選択し、サーバー名と IP アドレスを入力して、 **[保存]** を選択します。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="NSX-T Manager で DHCP リレー サービスを作成する方法を示すスクリーンショット。" border="true":::

1. **[第 1 層ゲートウェイ]** を選択し、第 1 層ゲートウェイの縦方向の省略記号を選択してから、 **[編集]** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="第 1 層ゲートウェイを編集する方法を示すスクリーンショット。" border="true":::

1. IP アドレスの割り当てを定義するには、 **[No IP Allocation Set]\(IP 割り当てセットなし\)** を選択します。

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="第 1 層ゲートウェイにサブネットを追加する方法を示すスクリーンショット。" border="true":::

1. **[種類]** で、 **[DHCP サーバー]** を選択します。 
   
1. **[DHCP サーバー]** で、 **[DHCP Relay]\(DHCP リレー\)** を選択してから、 **[保存]** を選択します。

1. もう一度 **[保存]** を選択し、 **[編集を閉じる]** を選択します。


### <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP アドレスの範囲を指定する

DHCP サーバーへのリレーを作成するときは、DHCP IP アドレスの範囲も指定します。

>[!NOTE]
>IP アドレス範囲は、サブスクリプションやオンプレミス ネットワークにある他の仮想ネットワークで使用されている IP 範囲と重複しないようにする必要があります。

1. NSX-T Manager で、 **[ネットワーク]**  >  **[セグメント]** を選択します。 
   
1. セグメント名の縦方向の省略記号を選択し、 **[編集]** を選択します。
   
1. **[Set Subnets]\(サブネットの設定\)** を選択して、サブネットの DHCP IP アドレスを指定します。 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP IP アドレスを指定するようにサブネットを設定する方法を示すスクリーンショット。" border="true":::
      
1. 必要に応じてゲートウェイ IP アドレスを変更し、DHCP 範囲 IP を入力します。 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="ゲートウェイ IP アドレスと DHCP 範囲を示すスクリーンショット。" border="true":::
      
1. **[適用]** 、 **[保存]** の順に選択します。 DHCP サーバー プールがセグメントに割り当てられます。
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP サーバー プールがセグメントに割り当てられていることを示すスクリーンショット。" border="true":::


## <a name="next-steps"></a>次のステップ

Azure VMware Solution VM から非 NSX-T DHCP サーバーに DHCP 要求を送信するには、「[L2 拡張 VMware HCX ネットワークで DHCP を構成する](configure-l2-stretched-vmware-hcx-networks.md)」を参照してください。
