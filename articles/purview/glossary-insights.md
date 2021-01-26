---
title: Purview Insights を使用した、データに関する用語集レポート
description: このハウツー ガイドでは、データに関する Purview Insights の用語集レポートを表示して使用する方法について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576776"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Azure Purview でのデータに関する用語集の分析情報

このハウツー ガイドでは、データに関する Purview の用語集の分析情報レポートへのアクセス、表示、フィルター処理を行う方法について説明します。

このハウツー ガイドでは、次の方法について説明します。

> [!div class="checklist"]
> - Purview アカウントから分析情報にアクセスする
> - データの鳥瞰図を取得する

## <a name="prerequisites"></a>前提条件

Purview Insights の使用を開始する前に、次の手順を完了していることをご確認ください。

- Azure リソースを設定し、アカウントにデータを設定する

- ソースの種類に関するスキャンを設定して完了する

- 用語集を設定し、用語集の用語に資産を添付する

詳細については、「[Azure Purview でデータ ソースを管理する (プレビュー)](manage-data-sources.md)」を参照してください。

## <a name="use-purview-glossary-insights"></a>Purview の用語集の分析情報を使用する

Azure Purview では、用語集の用語を作成して資産に添付することができます。 後で、用語集の分析情報で用語集の分布を確認できます。 これには、資産に添付された用語ごとに用語集の状態が表示されます。 また、状態別の用語や、ユーザー数別のロールの分布も表示されます。

**用語集の分析情報を表示するには:**

1. Azure portal で **Azure Purview** [インスタンス画面](https://aka.ms/purviewportal)に移動し、Purview アカウントを選択します。

1. **[概要]** ページの **[開始]** セクションで、 **[Purview アカウントの起動]** タイルを選択します。

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Azure portal から Purview を起動する":::

1. Purview の **[ホーム]** ページで、 **[分析情報の表示]** タイルを選択して **[分析情報]** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 領域にアクセスします。

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Azure portal で分析情報を表示する":::

1. **[分析情報]** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 領域で、 **[用語集]** を選択して Purview の **[用語集の分析情報]** レポートを表示します。

**[用語集の分析情報]** ページには、次の領域が表示されます。
1. 用語集の用語とカタログ ユーザーを示す **[High level KPIs]\(高レベルの KPI\)**

2. **[Top glossary terms and count of assets]\(上位の用語集の用語と資産の数\)** 。上位 5 つの用語集の用語と、それらに添付された資産が表示されます。 その他の資産はすべて、グラフ内の "その他" カテゴリに分類されます。

3. **[Glossary terms by term status]\(用語の状態別の用語集の用語\)** 。"ドラフト"、"承認済み"、"アラート"、"期限切れ" などの状態別の用語集の用語の分布が表示されます。 

1. 状態を示すグラフのスライスをポイントするかクリックして、その状態にある用語の数に注目します。

1. **[Distribution of roles by number of users]\(ユーザー数別のロールの分布\)** 。Purview でのロールごとのユーザー数別のロールの分布が示されます。

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="用語集の分析情報を表示する":::

## <a name="next-steps"></a>次のステップ

[資産の分析情報](./asset-insights.md)を通して Azure Purview の分析情報レポートの詳細について学習する