---
title: Looker のメタデータと系列
description: この記事では Looker ソースからのデータ系列の抽出について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: bed8395940643e56d586369fd00341b4c28b27f9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862841"
---
# <a name="how-to-get-lineage-from-looker-into-azure-purview"></a>Looker から Azure Purview に系列を取得する方法

この記事では、Azure Purview における Looker ソースのデータ系列の側面について説明します。 Looker の Purview のデータ系列を確認するための前提条件は、[Looker をスキャン](../purview/register-scan-looker-source.md)することです。 

## <a name="lineage-of-looker-artifacts-in-azure-purview"></a>Azure Purview における Looker アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して Looker アーティファクトを検索し、関連する結果を確認できます。 [asset overview & properties]\(資産の概要 & プロパティ\) タブに、説明、プロパティ、およびその他の情報などの基本的な詳細情報が表示されます。 [系列] タブには、Looker レイアウトとビューの間の資産リレーションシップが表示されます。そのため、現在、Looker ビューには、テーブルからの系列情報が含まれます。 

:::image type="content" source="./media/how-to-lineage-looker/lineage.png" alt-text="Looker で系列をどのようにレンダリングするのかを示すスクリーンショット。" lightbox="./media/how-to-lineage-looker/lineage.png":::


## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)
