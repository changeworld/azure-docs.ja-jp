---
title: Data Factory 系列データを Azure Purview にプッシュする
description: Data Factory 系列データを Azure Purview にプッシュする方法について説明します
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 08/10/2021
ms.openlocfilehash: ff6c53efea27bc2bb4e6273180aa64178d6a0295
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424260"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Data Factory 系列データを Azure Purview にプッシュする (プレビュー)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Data Factory ユーザー インターフェイス (UI) を使用して、アクティビティを実行して系列データを Azure Purview アカウントにレポートするパイプラインを作成します。 その後、Azure Purview アカウントのすべての系列情報を表示できます。 

現在、系列がサポートされるのは、コピー、データ フロー、および SSIS 実行のアクティビティです。 サポートされている機能の詳細については、「[サポートされる Azure Data Factory アクティビティ](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure Data Factory**。 Azure Data Factory がない場合は、[Azure Data Factory の作成](./quickstart-create-data-factory-portal.md)に関するページを参照してください。
* **Azure Purview アカウント**。 Purview アカウントにより、データ ファクトリによって生成されるすべての系列データがキャプチャされます。 Azure Purview アカウントがない場合は、[Azure Purview の作成](../purview/create-catalog-portal.md)に関するページを参照してください。

## <a name="run-pipeline-and-push-lineage-data-to-azure-purview"></a>パイプラインを実行して系列データを Azure Purview にプッシュする

### <a name="step-1-connect-data-factory-to-your-purview-account"></a>手順 1: Data Factory を Purview アカウントに接続する

「[Data Factory を Azure Purview に接続する](connect-data-factory-to-azure-purview.md)」の手順に従って、Data Factory と Purview アカウントの接続を確立できます。

### <a name="step-2-run-pipeline-in-data-factory"></a>手順 2 Data Factory でパイプラインを実行する

Data Factory でパイプライン、コピー アクティビティ、およびデータフロー アクティビティを作成できます。 系列データ キャプチャのために追加の構成は必要ありません。 系列データは、アクティビティの実行中に自動的にキャプチャされます。

:::image type="content" source="./media/data-factory-purview/adf-activities-for-lineage.png" alt-text="コピー アクティビティとデータフロー アクティビティのスクリーンショット。":::

:::image type="content" source="./media/data-factory-purview/ssis-activities-for-lineage.png" alt-text="SSIS パッケージの実行アクティビティのスクリーンショット。":::

コピー、データ フロー、SSIS の実行の各アクティビティを作成する方法については、「[Azure Data Factory を使用して Azure Blob Storage から Azure SQL Database のデータベースにデータをコピーする](./tutorial-copy-data-portal.md)」、「[マッピング データ フローを使用してデータを変換する](./tutorial-data-flow.md)」、および [Azure での SSIS パッケージの実行](./tutorial-deploy-ssis-packages-azure.md)に関するページを参照してください。

### <a name="step-3-monitor-lineage-reporting-status"></a>手順 3: 系列のレポートの状態を監視する

パイプラインを実行した後、[パイプライン監視ビュー](monitor-visually.md#monitor-pipeline-runs)で次の **[Lineage status]\(系列の状態\)** ボタンをクリックして、系列のレポートの状態を確認できます。 同じ情報を、アクティビティ出力 JSON -> `reportLineageToPurvew` セクションでも取得できます。

:::image type="content" source="./media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="パイプライン監視ビューで、系列のレポートの状態を監視する。":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>手順 4:Purview アカウントで系列情報を表示する

Purview UI では、資産を参照し、種類として "Azure Data Factory" を選択できます。 キーワードを使用して Data Catalog を検索することもできます。

:::image type="content" source="./media/data-factory-purview/view-dataset.png" alt-text="Purview で資産を参照しているスクリーンショット。" lightbox="./media/data-factory-purview/view-dataset.png":::

アクティビティの資産の [系列] タブをクリックすると、すべての系列情報が表示されます。

- コピー アクティビティによって、以下が行われます。

    :::image type="content" source="./media/data-factory-purview/copy-lineage.png" alt-text="Purview のコピー アクティビティのスクリーンショット。" lightbox="./media/data-factory-purview/copy-lineage.png":::

- データ フロー アクティビティ:

    :::image type="content" source="./media/data-factory-purview/dataflow-lineage.png" alt-text="Purview のデータ フロー アクティビティのスクリーンショット。" lightbox="./media/data-factory-purview/dataflow-lineage.png":::

    > [!NOTE] 
    > データフロー アクティビティの系列では、ソースとシンクのみがサポートされています。 データフロー変換の系列はまだサポートされていません。

- SSIS パッケージの実行アクティビティ:

    :::image type="content" source="./media/data-factory-purview/ssis-lineage.png" alt-text="Purview での SSIS 実行の系列のスクリーンショット。" lightbox="./media/data-factory-purview/ssis-lineage.png":::

    > [!NOTE] 
    > SSIS パッケージの実行アクティビティの系列では、ソースと宛先のみがサポートされています。 変換の系列はまだサポートされていません。

## <a name="next-steps"></a>次のステップ

[カタログ系列ユーザー ガイド](../purview/catalog-lineage-user-guide.md)

[Data Factory を Azure Purview に接続する](connect-data-factory-to-azure-purview.md)