---
title: Azure Synapse Analytics のメタデータと系列
description: この記事では、データ系列を追跡するために Azure Synapse Analytics と Azure Purview を接続する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/10/2021
ms.openlocfilehash: a6ff17795cfb65ddc5dc8b3bf8c55d3d8084efc8
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228880"
---
# <a name="how-to-get-lineage-from-azure-synapse-analytics-into-azure-purview"></a>Azure Synapse Analytics から Azure Purview に系列を取得する方法

このドキュメントでは、データ系列を追跡するために Azure Synapse ワークスペースと Azure Purview アカウントを接続するために必要な手順について説明します。 また、対象範囲スコープとサポートされる系列機能の詳細についても説明します。

## <a name="supported-azure-synapse-capabilities"></a>サポートされている Azure Synapse 機能

現在、Azure Purview は、次の Azure Synapse パイプライン アクティビティからランタイムの系列をキャプチャします。

- [[データのコピー]](../data-factory/copy-activity-overview.md)

> [!IMPORTANT]
> ソースまたは宛先でサポートされていないデータ ストレージ システムが使用されている場合、Azure Purview は系列を削除します。

[!INCLUDE[azure-synapse-supported-activity-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

## <a name="bring-azure-synapse-lineage-into-purview"></a>Azure Synapse 系列を Purview に取り込む

### <a name="step-1-connect-azure-synapse-workspace-to-your-purview-account"></a>手順 1: Azure Synapse ワークスペースを自分の Purview アカウントに接続する

Azure Sysnpase ワークスペースを Purview に接続できます。また、この接続により、Azure Synapse が系列情報を Purview にプッシュできるようになります。 「[Azure Purview アカウントを Synapse に接続する](../synapse-analytics/catalog-and-governance/quickstart-connect-azure-purview.md)」の手順に従ってください。 包括的に系列を追跡するために、複数の Azure Synapse ワークスペースを 1 つの Azure Purview アカウントに接続できます。

### <a name="step-2-run-pipeline-in-azure-synapse-workspace"></a>手順 2: Azure Synapse ワークスペースでパイプラインを実行する

Azure Synapse ワークスペースで Copy アクティビティを使用して、パイプラインを作成できます。 系列データ キャプチャのために追加の構成は必要ありません。 系列データは、アクティビティの実行中に自動的にキャプチャされます。

### <a name="step-3-monitor-lineage-reporting-status"></a>手順 3: 系列のレポートの状態を監視する

Azure Synapse パイプラインを実行した後、Synapse パイプライン監視ビューで次の **[Lineage status]\(系列の状態\)** ボタンをクリックして、系列のレポートの状態を確認できます。 同じ情報を、アクティビティ出力 JSON -> `reportLineageToPurvew` セクションでも取得できます。

:::image type="content" source="../data-factory/media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="パイプライン監視ビューで、系列のレポートの状態を監視する。":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>手順 4:Purview アカウントで系列情報を表示する

Purview アカウントでアセットを参照し、"Azure Synapse Analytics" という種類を選択します。 キーワードを使用して Data Catalog を検索することもできます。

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-assets.png" alt-text="Purview で Azure Synapse アセットを参照する。"

Synapse アカウント -> パイプライン -> アクティビティの順に選択すると、系列情報を表示できます。

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png" alt-text="Purview で Azure Synapse パイプラインの系列を参照します。" lightbox="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png":::

## <a name="next-steps"></a>次のステップ

[カタログ系列ユーザー ガイド](catalog-lineage-user-guide.md)

[系列のための Azure Data Share へのリンク](how-to-link-azure-data-share.md)