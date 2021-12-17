---
title: 同じリージョン内の複数の Azure VMware Solution プライベート クラウドを接続する
description: 同じリージョンにある複数の Azure VMware Solution プライベート クラウド間にネットワーク接続を作成する方法について説明します。
ms.topic: how-to
ms.date: 09/20/2021
ms.openlocfilehash: daa4b60c0e42c77f61054d6d9a77898cc0448c37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567843"
---
# <a name="connect-multiple-azure-vmware-solution-private-clouds-in-the-same-region"></a>同じリージョン内の複数の Azure VMware Solution プライベート クラウドを接続する

**AVS Interconnect** 機能を使用すると、同じリージョンにある複数の Azure VMware Solution プライベート クラウド間にネットワーク接続を作成することができます。 これにより、プライベート クラウドの管理ネットワークとワークロード ネットワーク間にルーティング リンクが作成され、クラウド間のネットワーク通信が可能になります。

1 つのプライベート クラウドを複数のプライベート クラウドに接続することができます。また、その接続は非推移的です。 たとえば、"_プライベート クラウド 1_" が "_プライベート クラウド 2_" に接続され、"_プライベート クラウド 2_" が "_プライベート クラウド 3_" に接続されている場合、プライベート クラウド 1 と 3 は、直接接続されるまで通信しません。

同じリージョン内のプライベート クラウドのみを接続できます。 異なるリージョンにあるプライベート クラウドを接続するには、[ExpressRoute Global Reach を使用](tutorial-expressroute-global-reach-private-cloud.md)して、オンプレミス回線にプライベート クラウドを接続する場合と同じ方法で、プライベート クラウドを接続します。 

## <a name="supported-regions"></a>サポートされているリージョン

AVS Interconnect 機能は、米国中南部 (SAT20)、北ヨーロッパ (DUB21)、東南アジア (SG2)、英国西部 (CWL20) を除くすべてのリージョンで使用できます。 

## <a name="prerequisites"></a>前提条件

- 接続している各プライベート クラウドへの書き込みアクセス権
- 各クラウドのルーティングされた IP アドレス空間が一意であり、重複していない

>[!NOTE]
>**AVS Interconnect** 機能の場合、ネイティブの Azure VNet ピアリングでピアリングの作成前に実行されるような IP 空間の重複チェックが実行されません。 そのため、プライベート クラウド間に重複がないことをお客様が確認する必要があります。
>
>Azure VMware Solution 環境では、Azure にルーティングされていない NSX セグメントに、ルーティングされていない重複する IP 配置を構成することができます。  各プライベート クラウド上の NSX T0 間でのみルーティングされるため、これで AVS Interconnect 機能に関する問題が発生することはありません。


## <a name="add-connection-between-private-clouds"></a>プライベート クラウド間の接続を追加する

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[接続]** を選択します。

2. **[AVS Interconnect]** タブを選択し、 **[追加]** をクリックします。

   :::image type="content" source="media/networking/private-cloud-to-private-cloud-no-connections.png" alt-text="[接続] の下にある [AVS Interconnect] タブを示すスクリーンショット。" border="true" lightbox="media/networking/private-cloud-to-private-cloud-no-connections.png":::

3. 新しい接続のために、情報と Azure VMware Solution プライベート クラウドを選択します。

   >[!NOTE]
   >同じリージョン内のプライベート クラウドにのみ接続できます。 異なるリージョンにあるプライベート クラウドに接続するには、[ExpressRoute Global Reach を使用](tutorial-expressroute-global-reach-private-cloud.md)して、オンプレミス回線にプライベート クラウドを接続する場合と同じ方法で、プライベート クラウドを接続します。 

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud.png" alt-text="他のプライベート クラウドへの接続を追加するために必要な情報を表示したスクリーンショット。" border="true":::


4. **[I confirm]\(確認しました\)** チェックボックスをオンにして、2 つのプライベート クラウドに重複するルーティングされた IP 空間がないことを確認します。 

5. **［作成］** を選択します  接続の作成状態を確認することができます。

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud-notification.png" alt-text="進行中の接続と既存の接続の通知情報を示すスクリーンショット。" border="true":::

   **[AVS プライベート クラウド]** の下に、すべての接続が表示されます。
   
   :::image type="content" source="media/networking/private-cloud-to-private-cloud-two-connections.png" alt-text="[接続] の下にある [AVS Interconnect] タブと、確立された 2 つのプライベート クラウド接続を示すスクリーンショット。" border="true" lightbox="media/networking/private-cloud-to-private-cloud-two-connections.png":::


## <a name="remove-connection-between-private-clouds"></a>プライベート クラウド間の接続を削除する

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[接続]** を選択します。

2. 削除する接続について、右側の **[削除]** (ごみ箱) を選択し、 **[はい]** を選択します。


## <a name="next-steps"></a>次のステップ

同じリージョン内にある複数のプライベート クラウドを接続したので、次のことについて学習することをお勧めします。

- [Azure VMware Solution のリソースを別のリージョンに移動する](move-azure-vmware-solution-across-regions.md)
- [Azure VMware Solution サブスクリプションを別のサブスクリプションに移動する](move-ea-csp-subscriptions.md)
