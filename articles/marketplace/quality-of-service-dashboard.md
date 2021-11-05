---
title: パートナー センターのサービスの品質ダッシュボード
description: サービスの品質について使用できる、パートナー センター (Azure Marketplace) 内のさまざまなレポートを示します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: smannepalle
ms.date: 09/27/2021
ms.openlocfilehash: c7df2a30d3de65966d289c0caad7bc6afaca6afd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088029"
---
# <a name="quality-of-service-qos-dashboard"></a>サービスの品質 (QoS) ダッシュボード

このダッシュボードには、すべてのオファーの展開の品質が表示されます。 オファーの展開の成功率が高いほど、顧客に提供されるサービスの品質が高くなります。

次の項目のグラフィカル表示が表示されます。

- [[Quality by offers]\(オファー別の品質\)](#quality-by-offers)
- [[Quality by deployment duration]\(展開期間別の品質\)](#quality-by-deployment-duration)
- ["展開数"](#deployment-count)
- [[Deployment count by status]\(状態別の展開数\)](#deployment-count-by-status)
- [[Deployment errors codes and resources]\(展開のエラー コードとリソース\)](#deployment-errors-codes-and-resources)
- [[Deployment errors by offer plan]\(オファー プラン別の展開エラー\)](#deployment-errors-by-offer-plan)
- [[Deployment reliability by location]\(場所別の展開の信頼性\)](#deployment-reliability-by-location)

さらに、[オファーの展開の詳細](#offer-deployment-details)を表形式で表示します。

> [!IMPORTANT]
> 現在、このダッシュボードは、(プライベート オファーではなく) 全体向けの **Azure アプリケーション** オファーでのみ使用できます。

現在、この機能は、Azure Resource Manager (ARM) テンプレートを使用して Azure アプリケーション オファーの展開を実行しているすべてのパートナーに適用されます (ただしプライベート オファーを除きます)。 このレポートには、他のマーケットプレース オファーのデータは表示されません。

## <a name="access-the-quality-of-service-dashboard"></a>サービスの品質ダッシュボードにアクセスする

[!INCLUDE [preview interface note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします

1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センターの [分析情報] タイルを示しています。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左側のメニューで、 **[サービスの品質]** を選択します。

    [![[分析情報] ダッシュボードの左側のナビゲーションを示しています。](./media/quality-of-service/insights-overview-workspaces.png)](./media/quality-of-service/insights-overview-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします

1. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  > [ **[分析]**](https://partner.microsoft.com/dashboard/commercial-marketplace/quality-of-service/summary) >  **[サービスの品質]** を選択します。

---

## <a name="elements-of-the-quality-of-service-dashboard"></a>サービスの品質ダッシュボードの要素

次のセクションでは、[サービスの品質] (QoS) ダッシュボードの使用方法とデータの読み取り方法について説明します。

### <a name="month-range"></a>月範囲

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

各ページの右上隅に月範囲の選択があります。 過去 6 か月または 12 か月を基準として月範囲を選択するか、カスタムの月範囲 (最大 12 か月間) を選択して、 **[サービスの品質]** ページのグラフの出力をカスタマイズします。 既定の月範囲 (選択期間) は 6 か月です。

[![サービスの品質ダッシュボードのフィルターを示します。](./media/quality-of-service/qos-filters-workspaces.png)](./media/quality-of-service/qos-filters-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

各ページの右上隅に月範囲の選択があります。 過去 6 か月または 12 か月を基準として月範囲を選択するか、カスタムの月範囲 (最大 12 か月間) を選択して、 **[サービスの品質]** ページのグラフの出力をカスタマイズします。 既定の月範囲 (選択期間) は 6 か月です。

---

### <a name="quality-by-offers"></a>[Quality by offers]\(オファー別の品質\)

このグラフは、オファー別のサービス品質とそれに対応する SKU を示しています。 これは、**合計**、**成功**、**失敗** のオファーの展開のメトリックと傾向を月単位で提供します。 棒グラフは、展開の数を表します。

折れ線グラフは、次の変化率を表します。

- 展開の合計 ( **[すべて]** タブ)
- 成功した展開 ( **[成功]** タブ)
- 失敗した展開 ( **[失敗]** タブ)。

グラフには、すべてのオファーのメトリックと傾向が表示されます。 上位のオファーがグラフに表示され、残りのオファーは <bpt id="p1">**</bpt>[残りすべて]<ept id="p1">**</ept> としてグループ化されます。

:::image type="content" source="media/quality-of-service/quality-by-offers-1.png" alt-text="オファー別の品質グラフのバージョン 1 を示します。":::

このグラフについて:

- 凡例で特定のオファーを選択すると、そのオファーと関連する SKU のみがグラフに表示されます。
- グラフのスライスにポインターを合わせると、展開数とそのオファーの割合が、すべてのオファーの合計展開数と比較して表示されます。
- [Quality by offers]\(オファー別の品質\) の傾向には、月別の増加または減少傾向が表示されます。

:::image type="content" source="media/quality-of-service/quality-by-offers-2.png" alt-text="オファー別の品質グラフのバージョン 2 を示します。":::

このグラフについて:

- 表示する特定のオファーと関連付けられている SKU を凡例から選択します。
- [Quality by offers]\(オファー別の品質\) の傾向には、月別のメトリックが表示されます。
- オファーの前月比の傾向を表示する場合は、そのオファーの最大 3 つの SKU を選択します。
- 折れ線グラフは、前のグラフで示したのと同じ変化率を表します。

### <a name="quality-by-deployment-duration"></a>[Quality by deployment duration]\(展開期間別の品質\)

このグラフは、展開の成功と失敗の平均期間のメトリックと傾向を示しています。 ドロップダウン メニューでオファーを選択して、メトリックを表示します。 表形式ビューで SKU を選択するか、検索バーに入力します。 さまざまな平均展開期間 (分単位) を次に示します。

- **[Success duration] \(成功期間\)** – オファーの展開の状態が [成功] とマークされた展開期間の平均時間。 この集計メトリックは、成功状態としてマークされた展開の開始タイムスタンプと終了タイムスタンプの間の期間を使用して計算されます。
- **[Failure duration] \(失敗期間\)** – オファーの展開の状態が [失敗] とマークされた展開期間の平均時間。 この集計メトリックは、失敗状態としてマークされた展開の開始タイムスタンプと終了タイムスタンプの間の期間を使用して計算されます。
- **[First Successful deployment duration] \(最初に成功した展開期間\)** – オファーの展開の状態が [成功] とマークされた展開期間の平均時間。 この集計メトリックは、成功状態としてマークされた、最初の展開の開始タイムスタンプから最後の展開の終了タイムスタンプまでの期間を使用して計算されます。 これは、特定のオファー SKU と顧客に対してマークされた展開ごとに計算されます。

:::image type="content" source="media/quality-of-service/deployment-duration-quality.png" alt-text="展開期間の品質グラフを表示します。":::

このグラフについて:

- 表示する [Failure duration] \(失敗期間\)、[Success duration] \(成功期間\)、または [First Successful deployment duration] \(最初に成功した展開期間\) を凡例から選択します。
- 折れ線グラフには、成功、失敗、前の試行で失敗した成功した展開としてマークされた展開の平均期間が表示されます。
- 最初の展開の平均時間には、展開が成功としてマークされる前の失敗の試行に費やされた時間が考慮されています。

### <a name="deployment-count"></a>"展開数"

このグラフは、オファーの展開の合計を示しています。 メトリックと成長傾向は折れ線グラフで表されます。 各月の値を表示するには、折れ線グラフにマウス ポインターを合わせます。

展開メトリックの下にあるパーセント値は、選択した月の範囲内での増加量または減少量を表します。

:::image type="content" source="media/quality-of-service/deployment-count.png" alt-text="展開数のグラフを表示します。":::

このグラフについて:

- 選択した日付範囲でのオファーの展開の総数。
- 選択した日付範囲でのオファーの展開の変化率。
- オファーの展開の総数の前月比の傾向。

### <a name="deployment-count-by-status"></a>[Deployment count by status]\(状態別の展開数\)

このグラフは、選択した月の範囲について、顧客による成功および失敗したオファーの展開のメトリックと傾向を示しています。 オファーの展開には、 **[成功]** または **[失敗]** の 2 つの状態があります。

:::image type="content" source="media/quality-of-service/deployment-count-by-status.png" alt-text="状態グラフで展開数を表示します。":::

このグラフについて:

- 選択した日付範囲の成功および失敗したオファーの展開の総数。
- 選択した日付範囲の成功および失敗したオファーの展開の変化率。
- 成功および失敗したオファーの展開数の前月比の傾向。

### <a name="deployment-errors-codes-and-resources"></a>[Deployment errors codes and resources]\(展開のエラー コードとリソース\)

このグラフは、オファーの展開ベースのエラー コードとリソースのメトリックと傾向を示しています。 表形式セクションは、エラー コードとリソースでピボットできます。 最初のサブタブには、分析ベースのエラー コード、説明、およびエラー数が表示されます。 2 つ目には、展開リソースの分析ベースが表示されます。 折れ線グラフには、合計エラー数ベースのエラー コードとリソースが表示されます。

エラー コードの詳細については、「[Azure Resource Manager を使用した Azure への展開で発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)」および「[Azure サービスのリソース プロバイダー](../azure-resource-manager/management/azure-services-resource-providers.md)」を参照してください。

:::image type="content" source="media/quality-of-service/deployment-error-codes-1.png" alt-text="展開エラー コード グラフのサンプル 1 を示します。":::
:::image type="content" source="media/quality-of-service/deployment-error-codes-2.png" alt-text="展開エラー コード グラフのサンプル 2 を示します。":::

これらのグラフについて:

- 表示する特定のエラーまたはリソースを凡例から選択します。
- 傾向ウィジェットには、月ごとのエラー数が表示されます。
- 前月比の傾向をエラー コードまたはリソース別に表示するには、表から最大 3 つの項目を選択します。
- 表のエラー数に基づいて、展開エラーのエラー コードとリソースを並べ替えます。

### <a name="deployment-errors-by-offer-plan"></a>[Deployment errors by offer plan]\(オファー プラン別の展開エラー\)

このグラフでは、Y 軸は展開エラー数を表し、X 軸は上位オファー プランのパーセンタイル (エラー数による) を表します。

:::image type="content" source="media/quality-of-service/deployment-error-by-offer-plan.png" alt-text="オファー プラン別の展開エラーのグラフを表示します。":::

このグラフについて:

- 棒グラフは、選択した月の範囲の展開エラー数を表します。
- 折れ線グラフの値は、オファー プラン別の累積エラー率を表します。

### <a name="deployment-reliability-by-location"></a>[Deployment reliability by location]\(場所別の展開の信頼性\)

このグラフは、選択した月の範囲で成功した展開数と失敗した展開数のヒート マップを示しています。 また、各リージョンに対する失敗の割合も表示されます。 緑から赤のカラー スケールは、失敗率の低い値から高い値を表します。 表内のレコードを選択すると、展開地域が拡大されます。

:::image type="content" source="media/quality-of-service/deployment-reliability.png" alt-text="場所別の展開の信頼性グラフを表示します。":::

このグラフについて:

- マップを動かして正確な場所を見ることができます。
- 特定の場所にズーム インできます。
- ヒート マップには、特定の場所の成功数、失敗数、および失敗率の展開の詳細を表示する補助グリッドがあります。
- 赤色の領域は失敗率が高く、緑色は低いことを示しています。
- グリッドで国またはリージョンを検索して選択すると、マップ内の場所にズームできます。 **[ホーム]** アイコンを使用すると、元のビューに戻ります。

## <a name="offer-deployment-details"></a>展開の詳細の取得

次の表は、利用可能なすべてのオファーの展開の詳細を示しています。 レポートをダウンロードすると、オファーの展開に関する生データを確認できます。

:::image type="content" source="media/quality-of-service/deployment-details.png" alt-text="展開の詳細な表を表示します。":::

この表について:

- 上位 1,000 件の展開の番号付き一覧を、展開日順に表示します。
- グリッドの各列で並べ替えることができます。
- コントロールを展開し、表をエクスポートできます。
- 詳細ビューはページ分割されています。 ページの下部で他のページを選択できます。

### <a name="dictionary-of-data-terms"></a>データ用語の辞書

| 列名 | 属性名 | 定義 |
| --- | --- | --- |
| プラン ID | プラン ID | デプロイされたオファーの名前 |
| Sku | Sku | デプロイされたオファーのプランまたは SKU の名前 |
| 展開の状態 | 展開の状態 | **成功** または **失敗** としてマークされたオファーの展開の状態 |
| サブスクリプション ID | サブスクリプション ID | 顧客のサブスクリプション ID |
| 顧客テナント ID | 顧客テナント ID | 顧客のテナント ID |
| Customer Name | Customer Name | 顧客の名前 |
| テンプレートの種類 | テンプレートの種類 | デプロイされた Azure アプリの種類。 マネージド アプリまたはソリューション テンプレートのいずれかであり、プライベートにすることはできません。 |
| 展開の開始日時 | 展開の開始日時 | 展開の開始時刻 |
| 展開終了時刻 | 展開終了時刻 | 展開の終了時刻 |
| [Deployment Duration:]\(配置期間:\) | [Deployment Duration:]\(配置期間:\) | オファーの展開の合計期間 (ミリ秒単位)。 グラフに分で表示されます。 |
| 展開リージョン | 展開リージョン | Azure App の展開の場所 |
| リソース プロバイダー | リソース プロバイダー | デプロイされた特定のリソースのリソース プロバイダー |
| リソース URI | リソース URI | デプロイされたリソースの URI |
| リソース グループ | リソース グループ | リソースがデプロイされているリソース グループ |
| リソースの種類 | リソースの種類 | デプロイされたリソースの種類 |
| リソース名 | リソース名 | デプロイされたリソースの名前 |
| エラー コード | エラー コード | 展開の失敗のエラー コード |
| エラー名 | エラー名 | 展開の失敗のエラー名 |
| エラー メッセージ | エラー メッセージ | 展開の失敗のエラー メッセージ |
| [Deep Error Code]\(詳細なエラー コード\) | [Deep Error Code]\(詳細なエラー コード\) | エラー コードに関する詳細情報 (ある場合) |
| [Deep Message Code]\(詳細なメッセージ コード\) | [Deep Message Code]\(詳細なメッセージ コード\) | エラー メッセージに関する詳細情報 (ある場合) |
| 関連付け ID | 関連付け ID | 異なる展開を区別するために使用される識別子。 同じ値は、展開されるすべてのリソースが 1 つの展開用であることを意味します。 |
|

## <a name="next-steps"></a>次の手順

- 展開エラーについては、「[Azure Resource Manager を使用した Azure への展開で発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)」を参照してください。
- リソース プロバイダーの詳細については、「[Azure サービスのリソース プロバイダー](../azure-resource-manager/management/azure-services-resource-providers.md)」を参照してください。
- オファーのマーケットプレース アクティビティを要約した集計データのグラフ、傾向、値については、「[コマーシャル マーケットプレース分析の概要ダッシュボード](./summary-dashboard.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](./orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の使用量ダッシュボード<ept id="p1">](usage-dashboard.md)</ept>」を参照してください。
- 成長傾向など、顧客の詳細については、「[コマーシャル マーケットプレース分析の顧客ダッシュボード](./customer-dashboard.md)」を参照してください。
- ライセンスの詳細については、「[コマーシャル マーケットプレース分析の [ライセンス] ダッシュボード](./license-dashboard.md)」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](./downloads-dashboard.md)」を参照してください。
- Microsoft AppSource および Azure Marketplace でのオファーに関する顧客からのフィードバックを統合して表示する方法については、[コマーシャル マーケットプレース分析の [評価とレビュー] ダッシュボード](./ratings-reviews.md)に関するページを参照してください。
- コマーシャル マーケットプレース分析についてのよくあるご質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析の一般的な質問](./analytics-faq.yml)」を参照してください。