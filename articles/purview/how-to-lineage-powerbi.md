---
title: Power BI のメタデータと系列
description: この記事では Power BI ソースからのデータ系列の抽出について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b7143f7ccb0cb4c91cbb86cadb3cfa78c5c9bec3
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107912555"
---
# <a name="how-to-get-lineage-from-power-bi-into-azure-purview"></a>Power BI から Azure Purview に系列を取得する方法

この記事では、Azure Purview における Power BI ソースのデータ系列の側面について説明します。 Power BI の Purview のデータ系列を確認するための前提条件は、 [Power BI をスキャン](../purview/register-scan-power-bi-tenant.md)することです。 

## <a name="common-scenarios"></a>一般的なシナリオ

1. Power BI ソースがスキャンされると、データ コンシューマーは Purview からレポートまたはダッシュボードの根本原因分析を実行できます。 レポートでデータの不一致が発生した場合、ユーザーは上流のデータセットを簡単に特定し、必要に応じて所有者に問い合わせることができます。

2. データ プロデューサーは、データセットを消費しているダウンストリーム レポートまたはダッシュボードを表示できます。 データセットに変更を加える前に、データ所有者が情報に基づいた決定を行うことができます。

2. ユーザーは、名前、保証の状態、秘密度ラベル、所有者、説明、およびその他のビジネス ファセットを検索して、関連する Power BI 成果物を返すことができます。

## <a name="power-bi-artifacts-in-azure-purview"></a>Azure Purview における Power BI アーティファクト

[Power BI のスキャン](../purview/register-scan-power-bi-tenant.md)が完了すると、次の Power BI アーティファクトが Purview にインベントリされます

* 容量
* Workspaces
* データフロー
* データセット 
* レポート
* ダッシュボード

ワークスペースのアーティファクトには、[データフロー] - [データセット] - [レポート] - [ダッシュボード] の順に系列が表示されます

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-overview.png" alt-text="Power BI アセットの [概要] タブを表示する方法を示すスクリーンショット。" lightbox="./media/how-to-lineage-powerbi/powerbi-overview.png":::

>[!Note]
> * PowerBI データセット内の列の系列と変換は現在サポートされていません
> * 現在、PowerBI データフローまたは PowerBI データセットの作成元となるデータソースについて、限られた情報が表示されています。 例: PowerBI データセットの SQL サーバー ソースの場合は、サーバー名のみがキャプチャされます。 

## <a name="lineage-of-power-bi-artifacts-in-azure-purview"></a>Azure Purview における Power BI アーティファクトの系列

ユーザーは、名前、説明、またはその他の詳細を使用して Power BI アーティファクトを検索し、関連する結果を確認できます。 [資産の概要 & プロパティ] タブで、説明、分類などの情報などの基本的な詳細情報が表示されます。 [系列] タブには、上流および下流の依存関係と共に資産のリレーションシップが表示されます。

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-lineage.png" alt-text="Power BI で系列をどのようにレンダリングするのかを示すスクリーンショット。" lightbox="./media/how-to-lineage-powerbi/powerbi-lineage.png":::

## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)
