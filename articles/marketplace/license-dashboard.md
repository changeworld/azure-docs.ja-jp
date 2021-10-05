---
title: パートナー センターの Microsoft コマーシャル マーケットプレース分析にあるライセンス ダッシュボード - Azure Marketplace
description: コマーシャル マーケットプレース分析の [ライセンス] ダッシュボードを使用して、ライセンスに関する情報にアクセスする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 40323124846bd825b2d0c51d16f7dfa7dc67511f
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082705"
---
# <a name="license-dashboard-in-commercial-marketplace-analytics"></a>コマーシャル マーケットプレース分析の [ライセンス] ダッシュボード

この記事では、パートナー センターのコマーシャル マーケットプレース プログラムの [ライセンス] ダッシュボードについて説明します。 [ライセンス] ダッシュボードには、次の情報が表示されます。

- ライセンスを購入した顧客の数
- 購入済みライセンスの合計数
- デプロイ済みライセンスの合計数
- 顧客が購入してデプロイしたライセンスの数
- 国およびリージョン間でのライセンスの配分

## <a name="check-license-usage"></a>ライセンスの使用状況の確認する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

パートナー センターで ISV アプリのライセンスの使用状況を確認するには、次の手順を実行します。

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センター ホーム ページの [分析情報] タイルを示しています。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左側のメニューで **[ライセンス]** を選択します。

    [ ![パートナー センターの [ライセンス] ダッシュボードのスクリーンショット。](./media/license-dashboard/license-dashboard-workspaces.png) ](./media/license-dashboard/license-dashboard-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

パートナー センターで ISV アプリのライセンスの使用状況を確認するには、次の手順を実行します。

1. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2165507)にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[分析]**  >  **[ライセンス]** の順に選択します。

:::image type="content" source="./media/license-dashboard/license-dashboard.png" alt-text="パートナー センターの [ライセンス] ダッシュボードのスクリーンショット。":::

---

## <a name="elements-of-the-license-dashboard"></a>[ライセンス] ダッシュボードの要素

次のセクションでは、[ライセンス] ダッシュボードの使用方法とデータの読み取り方法について説明します。

## <a name="month-range"></a>月範囲

ページの右上隅に月範囲の選択があります。 過去 6 か月または 12 か月を基準として月範囲を選択するか、カスタムの月範囲 (最大 12 か月間) を選択して、ページのウィジェットの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view) 

[ ![パートナー センターの [ライセンス] ダッシュボードの月範囲のスクリーンショット。](./media/license-dashboard/license-workspace-filters.png) ](./media/license-dashboard/license-workspace-filters.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view) 

:::image type="content" source="./media/license-dashboard/month-range.png" alt-text="パートナー センターの [ライセンス] ダッシュボードの月範囲のスクリーンショット。":::

---

## <a name="customers-widget"></a>[顧客] ウィジェット

_[顧客] ウィジェット_ には、現在の顧客数が表示されます。 傾向グラフには、月ごとの顧客数が表示されます。

:::image type="content" source="./media/license-dashboard/customers-widget.png" alt-text="パートナー センターの [ライセンス] ダッシュボードの [顧客] ウィジェットのスクリーンショット。":::

## <a name="license-widget"></a>[ライセンス] ウィジェット

_[ライセンス] ウィジェット_ には、プロビジョニング済みおよび割り当て済みのライセンスの現在の数が表示されます。 傾向グラフには、プロビジョニング済みおよび割り当て済みのライセンスの月ごとの数が表示されます。 過去 6 か月または 12 か月分のデータを表示できます。 ページの右上にある **[カスタム]** リンクを選択すると、カスタムの日付範囲を選択することもできます。

:::image type="content" source="./media/license-dashboard/license-widget.png" alt-text="パートナー センターの [ライセンス] ダッシュボードの [ライセンス] ウィジェットのスクリーンショット。":::

## <a name="analysis-widget"></a>[分析] ウィジェット

_[分析] ウィジェット_ には、オファーとプランごとのプロビジョニング済みおよび割り当て済みのライセンスの数と割合が表示されます。 傾向グラフには、プロビジョニング済みおよび割り当て済みのライセンスの月ごとの数が表示されます。 このウィジェットを使用すると、顧客や製品でデータをフィルター処理できます。

:::image type="content" source="./media/license-dashboard/analysis-widget.png" alt-text="パートナー センターの [ライセンス] ダッシュボードの [分析] ウィジェットのスクリーンショット。":::

## <a name="license-distribution-widget"></a>[ライセンス配布] ウィジェット

_[ライセンス配布]_ ウィジェットには、さまざまな国およびリージョン間のライセンスの配分が表示されます。 色分けされたリージョンは、ユーザー ライセンスの配布先を示します。 既定のビューに戻すには、ウィジェットの **[ズームのリセット]** ボタン ([ホーム] アイコン) を選択します。

:::image type="content" source="./media/license-dashboard/license-distribution.png" alt-text="パートナー センターの [ライセンス] ダッシュボードの [ライセンス配布] ウィジェットのスクリーンショット。":::

## <a name="data-terms-in-license-report-downloads"></a>ライセンス レポートのダウンロードのデータ用語

ウィジェットの右上隅にあるダウンロード アイコンを使用して、データをダウンロードできます。

| 属性名 | 定義 |
| ------------ | ------------- |
| 顧客の国 | 顧客の請求先の国 |
| 顧客の国番号 | 顧客の請求先の国番号 |
| Customer Name | 顧客名 |
| アクティブ化した日 | ライセンスがアクティブ化された日付 |
| 製品の表示名 | AppSource に表示されるオファーのタイトル |
| Product ID | Product ID |
| プロビジョニングされたライセンス | 顧客のアカウントにアクティブ化されたライセンスの数 |
| 割り当てられたライセンス | 顧客がユーザーに割り当てたライセンスの数 |
| SKU 名 | オファー内のプランの名前 |
| テナント ID | テナントの 一意の ID |
| ライセンスの状態 | ライセンスの状態 |
| サービス ID | プランをライセンス チェックにマップするためにパッケージで使用される一意の識別子 |
|||

## <a name="next-steps"></a>次のステップ

- コマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析レポートにアクセスする](analytics.md)」を参照してください。
