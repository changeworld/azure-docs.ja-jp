---
title: Azure Synapse の接続設定
description: Azure Synapse Analytics で接続設定を構成する方法の説明記事
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/05/2021
author: ashinMSFT
ms.author: seshin
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 58086bcbe321b7d50d6f5d0e41a9723dfbe512ed
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861648"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure Synapse Analytics の接続設定

この記事では、Azure Synapse Analytics の接続設定と、必要に応じてそれらを構成する方法について説明します。

## <a name="public-network-access"></a>パブリック ネットワーク アクセス 

パブリック ネットワーク アクセス機能を使用して、パブリック ネットワークから Azure Synapse ワークスペースへの着信接続を許可できます。 

- パブリック ネットワーク アクセスが **無効** のときは、[プライベート エンドポイント](synapse-workspace-managed-private-endpoints.md)経由でのみ、ワークスペースに接続できます。 
- パブリック ネットワーク アクセスが **有効** のときは、パブリック ネットワークからもワークスペースに接続できます。 この機能の管理は、ワークスペース作成時でも後からでもできます。 

> [!IMPORTANT]
> この機能は、[Azure Synapse Analytics のマネージド仮想ネットワーク](synapse-workspace-managed-vnet.md)に関連付けられている Azure Synapse ワークスペースでのみ使用できます。 ただし、マネージド VNet との関連付けに関係なく、パブリック ネットワークへの Synapse ワークスペースを開くことができます。 

**[無効]** オプションを選択した場合、構成したファイアウォール規則は適用されません。 さらに、ファイアウォール規則が Synapse portal のネットワーク設定でグレー表示されます。 パブリック ネットワーク アクセスを再び有効にすると、ファイアウォールの構成が再適用されます。 

> [!TIP]
> 有効に戻したときは、ファイアウォール規則を編集するまで少し時間を置いてください。

### <a name="configure-public-network-access-when-you-create-your-workspace"></a>ワークスペースの作成時にパブリック ネットワーク アクセスを構成する

1.    [Azure portal](https://aka.ms/azureportal) でワークスペースを作成するときに、 **[ネットワーク]** タブを選択します。
2.    [Managed virtual network]\(マネージド仮想ネットワーク\) で、 **[Enable]\(有効にする\)** を選択して、ワークスペースをマネージド仮想ネットワークに関連付け、パブリック ネットワーク アクセスを許可します。 

       :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png" alt-text="[Create Synapse workspace]\(Synapse ワークスペースの作成\) の [ネットワーク] タブの [Managed virtual network]\(マネージド仮想ネットワーク\) の設定" lightbox="media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png":::

3. **[公衆ネットワーク アクセス]** で、 **[Disable]\(無効にする\)** を選択してワークスペースへのパブリック アクセスを拒否します。 ワークスペースへのパブリック アクセスを許可する場合は、 **[Enable]\(有効にする\)** を選択します。

   :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-public-network-access-2.png" alt-text="Synapse ワークスペースの作成、ネットワークタブ、パブリック ネットワーク アクセス設定" lightbox="media/connectivity-settings/create-synapse-workspace-public-network-access-2.png"::: 

4.    残りのワークスペース作成フローを完了します。

### <a name="configure-public-network-access-after-you-create-your-workspace"></a>ワークスペースを作成した後でパブリック ネットワーク アクセスを構成する

1.    [Azure portal](https://aka.ms/azureportal) で Synapse ワークスペースを選択します。
2.    左側のナビゲーションで **[ネットワーク]** を選択します。
3.    **[Disable]\(無効にする\)** を選択してワークスペースへのパブリック アクセスを拒否します。 ワークスペースへのパブリック アクセスを許可する場合は、 **[Enable]\(有効にする\)** を選択します。

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png" alt-text="既存の Synapse ワークスペースの [ネットワーク] タブでパブリック ネットワーク アクセスの設定が有効になっています" lightbox="media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png"::: 

4.    無効になっている場合、**ファイアウォール規則** はグレーで表示されます。これは、ファイアウォール規則が無効であることを示します。 ファイアウォール規則の構成は保持されます。 

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png" alt-text="既存の Synapse ワークスペースの [ネットワーク] タブでパブリック ネットワーク アクセスの設定が無効になっています。ファイアウォール規則に注意してください" lightbox="media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png"::: 
 
5.    **[保存]** を選択して変更を保存します。 ネットワーク設定が正常に保存されたことを通知するメッセージが表示されます。

## <a name="connection-policy"></a>接続ポリシー
Azure Synapse Analytics での Synapse SQL の接続ポリシーは、"*既定値*" に設定されています。 Azure Synapse Analytics でこれを変更することはできません。 Azure Synapse Analytics での Synapse SQL への接続に対する影響については、[こちら](../../azure-sql/database/connectivity-architecture.md#connection-policy)を参照してください。 

## <a name="minimal-tls-version"></a>TLS の最小バージョン
Azure Synapse Analytics の Synapse SQL では、すべての TLS バージョンを使用した接続が許可されます。 Azure Synapse Analytics の Synapse SQL に対して最小 TLS バージョンを設定することはできません。

## <a name="next-steps"></a>次のステップ

 - [Azure Synapse ワークスペース](./synapse-workspace-ip-firewall.md)を作成します。