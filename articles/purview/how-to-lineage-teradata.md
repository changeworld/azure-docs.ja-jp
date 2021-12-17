---
title: Teradata のメタデータと系列
description: この記事では、Teradata ソースからのデータ系列の抽出について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 047405fea177cb8ba1365a1329dd6a682f1662fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428769"
---
# <a name="how-to-get-lineage-from-teradata-into-azure-purview"></a>Teradata から Azure Purview に系列を取得する方法

この記事では、Azure Purview における Teradata ソースのデータ系列の側面について説明します。 Purview で Teradata のデータ系列を確認するには、[Teradata をスキャン](../purview/register-scan-teradata-source.md)することが前提条件になります。 

## <a name="lineage-of-teradata-artifacts-in-azure-purview"></a>Azure Purview における Teradata アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して Teradata アーティファクトを検索し、関連する結果を確認できます。 [資産の概要 & プロパティ] タブには、説明、プロパティ、およびその他の情報などの基本的な詳細情報が表示されます。 [系列] タブの下に、Teradata テーブルとストアド プロシージャ間、および Teradata テーブルとビュー間の資産リレーションシップが表示されます。 

したがって、ストアド プロシージャで発生するデータ変換と、Teradata テーブルからのビューの変換に対する系列がサポートされます。 派生した系列は、列レベルでも使用できます。

:::image type="content" source="./media/how-to-lineage-teradata/lineage.png" alt-text="Teradata で系列がどのようにレンダリングされるのかを示すスクリーンショット。" lightbox="./media/how-to-lineage-teradata/lineage.png":::

上のスクリーンショットで、**customerView** は Teradata テーブル **test_customer** から作成された Teradata ビューです。 また、**Return_DataSet** は、Teradata テーブル **test_customer** を使用するストアド プロシージャです。

## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- ADF を使用して Teradata から Azure にデータを移動する場合、データ移動実行時の一部として系列を追跡できます - [Azure Data Factory をリンクして、自動化された系列をプッシュする](how-to-link-azure-data-factory.md)
