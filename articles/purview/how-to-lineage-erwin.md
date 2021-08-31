---
title: Erwin のメタデータと系列
description: この記事では Erwin ソースからのデータ系列の抽出について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: d1821ca94d8f5329ca4c08f65438a3d0ba6e4d48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862773"
---
# <a name="how-to-get-lineage-from-erwin-into-azure-purview"></a>Erwin から Azure Purview に系列を取得する方法

この記事では、Azure Purview における Erwin ソースのデータ系列の側面について説明します。 Erwin の Purview のデータ系列を確認するための前提条件は、[Erwin をスキャン](../purview/register-scan-erwin-source.md)することです。 

## <a name="lineage-of-erwin-artifacts-in-azure-purview"></a>Azure Purview における Erwin アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して Erwin アーティファクトを検索し、関連する結果を確認できます。 [asset overview & properties]\(資産の概要 & プロパティ\) タブに、説明、プロパティ、およびその他の情報などの基本的な詳細情報が表示されます。 [系列] タブには、Erwin テーブルとビューの間の資産リレーションシップが表示されます。そのため、Erwin ビューには、テーブルからの系列情報が含まれます。 

:::image type="content" source="./media/how-to-lineage-erwin/lineage.png" alt-text="Erwin で系列をどのようにレンダリングするのかを示すスクリーンショット。" lightbox="./media/how-to-lineage-erwin/lineage.png":::


## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)
