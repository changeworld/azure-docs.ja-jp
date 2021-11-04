---
title: SAP S/4HANA のメタデータと系列
description: この記事では、SAP S/4HANA ソースからのデータ系列の抽出について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 122b7a0b826c43f43b5e43d4ba2cb5e42fd52a16
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422484"
---
# <a name="how-to-get-lineage-from-sap-s4hana-into-azure-purview"></a>SAP S/4HANA から Azure Purview に系列を取得する方法

この記事では、Azure Purview における SAP S/4HANA ソースのデータ系列の側面について説明します。 SAP S/4HANA の Purview でデータ系列を表示するための前提条件は、[SAP S/4HANA をスキャンする](../purview/register-scan-saps4hana-source.md)ことです。 

## <a name="lineage-of-sap-s4hana-artifacts-in-azure-purview"></a>Azure Purview における SAP S/4HANA アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して SAP S/4HANA アーティファクトを検索し、関連する結果を確認できます。 [資産の概要 & プロパティ] タブには、説明、プロパティ、およびその他の情報などの基本的な詳細情報が表示されます。 [系列] タブの下に、SAP S/4HANA のテーブルとビュー間のアセット リレーションシップが表示されます。 そのため、SAP S/4HANA ビューには、テーブルからの系列情報が含まれます。 

派生した系列は、列レベルでも使用できます。

## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- ADF を使用して SAP S/4HANA からデータを Azure に移動する場合、データ移動実行時の一部として系列を追跡できます - [Azure Data Factory をリンクして、自動化された系列をプッシュする](how-to-link-azure-data-factory.md)
