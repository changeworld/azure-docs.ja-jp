---
title: マッピング データ フローを作成する
description: Azure Data Factory のマッピング データ フローを作成する方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 1c47e42f3186d573fb57f1ebaa89140e0c713c0e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614618"
---
# <a name="create-azure-data-factory-data-flows"></a>Azure Data Factory のデータ フローを作成する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

マッピング データ フローにより、コーディングを必要とすることなく、大規模なデータ変換を行う機能が提供されます。 データ フロー デザイナーで一連の変換を構築することで、データ変換ジョブを設計できます。 任意の数のソースの変換から始め、その後にデータ変換手順を実行します。 次に、データ フローをシンクで完了して、結果を宛先に書き込みます。

## <a name="steps-to-create-a-new-data-flow"></a>新しいデータ フローを作成する手順

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

最初に、Azure portal で[新しい V2 データ ファクトリを作成](quickstart-create-data-factory-portal.md)します。 新しいファクトリを作成した後、ポータルで **[Open Azure Data Factory Studio]** タイルを選択して、Data Factory Studio を起動します。

:::image type="content" source="media/data-flow-create/open-data-factory-studio-from-portal.png" alt-text="Azure portal から Data Factory Studio を開く方法のスクリーンショットを示します。":::

テンプレート ギャラリーからサンプル データ フローを追加できます。 ギャラリーを参照するには、Data Factory Studio で **[作成者]** タブを選択し、プラス記号をクリックして、 **[パイプライン]**  |  **[テンプレート ギャラリー]** を選択します。

:::image type="content" source="media/data-flow-create/open-template-gallery-from-data-factory.png" alt-text="Data Factory でテンプレート ギャラリーを開く方法を示します。":::

そこでデータ フロー カテゴリを選択して使用可能なテンプレートから選択します。

:::image type="content" source="media/data-flow-create/template-gallery-filtered-for-data-flow.png" alt-text="データ フローでフィルター処理されたテンプレート ギャラリーを示します。":::

テンプレートを使用せずにデータ フローをデータ ファクトリに直接追加することもできます。 Data Factory Studio で **[作成者]** タブを選択し、プラス記号をクリックして、 **[データ フロー]**  |  **[データ フロー]** を選択します。  

:::image type="content" source="media/data-flow-create/create-data-flow-directly.png" alt-text="空のデータ フローを直接作成する方法のスクリーンショットを示します。":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

最初に、Azure portal で[新しい Synapse ワークスペースを作成](../synapse-analytics/quickstart-create-workspace.md)します。 新しいワークスペースを作成した後、 **[Open Azure Studio]\(Azure Studio を開く\)** タイルを選択して、Data Factory UI を開始します。
    
:::image type="content" source="media/data-flow-create/open-synapse-studio-from-portal.png" alt-text="Azure portal から Synapse Studio を開く方法のスクリーンショットを示します。":::

テンプレート ギャラリーからサンプル データ フローを追加できます。  ギャラリーを参照するには、Synapse Studio で **[統合]** タブを選択し、プラス記号をクリックして、 **[ギャラリーを参照]** を選択します。

:::image type="content" source="media/data-flow-create/open-template-gallery-from-synapse.png" alt-text="Data Factory でテンプレート ギャラリーを開く方法を示します。":::

データ フローのカテゴリでフィルター処理し、使用可能なテンプレートから選択します。

:::image type="content" source="media/data-flow-create/synapse-template-gallery-filtered-for-data-flow.png" alt-text="データ フローでフィルター処理されたテンプレート ギャラリーを示します。":::

テンプレートを使用せずにデータ フローをワークスペースに直接追加することもできます。 Synapse Studio で **[統合]** タブを選択し、プラス記号をクリックして、 **[パイプライン]** を選択します。  次に、パイプラインで、 **[移動と変換]** アクティビティ セクションを展開し、**データ フロー** をパイプラインのキャンバスにドラッグします。

:::image type="content" source="media/data-flow-create/create-pipeline-in-synapse.png" alt-text="空のパイプラインを直接作成する方法のスクリーンショットを示します。":::

:::image type="content" source="media/data-flow-create/add-data-flow-to-pipeline-synapse.png" alt-text="空のデータ フローをパイプラインに直接追加する方法のスクリーンショットを示します。":::

---

## <a name="next-steps"></a>次の手順

* [チュートリアル: マッピング データ フローを使用してデータを変換する](tutorial-data-flow.md)
* [ソース変換](data-flow-source.md)を使用してデータ変換の構築を開始します。
