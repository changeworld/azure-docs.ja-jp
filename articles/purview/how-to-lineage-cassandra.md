---
title: Cassandra のメタデータと系列
description: この記事では Cassandra ソースからのデータ系列の抽出について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: a46649187403c5825f580da167f9fe77dc9ba210
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453328"
---
# <a name="how-to-get-lineage-from-cassandra-into-azure-purview"></a>Cassandra から Azure Purview に系列を取得する方法

この記事では、Azure Purview における Cassandra ソースのデータ系列の側面について説明します。 Cassandra の Purview のデータ系列を確認するための前提条件は、[Cassandra サーバーをスキャン](../purview/register-scan-cassandra-source.md)することです。 

## <a name="lineage-of-cassandra-artifacts-in-azure-purview"></a>Azure Purview における Cassandra アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して Cassandra アーティファクトを検索し、関連する結果を確認できます。 [asset overview & properties]\(資産の概要 & プロパティ\) タブに、説明、プロパティ、およびその他の情報などの基本的な詳細情報が表示されます。 [系列] タブの下に、Cassandra テーブルと具体化されたビューの間の資産リレーションシップが表示されます。そのため、現在、Cassandra の具体化されたビューには、テーブルからの系列情報が含まれます。 

派生した系列は、列レベルでも使用できます。

:::image type="content" source="./media/how-to-lineage-cassandra/lineage.png" alt-text="Cassandra で系列をどのようにレンダリングするのかを示すスクリーンショット。" lightbox="./media/how-to-lineage-cassandra/lineage.png":::


## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)
