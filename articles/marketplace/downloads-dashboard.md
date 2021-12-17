---
title: パートナー センターの Microsoft コマーシャル マーケットプレース分析にあるダウンロード ダッシュボード - Azure Marketplace
description: ご自身のマーケットプレース オファーのダウンロード要求にアクセスする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: shganesh-dev
ms.author: shganesh
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 34d8746939b1ec4a957d72b089541eb6b5c0b5dd
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080230"
---
# <a name="downloads-dashboard-in-commercial-marketplace-analytics"></a>コマーシャル マーケットプレース分析のダウンロード ダッシュボード

この記事では、パートナー センターのダウンロード ダッシュボードについて説明します。 このダッシュボードには、過去 30 日間のダウンロード要求の一覧が表示されます。

>[!NOTE]
> 分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析に関するよく寄せられる質問と用語](analytics-faq.yml)」を参照してください。

## <a name="downloads-dashboard"></a>[ダウンロード] ダッシュボード

[[ダウンロード] ダッシュボード](https://go.microsoft.com/fwlink/?linkid=2165766)には、1000 行を超える顧客または注文データが含まれるダウンロードの要求が表示されます。

1000 行を超えるデータのダウンロードを要求するたびに、 **[ダウンロード]** ダッシュボードへのリンクを含むポップアップ通知が届きます。 これらのデータのダウンロードは 30 日間可能であり、その後削除されます。

## <a name="access-the-downloads-dashboard"></a>[ダウンロード] ダッシュボードにアクセスする

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センター ホーム ページの [分析情報] タイルの画像。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左側のメニューで **[ダウンロード]** を選択します。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[分析]**  >  **[ダウンロード]** の順に選択します。

---

## <a name="lifetime-export-of-commercial-marketplace-analytics-reports"></a>コマーシャル マーケットプレース分析レポートの有効期間エクスポート

[ダウンロード] ページでは、エンド ユーザーが次の操作を実行できます。

- コマーシャル マーケットプレース分析レポートの有効期間を csv および tsv 形式でエクスポートする。
- 任意の日付範囲のコマーシャル マーケットプレース分析レポートをエクスポートする。
- 6 または 12 か月間のコマーシャル マーケットプレース分析レポートをエクスポートする。

分析レポートの有効期間エクスポート機能のサポート:

| レポート | 有効期間エクスポート | 日付に基づく期間 |
| - | - | - |
| Orders | ![緑のチェック マーク](media/downloads-dashboard/check-green-yes.png) | ![緑のチェック マーク](media/downloads-dashboard/check-green-yes.png) |
| 顧客 | ![緑のチェック マーク](media/downloads-dashboard/check-green-yes.png) | ![緑のチェック マーク](media/downloads-dashboard/check-green-yes.png) |
| Marketplace の分析情報 | ![緑のチェック マーク](media/downloads-dashboard/check-green-yes.png) | ![緑のチェック マーク](media/downloads-dashboard/check-green-yes.png) |
| 使用法 | ![黒の X マーク](media/downloads-dashboard/check-black-no.png) | 最大 1 年間 |
|

ユーザーは、[ダウンロード] ダッシュボードからレポートの非同期ダウンロードをスケジュールできます。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

[![[ダウンロード] ページからレポートの非同期ダウンロードをスケジュールする](media/downloads-dashboard/download-reports-workspaces.png)](media/downloads-dashboard/download-reports.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

[![[ダウンロード] セクションからレポートの非同期ダウンロードをスケジュールする](media/downloads-dashboard/download-reports.png)](media/downloads-dashboard/download-reports.png#lightbox)

---

## <a name="next-steps"></a>次のステップ

- パートナー センターのコマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析](analytics.md)」を参照してください。
- オファーのマーケットプレース アクティビティを要約した集計データのグラフ、傾向、値については、「[コマーシャル マーケットプレース分析の概要ダッシュボード](summary-dashboard.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「[コマーシャル マーケットプレース分析の使用量ダッシュボード](usage-dashboard.md)」を参照してください。
- 成長傾向など、顧客の詳細については、「[コマーシャル マーケットプレース分析の顧客ダッシュボード](customer-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「[コマーシャル マーケットプレース分析の [評価とレビュー] ダッシュボード](ratings-reviews.md)」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析に関するよく寄せられる質問と用語](analytics-faq.yml)」を参照してください。
