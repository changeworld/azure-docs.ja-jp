---
title: Synapse ワークスペースを Azure Purview に接続する 
description: Synapse ワークスペースを Azure Purview アカウントに接続します。
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 09/02/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: b7d729234244302e648a2d3a0bf9c8dc94f10d5a
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450361"
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

- **2021 年 8 月 18 日以降** に作成された Purview アカウントでは、Synapse ワークスペースのマネージド ID に、Purview **ルート コレクション** の **データ キュレーター** ロールが付与されます。 詳細については、「[Azure Purview でのアクセス制御](../../purview/catalog-permissions.md)」および「[コレクションを使用してロールを追加してアクセスを制限する](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)」を参照してください。

    Synapse Studio で Synapse ワークスペースを Purview に接続すると、Synapse は、自動的にこのようなロールの割り当ての追加を試みます。 Purview ルート コレクションに対する **コレクション管理者** ロールがあり、ネットワークから Purview アカウントにアクセスできる場合、この操作は成功します。

- **2021 年 8 月 18 日より前** に作成された Purview アカウントでは、Synapse ワークスペースのマネージド ID に、Purview アカウントの Azure 組み込みの [**Purview データ キュレーター**](../../role-based-access-control/built-in-roles.md#purview-data-curator) ロールが付与されます。 詳細については、[Azure Purview でのアクセス制御 - 従来のアクセス許可](../../purview/catalog-permissions.md#legacy-permission-guide)に関するページを参照してください。

    Synapse Studio で Synapse ワークスペースを Purview に接続すると、Synapse は、自動的にこのようなロールの割り当ての追加を試みます。 Purview アカウントに Azure 組み込みの **所有者** ロールまたは **ユーザー アクセス管理者** ロールがある場合、この操作は成功します。

Purview ロールの割り当て情報を読み取る権限があり、必要なロールが付与されていない場合は、次の警告が表示される可能性があります。 パイプラインの系列のプッシュに対して接続が適切に設定されていることを確認するには、Purview アカウントに移動し、**Purview データ キュレーター** ロールが Synapse ワークスペースのマネージド ID に付与されているかを確認します。 ない場合は、ロールの割り当てを手動で追加します。

:::image type="content" source="./media/register-purview-account-warning.png" alt-text="Purview アカウント登録の警告のスクリーンショット。":::

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