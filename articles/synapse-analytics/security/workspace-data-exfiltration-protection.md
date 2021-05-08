---
title: Azure Synapse Analytics ワークスペースでのデータ流出の防止
description: この記事では、Azure Synapse Analytics でのデータ流出の防止について説明します。
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 5fed7649353c0ef6cb222c0b0c91a5203665e9fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598050"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics ワークスペースでのデータ流出の防止
この記事では、Azure Synapse Analytics でのデータ流出の防止について説明します。

## <a name="securing-data-egress-from-synapse-workspaces"></a>Synapse ワークスペースからのデータ エグレスのセキュリティ保護
Azure Synapse Analytics ワークスペースでは、ワークスペースのデータ流出の防止を有効化することがサポートされています。 流出防止を使用すると、悪意のある内部関係者が Azure リソースにアクセスし、機密データを組織の範囲外の場所に流出させることから保護できます。 ワークスペースの作成時に、マネージド仮想ネットワークと、データ流出に対する追加の保護を使用して、ワークスペースを構成することを選択できます。 [マネージド仮想ネットワーク](./synapse-workspace-managed-vnet.md)を使用してワークスペースを作成するとき、データ統合および Spark リソースがマネージド仮想ネットワークにデプロイされます。 ワークスペースの専用 SQL プールとサーバーレス SQL プールにはマルチテナント機能があり、そのため、マネージド仮想ネットワークの外部に存在する必要があります。 データ流出の防止を備えたワークスペースでは、マネージド仮想ネットワーク内のリソースは常に[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)を経由して通信し、Synapse SQL リソースは、承認された Azure リソース (ワークスペースからの承認されたマネージド プライベート エンドポイント接続のターゲット) にのみ接続できます。 

> [!Note]
> ワークスペースの作成後に、マネージド仮想ネットワークとデータ流出の防止のためのワークスペース構成を変更することはできません。

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>承認されたターゲットへの Synapse ワークスペース データ送信の管理
データ流出の防止を有効にしてワークスペースが作成された後、ワークスペース リソースの所有者は、ワークスペースに対して承認された Azure AD テナントの一覧を管理できます。 ワークスペース上の[適切なアクセス許可](./synapse-workspace-access-control-overview.md)を持つユーザーは、Synapse Studio を使用して、ワークスペースの承認された Azure AD テナント内のリソースに対するマネージド プライベート エンドポイント接続要求を作成できます。 未承認のテナントにあるリソースへのプライベート エンドポイント接続をユーザーが作成しようとすると、マネージド プライベート エンドポイントの作成はブロックされます。

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>データ流出の防止が有効になっているワークスペースの例
例を使用して、Synapse ワークスペースのデータ流出防止について説明します。 Contoso ではテナント A とテナント B に Azure リソースがあり、これらのリソースを安全に接続する必要があります。 テナント A に Synapse ワークスペースが作成され、承認された Azure AD テナントとしてテナント B が追加されました。 この図には、ストレージ アカウント所有者によって承認されたテナント A とテナント B の Azure Storage アカウントへのプライベート エンドポイント接続が示されています。 この図には、ブロックされたプライベート エンドポイントの作成も示されています。 このプライベート エンドポイントの作成は、Contoso のワークスペースで承認された Azure AD テナントでない Fabrikam Azure AD テナント内の Azure Storage アカウントがターゲットとされたためブロックされました。

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="この図は、Synapse ワークスペースに対してデータ流出の防止を実装する方法を示しています":::

>[!IMPORTANT]
>
> - ワークスペースのテナント以外のテナント内のリソースには、SQL プールからそれらへの接続をブロックするファイアウォール規則を設定しないでください。 Spark クラスターなどワークスペースのマネージド仮想ネットワーク内のリソースは、マネージド プライベート リンクを介してファイアウォールで保護されたリソースに接続できます。
>
> - Synapse ワークスペースのマネージド仮想ネットワークでデータ流出保護が有効になっている場合、メタストアは無効になります。 これらのワークスペースで Spark SQL を使用することはできません。
> >

## <a name="next-steps"></a>次の手順

[データ流出の防止が有効になっているワークスペースを作成する](./how-to-create-a-workspace-with-data-exfiltration-protection.md)方法を学習する

[マネージド ワークスペースの Virtual Network](./synapse-workspace-managed-vnet.md) の詳細を学習する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を学習する

[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)
