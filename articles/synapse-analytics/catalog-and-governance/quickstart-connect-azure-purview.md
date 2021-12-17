---
title: Synapse ワークスペースを Azure Purview に接続する 
description: Synapse ワークスペースを Azure Purview アカウントに接続します。
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 09/29/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 86e2eee3be8a25cc236a5774ab485c863c8d655b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842527"
---
# <a name="quickstartconnect-a-synapse-workspace-to-an-azure-purview-account"></a>クイックスタート: Synapse ワークスペースを Azure Purview アカウントに接続する

このクイックスタートでは、Azure Purview アカウントを Synapse ワークスペースに登録します。 この接続により、Azure Purview の資産を検出し、Synapse の機能を使用してそれらを操作し、系列情報を Purview にプッシュできるようになります。

Synapse では、次のタスクを実行できます。
- 上部にある検索ボックスを使用して、キーワードに基づいて Purview の資産を検索する 
- メタデータ、[系列](../../purview/catalog-lineage-user-guide.md)、注釈に基づいてデータを理解する 
- リンクされたサービスまたは統合データセットを使用してこれらのデータをワークスペースに接続する 
- Synapse Apache Spark、Synapse SQL、および Data Flow を使用してこれらのデータセットを分析する 
- パイプラインを実行し、[系列情報を Purview にプッシュする](../../purview/how-to-lineage-azure-synapse-analytics.md)

## <a name="prerequisites"></a>前提条件 
- [Azure Purview アカウント](../../purview/create-catalog-portal.md) 
- [Synapse ワークスペース](../quickstart-create-workspace.md) 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Azure Purview アカウントを接続するためのアクセス許可 

Azure Purview アカウントを Synapse ワークスペースに接続するには、Azure portal IAM からの Synapse ワークスペースの **共同作成者** ロールが必要です。また、その Azure Purview アカウントにアクセスできる必要があります。 詳細については、[Azure Purview のアクセス許可](../../purview/catalog-permissions.md)に関するページを参照してください。

## <a name="connect-an-azure-purview-account"></a>Azure Purview アカウントの接続  

Azure Purview アカウントを接続するには、次の手順に従います。

