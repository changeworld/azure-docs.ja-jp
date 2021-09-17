---
title: IP ファイアウォール規則を構成する
description: Azure Synapse Analytics で IP ファイアウォール規則を構成する方法を説明した記事
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/15/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: a375c4c99e3b86706f9fed7bc52f3064995cd21c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252785"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Azure Synapse Analytics の IP ファイアウォール規則

この記事では、IP ファイアウォール規則について説明し、それを Azure Synapse Analytics で構成する方法を示します。

## <a name="ip-firewall-rules"></a>IP ファイアウォール規則

IP ファイアウォール規則は、各要求の発信元 IP アドレスに基づいて Synapse ワークスペースへのアクセス権を付与または拒否します。 お使いのワークスペースに対して IP ファイアウォール規則を構成できます。 ワークスペース レベルで構成された IP ファイアウォール規則は、ワークスペースのすべてのパブリック エンドポイント (専用 SQL プール、サーバーレス SQL プール、開発) に適用されます。

## <a name="create-and-manage-ip-firewall-rules"></a>IP ファイアウォール規則の作成および管理

IP ファイアウォール規則を Azure Synapse ワークスペースに追加するには、2 つの方法があります。 IP ファイアウォールをワークスペースに追加するには、ワークスペースの作成時に **[ネットワーク]** を選択し、 **[Allow connections from all IP addresses]\(すべての IP アドレスからの接続を許可する\)** をオンにします。

> [!Important]
> この機能は、マネージド VNet に関連付けられていない Azure Synapse ワークスペースでのみ使用できます。

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" alt-text="[セキュリティ] タブと [Allow connections from all IP addresses]\(すべての IP アドレスからの接続を許可する\) チェックボックスが強調表示されているスクリーンショット。":::


また、ワークスペースの作成後に、Synapse ワークスペースに IP ファイアウォール規則を追加することもできます。 Azure portal の **[セキュリティ]** の下で **[ファイアウォール]** を選択します。 新しい IP ファイアウォール規則を追加するには、それに名前、開始 IP、および終了 IP を指定します。 完了したら、 **[保存]** を選択します。

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" alt-text="Synapse ワークスペースの [ネットワーク] ページのスクリーンショット。[クライアント IP の追加] ボタンと [ルール] フィールドが強調表示されています。":::

## <a name="connect-to-synapse-from-your-own-network"></a>独自のネットワークから Synapse に接続する

Synapse Studio を使用して、Synapse ワークスペースに接続できます。 また、SQL Server Management Studio (SSMS) を使用して、ワークスペース内の SQL リソース (専用 SQL プールとサーバーレス SQL プール) に接続することもできます。

ネットワークおよびローカル コンピューターのファイアウォールで、Synapse Studio に対して TCP ポート 80、443、および 1443 での送信通信を許可するように設定してください。

また、Synapse Studio に対して UDP ポート 53 での送信通信を許可する必要があります。 SSMS や Power BI などのツールを使用して接続するには、TCP ポート1433での送信通信を許可する必要があります。


## <a name="next-steps"></a>次のステップ

[Azure Synapse ワークスペース](../quickstart-create-workspace.md)を作成する

[マネージド ワークスペース仮想ネットワーク](./synapse-workspace-managed-vnet.md)を使用して Azure Synapse ワークスペースを作成する