---
title: マネージド仮想ネットワーク
description: Azure Synapse Analytics のマネージド仮想ネットワークについて説明する記事
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/16/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: 9866a2c773193cc20bd6b9e193e025fa65eddcc6
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446208"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Azure Synapse Analytics のマネージド仮想ネットワーク

この記事では、Azure Synapse Analytics のマネージド仮想ネットワークについて説明します。

## <a name="managed-workspace-virtual-network"></a>マネージド ワークスペース仮想ネットワーク

Azure Synapse ワークスペースを作成するときに、そのワークスペースを Microsoft Azure Virtual Network に関連付けることを選択できます。 ワークスペースに関連付けられた仮想ネットワークは、Azure Synapse によって管理されます。 この仮想ネットワークは、"*マネージド ワークスペース仮想ネットワーク*" と呼ばれます。

マネージド ワークスペース仮想ネットワークによってもたらされる価値として、次の 4 つの点が挙げられます。

- マネージド ワークスペース仮想ネットワークを使用すると、仮想ネットワークの管理負荷を Azure Synapse にオフロードすることができます。
- Azure Synapse 管理トラフィックが仮想ネットワークに入るのを許可するインバウンド NSG ルールを、独自の仮想ネットワークに対して構成する必要はありません。 そのような NSG ルールの構成ミスは、顧客へのサービス中断を招きます。
- Spark クラスター用のサブネットをピーク負荷に基づいて自分で作成する必要がありません。
- マネージド ワークスペース仮想ネットワークは、マネージド プライベート エンドポイントと共に、データ流出への保護対策となります。 マネージド プライベート エンドポイントは、マネージド ワークスペース仮想ネットワークが関連付けられているワークスペースにのみ作成できます。

マネージド ワークスペース仮想ネットワークが関連付けられたワークスペースを作成することによって、そのワークスペースが他のワークスペースから分離されたネットワークであることが保証されます。 Azure Synapse のワークスペースには、さまざまな分析機能が用意されています。データ統合、サーバーレス Apache Spark プール、専用 SQL プール、サーバーレス SQL プールはその例です。

マネージド ワークスペース仮想ネットワークが関連付けられているワークスペースには、データ統合および Spark リソースがデプロイされます。 さらに、マネージド ワークスペース仮想ネットワークでは、Spark のアクティビティに関して、ユーザーレベルの分離性が確保されます。各 Spark クラスターが独自のサブネットに配置されるためです。

専用 SQL プールとサーバーレス SQL プールはマルチテナントの機能であるため、マネージド ワークスペース仮想ネットワークの外に存在します。 ワークスペース内の専用 SQL プールとサーバーレス SQL プールへの通信には、Azure Private Link が使用されます。 このプライベート リンクは、マネージド ワークスペース仮想ネットワークが関連付けられたワークスペースを作成すると自動的に作成されます。

>[!IMPORTANT]
>このワークスペースの構成は、ワークスペースの作成後に変更することはできません。 たとえば、マネージド ワークスペース仮想ネットワークが関連付けられていないワークスペースを再構成して、仮想ネットワークを関連付けることはできません。 同様に、マネージド ワークスペース仮想ネットワークが関連付けられているワークスペースを再構成して、仮想ネットワークの関連付けを解除することもできません。

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>マネージド ワークスペース仮想ネットワークを使用して Azure Synapse ワークスペースを作成する

まだ登録していない場合、ネットワーク リソース プロバイダーを登録します。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 [登録](../../azure-resource-manager/management/resource-providers-and-types.md)時、リソース プロバイダーの一覧から *Microsoft.Network* を選択します。

マネージド ワークスペース仮想ネットワークが関連付けられた Azure Synapse ワークスペースを作成するには、Azure portal の **[Networking]\(ネットワーク\)** タブを選択し、 **[マネージド仮想ネットワークの有効化]** チェック ボックスをオンにします。

チェック ボックスをオフのままにした場合、ワークスペースには仮想ネットワークが関連付けられません。

>[!IMPORTANT]
>プライベート リンクは、マネージド ワークスペース仮想ネットワークがあるワークスペースでしか使用できません。

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-networking-managed-virtual-network-outbound-traffic.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-networking-managed-virtual-network-outbound-traffic.png" alt-text="[Create Synapse workspace]\(Synapse ワークスペースの作成\) の [Networking]\(ネットワーク\) ページのスクリーンショット。[Managed virtual network]\(マネージド仮想ネットワーク\) オプションが [Enable]\(有効\) で、[承認されたターゲットへの送信データ トラフィックのみを許可する] オプションが [Yes]\(はい\) に設定されています。":::

ご利用のワークスペースにマネージド ワークスペース仮想ネットワークを関連付けるよう選択したら、[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)を使用して、マネージド ワークスペース仮想ネットワークからのアウトバウンド接続を承認済みのターゲットにのみ許可することで、データ流出から保護することができます。 マネージド ワークスペース仮想ネットワークからのアウトバウンド トラフィックを、マネージド プライベート エンドポイント経由するターゲットに制限するには、 **[はい]** を選択します。 



:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-allow-outbound-traffic.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-allow-outbound-traffic.png" alt-text="[承認されたターゲットへの送信データ トラフィックのみを許可する] オプションが [Yes]\(はい\) に設定されている、[Managed virtual network]\(マネージド仮想ネットワーク\) ページのスクリーンショット。":::

ワークスペースからのアウトバウンド トラフィックをすべてのターゲットに許可する場合は **[いいえ]** を選択します。

マネージド プライベート エンドポイントの作成先となるターゲットを Azure Synapse ワークスペースから制御することもできます。 ご利用のサブスクリプションと同じ AAD テナント内のリソースに対しては、既定でマネージド プライベート エンドポイントが許可されます。 ご利用のサブスクリプションとは異なる AAD テナント内のリソースに対してマネージド プライベート エンドポイントを作成したい場合は、 **[+ 追加]** を選択して AAD テナントを追加してください。 AAD テナントはドロップダウンから選択できるほか、AAD テナント ID を手動で入力することもできます。

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-private-endpoints-azure-ad.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-managed-virtual-network-private-endpoints-azure-ad.png" alt-text="[Azure AD テナント] の[追加] ボタンが強調表示されている、[Managed virtual network]\(マネージド仮想ネットワーク\) ページのスクリーンショット。":::

ワークスペースの作成後、Azure Synapse ワークスペースがマネージド ワークスペース仮想ネットワークに関連付けられているかどうかは、Azure portal の **[概要]** を選択することで確認できます。

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-overview-managed-virtual-network-enabled.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-analytics-overview-managed-virtual-network-enabled.png" alt-text="[Managed virtual network]\(マネージド仮想ネットワーク\) が有効であることを示す、Azure Synapse ワークスペースの概要ページのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Azure Synapse ワークスペース](../quickstart-create-workspace.md)を作成する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)について理解を深める

[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)