1.  [https://web.azuresynapse.net](https://web.azuresynapse.net) にアクセスし、Synapse ワークスペースにサインインします。 
2. **[管理]**  ->  **[Azure Purview]** に移動し、 **[Connect to a Purview account]\(Purview アカウントに接続する\)** を選択します。
3. **[Azure サブスクリプションから]** または **[手動で入力]** を選択できます。 **Azure サブスクリプションから**、自分がアクセスできるアカウントを選択できます。
4. 接続すると、 **[Azure Purview アカウント]** タブで Purview アカウントの名前を確認できます。 

Purview アカウントがファイアウォールによって保護されている場合は、Purview 用のマネージド プライベート エンドポイントを作成します。 Azure Synapse から[セキュリティで保護された Purview アカウントにアクセス](how-to-access-secured-purview-account.md)できるようにする方法の詳細を確認してください。 初期接続時にそれを行うことも、既存の接続を後で編集することもできます。

Purview の接続情報は、次のような Synapse ワークスペース リソースに格納されます。 プログラムを使用して接続を確立するには、Synapse ワークスペースを更新し、`purviewConfiguration` 設定を追加します。

```json
{
    "name": "ContosoSynapseWorkspace",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

## <a name="set-up-authentication"></a>認証の設定

Synapse ワークスペースのマネージド ID は、Synapse ワークスペースから Purview への系列のプッシュ操作を認証するために使用されます。

Synapse ワークスペースのマネージド ID に、Purview **ルート コレクション** の **データ キュレーター** ロールを付与します。 詳細については、「[Azure Purview でのアクセス制御](../../purview/catalog-permissions.md)」および「[コレクションを使用してロールを追加してアクセスを制限する](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)」を参照してください。

Synapse Studio で Synapse ワークスペースを Purview に接続すると、Synapse は、自動的にこのようなロールの割り当ての追加を試みます。 Purview ルート コレクションに対する **コレクション管理者** ロールがあり、ネットワークから Purview アカウントにアクセスできる場合、この操作は成功します。

## <a name="monitor-purview-connection"></a>Purview 接続を監視する

Synapse ワークスペースを Purview アカウントに接続すると、有効な統合機能に関する詳細を含む次のページが表示されます。

:::image type="content" source="./media/monitor-purview-connection-status.png" alt-text="Azure Synapse と Purview の統合状態を監視するためのスクリーンショット。":::

**データ系列 - Synapse パイプライン** では、次のいずれかの状態が表示される可能性があります。

- **接続中**: Synapse ワークスペースは Purview アカウントに正常に接続されています。 これは、Synapse ワークスペースが Purview アカウントに関連付けられていて、そのアカウントに系列をプッシュする権限があることを示しています。 Purview アカウントがファイアウォールによって保護されている場合は、アクティビティの実行および系列のプッシュに使用される統合ランタイムが、Purview アカウントに到達できることを確認する必要もあります。 [セキュリティで保護された Azure Purview アカウントへのアクセス](how-to-access-secured-purview-account.md)に関するページで詳細を確認してください。
- **接続解除**: Purview データ キュレーター ロールが Synapse ワークスペースのマネージド ID に付与されないため、Synapse ワークスペースは、Purview に系列をプッシュできません。 この問題を修正するには、ご自身の Purview アカウントに移動してロールの割り当てを確認し、必要に応じて手動でロールを付与します。 詳細については、「[認証の設定](#set-up-authentication)」セクションを 参照してください。
- **不明**: Azure Synapse によって状態を確認できません。 次のような原因が考えられます。

    - アカウントがファイアウォールによって保護されているため、ご自身の現在のネットワークから Purview アカウントに到達できません。 代わりに、ご自身の Purview アカウントに接続されているプライベート ネットワークから Synapse Studio を起動できます。
    - Purview アカウント上でロールの割り当てを確認する権限がありません。 ご自身のロールの割り当てについては、Purview アカウント管理者に問い合わせることができます。 必要な Purview ロールについては、「[認証の設定](#set-up-authentication)」セクションを参照してください。

## <a name="report-lineage-to-azure-purview"></a>系列を Azure Purview に報告する

Synapse ワークスペースを Purview アカウントに接続すると、パイプラインを実行するときに、Synapse は系列情報を Purview アカウントに報告します。 サポートされる機能の詳細とエンドツーエンド チュートリアルについては、「[Azure Synapse Analytics のメタデータと系列](../../purview/how-to-lineage-azure-synapse-analytics.md)」を参照してください。

## <a name="discover-and-explore-data-using-purview"></a>Purview を使用してデータを検出して探索する

Synapse ワークスペースを Purview アカウントに接続すると、Synapse ワークスペースの上部中央にある検索バーを使用して、データを検索してアクションを実行できます。 詳細については、「[Azure Purview を使用した Synapse でのデータの検出、接続、探索](how-to-discover-connect-analyze-azure-purview.md)」を参照してください。

## <a name="nextsteps"></a>次のステップ 

[Azure Purview を使用した Synapse でのデータの検出、接続、調査](how-to-discover-connect-analyze-azure-purview.md)

[Azure Synapse Analytics のメタデータと系列](../../purview/how-to-lineage-azure-synapse-analytics.md)

[セキュリティで保護された Azure Purview アカウントにアクセスする](how-to-access-secured-purview-account.md)

[Azure Purview での Azure Synapse 資産の登録とスキャン](../../purview/register-scan-azure-synapse-analytics.md)

[Power BI から Azure Purview に系列を取得する](../../purview/how-to-lineage-powerbi.md)

[Azure Data Share と Azure Purview を接続する](../../purview/how-to-link-azure-data-share.md)
