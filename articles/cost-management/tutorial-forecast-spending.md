---
title: "チュートリアル - Azure Cost Management を使用した支出の予測 | Microsoft Docs"
description: "このチュートリアルでは、使用量の履歴と支出データを使用して支出を予測する方法について説明します。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 35142cb40560db848c71da266bbaa1881f12e15d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-forecast-future-spending"></a>チュートリアル: 将来の支出の予測

Azure Cost Management by Cloudyn では、使用量の履歴と支出データを利用して将来の支出予測を支援します。 Cloudyn レポートを使用して、すべての予想コスト データを確認できます。 このチュートリアルの例では、このレポートを使用して予想コストを確認する方法を説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 将来の支出の予測

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure アカウントが必要です。
- Azure Cost Management に関して、評価版の登録または有料サブスクリプションが必要となります。

## <a name="forecast-future-spending"></a>将来の支出の予測

Cloudyn には、時系列の使用量に基づいて支出の予測を支援する予想コスト レポートが用意されています。 その主な目的は、コストの傾向が組織の予想値を超えないことの確認を支援することです。 使用するレポートは、当月予想コストと年間予想コストです。 過去 30 日間の使用量で比較的一貫性が保たれている場合、両方のレポートに将来の予想支出が表示されます。

当月予想コスト レポートには、サービスのコストが表示されます。 予想コストを表示するために使用されるのは、当月の初めからのコストと前月のコストです。 ポータルの上部にあるレポート メニューで、**[コスト]** > **[Projection and Budget]\(予想と予算\)** > **[Current Month Projected Cost]\(当月予想コスト\)** の順にクリックします。 次に例を示します。

![当月予想コスト](./media/tutorial-forecast-spending/project-month01.png)

この例では、どのサービスに最もコストが費やされたかを確認できます。 Azure のコストは AWS のコストよりも少額でした。 Azure VM のコスト予想の詳細を表示するには、**[フィルタ]** 一覧で、**[Azure/VM]** を選択します。

![Azure VM 当月予想コスト](./media/tutorial-forecast-spending/project-month02.png)

関心のある他のサービスの月間コスト予想を表示するには、上記の基本的な手順に従います。

年間予想コスト レポートには、今後 12 か月間のサービスの推定コストが表示されます。

ポータルの上部にあるレポート メニューで、**[コスト]** > **[Projection and Budget]\(予想と予算\)** > **[Annual Projected Cost]\(年間予想コスト\)** の順にクリックします。 次に例を示します。

![年間予想コスト レポート](./media/tutorial-forecast-spending/project-annual01.png)

この例では、どのサービスに最もコストが費やされたかを確認できます。 月間の例と同様に、Azure のコストは AWS のコストよりも少額でした。 Azure VM のコスト予想の詳細を表示するには、**[フィルタ]** 一覧で、**[Azure/VM]** を選択します。

![VM の年間予想コスト](./media/tutorial-forecast-spending/project-annual02.png)

上の図では、Azure VM の年間予想コストは 28,374 ドルです。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 将来の支出の予測


次のチュートリアルに進み、コスト配分およびショーバック レポートを使用してコストを管理する方法を確認してください。

> [!div class="nextstepaction"]
> [コストの配分とショーバックのレポートを使用してコストを管理する](tutorial-manage-costs.md)
