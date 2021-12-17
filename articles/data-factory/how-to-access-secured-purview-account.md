---
title: セキュリティで保護された Azure Purview アカウントへのアクセス
description: Azure Data Factory でプライベート エンドポイントを使用して、ファイアウォールで保護された Azure Purview アカウントにアクセスする方法を説明します
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 09/02/2021
ms.openlocfilehash: 51da9cba732c5654709d0c1e84398035d4675cc8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005729"
---
# <a name="access-a-secured-azure-purview-account-from-azure-data-factory"></a>保護された Azure Purview アカウントに Azure Data Factory でアクセスする

この記事では、保護された Azure Purview アカウントに Azure Data Factory でアクセスする方法を、さまざまな連携シナリオ別に説明します。

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview プライベート エンドポイントをデプロイするシナリオ

[Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md) を使用すれば、Azure Purview アカウントで、Private Link を通じて、仮想ネットワーク (VNet) からカタログへの保護されたアクセスを実行できます。 Purview では次のとおり、さまざまなアクセス方法に対応した数種類のプライベート エンドポイントを用意しています: *アカウント* プライベート エンドポイント、*ポータル* プライベート エンドポイント、*インジェスト* プライベート エンドポイント。 [Purview プライベート エンドポイントの概要](../purview/catalog-private-link.md#conceptual-overview)を読む。 

Purview アカウントをファイアウォールで保護し、一般のアクセスを拒否している場合は、次のチェックリストを使用してプライベート エンドポイントをセットアップし、Data Factory から Purview に接続できるようにします。 

| シナリオ                                                     | 必要な Purview プライベート エンドポイント                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [パイプラインを実行して系列を Purview に報告する](tutorial-push-lineage-to-purview.md) | Data Factory パイプラインで Purview に系列をプッシュするには、Purview ***account** _ と _*_インジェスト_*_ プライベート エンドポイントが必要です。 <br>- _*Azure Integration Runtime** を使用するときは、[Purview のマネージド プレイベート エンドポイント](#managed-private-endpoints-for-purview)に関するセクションの手順に従って、Data Factory マネージド仮想ネットワークにマネージド プライベート エンドポイントを作成します。<br>- **セルフホステッド統合ランタイム** を使用するときは、[このセクション](../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts)の手順に従って、統合ランタイムの仮想ネットワークに *アカウント* および *インジェスト* プライベート エンドポイントを作成します。 |
| [ADF UI で Purview を使用してデータの検出、探索を行う](how-to-discover-explore-purview-data.md) | Data Factory 作成 UI の上部中央にある検索バーで Purview データを検索してアクションを実行するには、Data Factory Studio を起動する仮想ネットワークで、Purview ***account** _ と _*_ポータル_*_ プライベート エンドポイントを作成する必要があります。 [_account* と *ポータル* プライベート エンドポイントの有効化](../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)に関するセクションの手順に従います。 |

## <a name="managed-private-endpoints-for-purview"></a>Purview のマネージド プライベート エンドポイント

[マネージド プライベート エンドポイント](managed-virtual-network-private-endpoint.md#managed-private-endpoints)は、Azure リソースへのプライベート リンクを作成するために、Azure Data Factory Managed Virtual Network に作成するプライベート エンドポイントです。 パイプラインを実行して、ファイアウォールで保護している Azure Purview アカウントに系列を報告するときは、[Virtual network configuration]\(仮想ネットワーク構成\) オプションを有効にして Azure Integration Runtime を作成してから、次の手順で Purview ***account** _ と _ *_ingestion_** マネージド プライベート エンドポイントを作成します。

### <a name="create-managed-private-endpoints"></a>マネージド プライベート エンドポイントを作成する

Data Factory 作成 UI で Purview のマネージド プライベート エンドポイントを作成するには

1. **[Manage]\(管理\)**  ->  **[Azure Purview]** に移動し、 **[Edit]\(編集\)** をクリックして既存の接続済み Purview アカウントを編集するか、 **[Connect to a Purview account]\(Purview アカウントに接続\)** をクリックして新しい Purview アカウントに接続します。

2. **[Create managed private endpoints]\(マネージド プライベート エンドポイントを作成する\)** で **[Yes]\(はい\)** を選択します。 このオプションを表示するには、[Virtual network configuration]\(仮想ネットワーク構成\) オプションを有効にした Azure Integration Runtime が、データ ファクトリに少なくとも 1 つ必要です。

3. **[+ Create all]\(すべてを作成\)** をクリックして、必要な Purview プライベート エンドポイントを一括作成します。ここで必要なエンドポイントには、Purview マネージド リソースの **_account_ *_ プライベート エンドポイント、_* _ingestion_ *_ プライベート エンドポイントなどがあります。また、Purview マネージド リソースには、Blob ストレージ、Queue ストレージ、Event Hubs の名前空間があります。Purview マネージド リソースの情報を取得するには、Data Factory 用の Purview アカウントで、少なくとも _* Reader** ロールが必要です。

   :::image type="content" source="./media/how-to-access-secured-purview-account/purview-create-all-managed-private-endpoints.png" alt-text="接続した Purview アカウントにプライベート エンドポイントを作成する。":::

4. 次のページで、プライベート エンドポイントの名前を指定します。 インジェスト プライベート エンドポイントの名前も、これとサフィックスを使用して生成されます。

   :::image type="content" source="./media/how-to-access-secured-purview-account/name-purview-private-endpoints.png" alt-text="接続した Purview アカウントのマネージド プライベート エンドポイントの名前を指定します。":::

5. **[Create]\(作成\)** をクリックしてプライベート エンドポイントを作成します。 作成後 4 つのプライベート エンドポイント要求が生成されるので、[Purview の所有者がこれらを承認](#approve-private-endpoint-connections)する必要があります。

このようなバッチマネージド プライベート エンドポイントの作成は、Purview UI でのみ提供されます。 プログラムを使用してマネージド プライベート エンドポイントを作成する場合は、これらのプライベート エンドポイントを個別に作成する必要があります。 Purview マネージド リソースの情報は、Azure portal -> 目的の Purview アカウント -> [Managed resources]\(マネージド リソース\) で見られます。

### <a name="approve-private-endpoint-connections"></a>プライベート エンドポイントの接続を承認する

Purview マネージド プライベート エンドポイントを作成すると、はじめは Pending (保留) の状態になっています。 Purview の所有者は、リソースごとにプライベート エンドポイントの接続を承認する必要があります。

Purview プライベート エンドポイントの接続を承認する権限がある場合は、Data Factory UI で次の手順を実行します。 

1. **[Manage]\(管理\)**  ->  **[Azure Purview]**  ->  **[Edit]\(編集\)** に移動します
2. プライベート エンドポイントのリストで、各プライベート エンドポイント名の隣の **[Edit]\(編集\)** (鉛筆ボタン) をクリックします
3. **[Manage approvals in Azure portal]\(Azure portal で承認を管理\)** をクリックしてリソースに移動します。
4. そのリソースで、 **[Networking]\(ネットワーク\)**  ->  **[Private endpoint connection]\(プライベート エンドポイントの接続\)** に移動して承認を行います。 プライベート エンドポイントは `data_factory_name.your_defined_private_endpoint_name` という名前になっており、“Requested by data_factory_name” (data_factory_name の要求による) という説明が付いています。
5. すべてのプライベート エンドポイントに対しこの操作を繰り返します。

Purview プライベート エンドポイントの接続を承認する権限がない場合は、次の手順を実行するよう Purview アカウントの所有者に依頼します。

- *アカウント* プライベート エンドポイントについては、Azure portal -> 目的の Purview アカウント -> [Networking]\(ネットワーク\) -> 承認するプライベート エンドポイントの接続、に移動します。
- *インジェスト* プライベート エンドポイントについては、Azure portal -> 目的の Purview アカウント -> [Managed resources]\(マネージド リソース\) に移動し、Storage アカウントと Event Hubs の名前空間をそれぞれクリックし、[Networking]\(ネットワーク\) -> [Private endpoint connection]\(プライベート エンドポイントの接続\) ページでプライベート エンドポイントの接続を承認します。

### <a name="monitor-managed-private-endpoints"></a>マネージド プライベート エンドポイントを監視する

Purview でマネージド プライベート エンドポイントを作成したら、2 つの場所でそれを監視できます。

- **[Manage]\(管理\)**  ->  **[Azure Purview]**  ->  **[Edit]\(編集\)** に移動して、既存の接続済み Purview アカウントを開きます。 関連するすべてのプライベート エンドポイントを表示するには、Data Factory で Purview マネージド リソースの情報を取得するために、Purview アカウントで少なくとも **Reader** ロールを持っている必要があります。 そうしないと、警告の付いた *アカウント* プライベート エンドポイントしか表示されません。
- **[Manage]\(管理\)**  ->  **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** に移動すると、データ ファクトリーに作成したすべてのマネージド プライベート エンドポイントを見ることができます。 Purview アカウントで少なくとも **Reader** ロールを持っていれば、関連するエンドポイント同士をグループ化した形で、Purview のプライベート エンドポイントが表示されます。 そうでない場合は、リスト上に個別に表示されます。

## <a name="nextsteps"></a>次のステップ 

- [Data Factory を Azure Purview に接続する](connect-data-factory-to-azure-purview.md)
- [チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](tutorial-push-lineage-to-purview.md)
- [Purview を使用した ADF でのデータの検出と探索](how-to-discover-explore-purview-data.md)
