---
title: セキュリティで保護された Azure Purview アカウントへのアクセス
description: ファイアウォールで保護された Azure Purview アカウントにプライベート エンドポイントを通じて Synapse からアクセスする方法について説明します
author: linda33wj
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: how-to
ms.date: 09/02/2021
ms.author: jingwang
ms.reviewer: jrasnick
ms.openlocfilehash: e147ee8cd6483ab32fee0fe393b104669370ab5c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440142"
---
# <a name="access-a-secured-azure-purview-account-from-azure-synapse-analytics"></a>セキュリティで保護された Azure Purview アカウントに Azure Synapse Analytics からアクセスする

この記事では、さまざまな統合シナリオ向けに、セキュリティで保護された Azure Purview アカウントに Azure Synapse Analytics からアクセスする方法について説明します。

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview プライベート エンドポイントのデプロイ シナリオ

Azure Purview アカウントに [Azure プライベート エンドポイント](../../private-link/private-endpoint-overview.md)を使用することで、プライベート リンクを介して仮想ネットワーク (VNet) からカタログに安全にアクセスできます。 Purview では、さまざまなアクセス ニーズに応じて異なる種類のプライベート ポイントを提供します ("*アカウント*" プライベート エンドポイント、"*ポータル*" プライベート エンドポイント、"*インジェスト*" プライベート エンドポイント)。 詳細については、[Purview プライベート エンドポイントの概念の概要](../../purview/catalog-private-link.md#conceptual-overview)に関するページを参照してください。 

ご自分の Purview アカウントがファイアウォールで保護されていて、パブリック アクセスを拒否する場合は、Synapse が Purview に正常に接続できるように、下のチェックリストに従ってプライベート エンドポイントを設定してください。 

| シナリオ                                                     | 必要な Purview プライベート エンドポイント                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [パイプラインを実行して系列を Purview にレポートする](../../purview/how-to-lineage-azure-synapse-analytics.md) | Synapse パイプラインで系列を Purview にプッシュするには、"***アカウント** _" および "_*_インジェスト_*_" プライベート エンドポイントが必要です。 <br>- _*Azure Integration Runtime** を使用している場合は、「[Purview 用のマネージド プライベート エンドポイント](#managed-private-endpoints-for-purview)」セクションの手順に従って、Synapse によって管理される仮想ネットワークにマネージド プライベート エンドポイントを作成します。<br>**セルフホステッド統合ランタイム** を使用している場合は、[このセクション](../../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts)の手順に従って、ご自分の統合ランタイムの仮想ネットワークに "*アカウント*" および "*インジェスト*" プライベート エンドポイントを作成します。 |
| [Synapse Studio で Purview を使用してデータを検出および探索する](how-to-discover-connect-analyze-azure-purview.md) | Synapse Studio の上部中央にある検索バーを使用し、Purview データを検索してアクションを実行するには、Synapse Studio を起動した仮想ネットワークに Purview の "**アカウント**" および "_*_ポータル_*_" プライベート エンドポイントを作成する必要があります。 ["_アカウント*" および "*ポータル*" プライベート エンドポイントの有効化](../../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)に関する記事の手順に従ってください。 |

## <a name="managed-private-endpoints-for-purview"></a>Purview 用のマネージド プライベート エンドポイント

[マネージド プライベート エンドポイント](../security/synapse-workspace-managed-private-endpoints.md)は、ご自分の Azure Synapse ワークスペースに関連付けられているマネージド仮想ネットワークに作成されたプライベート エンドポイントです。 パイプラインを実行し、ファイアウォールで保護された Azure Purview アカウントに系列をレポートする場合、"マネージド仮想ネットワーク" のオプションを有効にして Synapse ワークスペースが作成されていることを確認してから、次のとおりに Purview "***アカウント** _" および "_ *_インジェスト_**" マネージド プライベート エンドポイントを作成します。

### <a name="create-managed-private-endpoints"></a>マネージド プライベート エンドポイントを作成する

Synapse Studio で Purview 用のマネージド プライベート エンドポイントを作成するには:

1. **[管理]**  ->  **[Azure Purview]** の順に移動し、 **[編集]** をクリックして既存の接続済み Purview アカウントを編集するか、 **[Purview アカウントに接続]** をクリックして新しい Purview アカウントに接続します。

2. **[マネージド プライベート エンドポイントの作成]** で **[はい]** を選択します。 "**workspaces/managedPrivateEndpoint/write**" アクセス許可 (Synapse 管理者や Synapse のリンクされた Data Manager のロールなど) が付与されている必要があります。

3. **+ [すべて作成する]** ボタンをクリックして、Purview の管理対象リソース (BLOB ストレージ、キュー ストレージ、Event Hubs 名前空間) 用に "**_アカウント_ *_" プライベート エンドポイントや "_* _インジェスト_ *_" プライベート エンドポイントなど、必要な Purview プライベート エンドポイントをバッチ作成します。Synapse が Purview の管理対象リソースの情報を取得するには、Purview アカウントに少なくとも _* 閲覧者** ロールが必要です。

   :::image type="content" source="./media/purview-create-all-managed-private-endpoints.png" alt-text="接続した Purview アカウント用のマネージド プライベート エンドポイントを作成する。":::

4. 次のページで、プライベート エンドポイントの名前を指定します。 これは、サフィックスと一緒にインジェスト プライベート エンドポイントの名前を生成するためにも使用されます。

   :::image type="content" source="./media/name-purview-private-endpoints.png" alt-text="接続した Purview アカウント用のマネージド プライベート エンドポイントに名前を付ける。":::

5. **[作成]** をクリックしてプライベート エンドポイントを作成します。 作成後、[Purview の所有者によって承認される必要がある](#approve-private-endpoint-connections) 4 つのプライベート エンドポイント要求が生成されます。

このようなマネージド プライベート エンドポイントのバッチ作成は、Synapse Studio でのみ実行できます。 マネージド プライベート エンドポイントをプログラムで作成したい場合は、それらの PE を個別に作成する必要があります。 Purview の管理対象リソースの情報は、Azure portal -> 自分の Purview アカウント -> [マネージド リソース] から確認できます。

### <a name="approve-private-endpoint-connections"></a>プライベート エンドポイント接続を承認する

Purview 用のマネージド プライベート エンドポイントを作成した後、最初に "保留中" の状態が表示されます。 Purview の所有者は、リソースごとにプライベート エンドポイント接続を承認する必要があります。

Purview プライベート エンドポイント接続を承認するアクセス許可がある場合は、Synapse Studio で: 

1. **[管理]**  ->  **[Azure Purview]**  ->  **[編集]** の順に移動します
2. プライベート エンドポイントの一覧で、各プライベート エンドポイント名の横にある **[編集]** (鉛筆) ボタンをクリックします
3. **[Manage approvals in Azure portal]\(Azure portal で承認を管理する\)** をクリックします。これにより、リソースが表示されます。
4. 指定のリソースで、 **[ネットワーク]**  ->  **[プライベート エンドポイント接続]** の順に移動して、それを承認します。 プライベート エンドポイントには `data_factory_name.your_defined_private_endpoint_name` という名前が付けられ、"Requested by data_factory_name (data_factory_name によって要求されました)" という説明が付けられます。
5. すべてのプライベート エンドポイントについて、この操作を繰り返します。

Purview プライベート エンドポイント接続を承認するアクセス許可がない場合は、Purview アカウント オーナーに次のとおりにするよう依頼してください。

- "*アカウント*" プライベート エンドポイントの場合、Azure portal -> 自分の Purview アカウント -> [ネットワーク] -> [プライベート エンドポイント接続] の順に移動して承認します。
- "*インジェスト*" プライベート エンドポイントの場合、Azure portal -> 自分の Purview アカウント -> [マネージド リソース] の順に移動して、ストレージ アカウントと Event Hubs 名前空間をそれぞれクリックし、[ネットワーク] -> [プライベート エンドポイント接続] ページでプライベート エンドポイント接続を承認します。

### <a name="monitor-managed-private-endpoints"></a>マネージド プライベート エンドポイントを監視する

Purview 用に作成されたマネージド プライベート エンドポイントは 2 つの場所で監視できます。

- **[管理]**  ->  **[Azure Purview]**  ->  **[編集]** の順に移動して、既存の接続済み Purview アカウントを開きます。 関連するプライベート エンドポイントをすべて表示するには、Synapse が Purview の管理対象リソースの情報を取得するための **閲覧者** ロールが Purview アカウントに少なくとも必要です。 これがない場合は、"*アカウント*" プライベート エンドポイントのみが警告と共に表示されます。
- **[管理]**  ->  **[マネージド プライベート エンドポイント]** の順に移動します。そこに、Synapse ワークスペースで作成されたマネージド プライベート エンドポイントがすべて表示されます。 Purview アカウントに少なくとも **閲覧者** ロールがある場合は、Purview に関連するプライベート エンドポイントがグループ化されて表示されます。 そうでない場合は、それらは一覧に別々に表示されます。

## <a name="nextsteps"></a>次のステップ 

- [Azure Purview への Synapse ワークスペースの接続](quickstart-connect-azure-purview.md)
- [Azure Synapse Analytics のメタデータと系列](../../purview/how-to-lineage-azure-synapse-analytics.md)
- [Azure Purview を使用した Synapse でのデータの検出、接続、調査](how-to-discover-connect-analyze-azure-purview.md)