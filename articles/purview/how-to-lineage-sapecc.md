---
title: SAP ECC のメタデータと系列
description: この記事では、SAP ECC ソースからのデータ系列の抽出について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: fc7c612384d46ed4983e634ec665927cf0bf1002
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463981"
---
# <a name="how-to-get-lineage-from-sap-ecc-into-azure-purview"></a>SAP ECC から Azure Purview に系列を取得する方法

この記事では、Azure Purview における SAP ECC ソースのデータ系列の側面について説明します。 SAP ECC の Purview でデータ系列を表示するための前提条件は、[SAP ECC をスキャンする](../purview/register-scan-sapecc-source.md)ことです。 

## <a name="lineage-of-sap-ecc-artifacts-in-azure-purview"></a>Azure Purview における SAP ECC アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して SAP ECC アーティファクトを検索し、関連する結果を確認できます。 [資産の概要 & プロパティ] タブに、説明、プロパティ、およびその他の情報などの基本的な詳細情報が表示されます。 [系列] タブには、SAP ECC テーブルとビューの間の資産リレーションシップが表示されます。そのため、SAP ECC ビューには、テーブルからの系列情報が含まれます。 

派生した系列は、列レベルでも使用できます。

:::image type="content" source="./media/how-to-lineage-sapecc/lineage.png" alt-text="SAP ECC の系列のレンダリング方法を示すスクリーンショット。" lightbox="./media/how-to-lineage-sapecc/lineage.png":::


## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- ADF を使用して SAP ECC からデータを Azure に移動する場合、データ移動実行時の一部として系列を追跡できます - [Azure Data Factory をリンクして、自動化された系列をプッシュする](how-to-link-azure-data-factory.md)
