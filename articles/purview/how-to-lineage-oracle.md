---
title: Oracle のメタデータと系列
description: この記事では Oracle ソースからのデータ系列の抽出について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: cf415250868c74aa95e22b12d8980987f0c9dc48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862834"
---
# <a name="how-to-get-lineage-from-oracle-into-azure-purview"></a>Oracle から Azure Purview に系列を取得する方法

この記事では、Azure Purview における Oracle ソースのデータ系列の側面について説明します。 Oracle の Purview のデータ系列を確認するための前提条件は、[Oracle をスキャン](../purview/register-scan-oracle-source.md)することです。 

## <a name="lineage-of-oracle-artifacts-in-azure-purview"></a>Azure Purview における Oracle アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して Oracle アーティファクトを検索し、関連する結果を確認できます。 [資産の概要 & プロパティ] タブで、説明、分類などの情報などの基本的な詳細情報が表示されます。 [系列] タブの下に、Oracle テーブルとストアド プロシージャ、ビュー、関数の間の資産リレーションシップが表示されます。 

そのため、現在、Oracle ビューにはテーブルの系列情報が含まれます。一方、関数とストアド プロシージャでは、parameter_dataset と stored_procedure_result_set によりテーブルとビューの間に系列が生成されます。 派生した系列は、列レベルでも使用できます。

:::image type="content" source="./media/how-to-lineage-oracle/lineage.png" alt-text="Oracle で系列をどのようにレンダリングするのかを示すスクリーンショット。" lightbox="./media/how-to-lineage-oracle/lineage.png":::


## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)
