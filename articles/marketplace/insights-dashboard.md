---
title: コマーシャル マーケットプレース分析の [Marketplace の分析情報] ダッシュボード
description: パートナー センターのマーケットプレース Web 分析の概要にアクセスすることにより、Microsoft AppSource と Azure Marketplace での顧客エンゲージメントを測定できます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 30b26ea098c66bc807250c0100db93d7564222b6
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069199"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>コマーシャル マーケットプレース分析の [Marketplace の分析情報] ダッシュボード

この記事では、パートナー センターの [Marketplace の分析情報] ダッシュボードについて説明します。 このダッシュボードには、コマーシャル マーケットプレース Web 分析の概要が表示されます。これにより公開元は、次のコマーシャル マーケットプレースのオンライン ストアに掲載されている、個々の製品詳細ページの顧客エンゲージメントを測定できます: Microsoft AppSource と Azure Marketplace。

分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.yml)」を参照してください。

## <a name="marketplace-insights-dashboard"></a>[Marketplace の分析情報] ダッシュボード

[[Marketplace の分析情報] ダッシュボード](https://go.microsoft.com/fwlink/?linkid=2165936)には、Azure Marketplace および AppSource のオファーのビジネス パフォーマンスの概要が示されます。 このダッシュボードは、次のことについて大まかに説明しています。

- ページ アクセス数の傾向
- Call to Action の傾向
- オファー、参照ドメイン、キャンペーン ID に対するページ アクセス数および Call to Action
- 地域ごとの Marketplace の分析情報
- Marketplace の分析情報の詳細テーブル

Marketplace の分析情報ダッシュボードにはクリックストリーム データ データがありますが、これをリードのターゲット エンドポイントで生成されたリードに相関付けることはできません。

> [!NOTE]
> Azure Marketplace または AppSource のオファーにユーザーがアクセスしてからパートナー センターで報告されるまでの最大待機時間は 48 時間です。

## <a name="access-the-marketplace-insights-dashboard"></a>[Marketplace insights]\(Marketplace の分析情報\) ダッシュボードにアクセスする

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センター ホーム ページの [分析情報] タイルの画像。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左メニューの **[Marketplace insights]\(Marketplace の分析情報\)** を選択します。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. 左側のナビゲーション メニューで **[商業マーケットプレース]**  >  **[分析]**  >  **[Marketplace insights]\(マーケットプレースの分析情報\)** を選択します。

---

## <a name="elements-of-the-marketplace-insights-dashboard"></a>[Marketplace の分析情報] ダッシュボードの要素

[Marketplace の分析情報] ダッシュボードでは、2 つのタブに Azure Marketplace および AppSource の Web テレメトリの詳細が表示されます。 次のセクションでは、[Marketplace の分析情報] ダッシュボードの使用方法とデータの読み取り方法について説明します。

### <a name="month-range"></a>月範囲

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

各ページの右上隅には、月範囲の選択が表示されます。 過去 6 か月または 12 か月の月範囲を選択することで、あるいは 12 か月を最大期間とするカスタムの月範囲を選択することで、 **[Marketplace Insights]\(Marketplace の分析情報\)** ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

[ ![[Marketplace Insights]\(Marketplace の分析情報\) ダッシュボードの月フィルターの画像。](./media/insights-dashboard/marketplace-insights-filters.png) ](./media/insights-dashboard/marketplace-insights-filters.png#lightbox)

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

各ページの右上隅には、月範囲の選択が表示されます。 過去 6 か月または 12 か月の月範囲を選択することで、あるいは 12 か月を最大期間とするカスタムの月範囲を選択することで、 <bpt id="p1">**</bpt>[Marketplace の分析情報]<ept id="p1">**</ept> ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

<bpt id="p1">:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="</bpt>[Marketplace の分析情報] ダッシュボードの月フィルターの画像。<ept id=&quot;p1&quot;>":::</ept>

---

> [!NOTE]
> 視覚化ウィジェットおよびエクスポート レポートのすべてのメトリックでは、ユーザーが選択した計算期間が優先されます。

### <a name="page-visits-trends"></a>ページ アクセス数の傾向

[Marketplace の分析情報] の <bpt id="p1">**</bpt>[ビジター]<ept id="p1">**</ept> グラフには、選択した計算期間の<bpt id="p2">_</bpt>ページ アクセス<ept id="p2">_</ept>および<bpt id="p3">_</bpt>ユニーク ビジター<ept id="p3">_</ept>の数が表示されます。

<bpt id="p1">**</bpt>ページ アクセス<ept id="p1">**</ept>: この数値は、選択した計算期間について、オファーのリスト ページ (製品詳細ページ) の個別のユーザー セッション数を表します。 赤と緑の割合インジケーターは、ページ アクセスの増加率を表します。 傾向グラフは、月単位のページ アクセス数を表します。

<bpt id="p1">**</bpt>ユニーク ビジター<ept id="p1">**</ept>: この数値は、Azure Marketplace および AppSource のオファーの、選択した計算期間における個別のビジター数を表します。 1 つまたは複数の製品詳細ページにアクセスしたビジターは、1 人のユニーク ビジターとしてカウントされます。

<bpt id="p1">[</bpt><ph id="ph1">![</ph>[Marketplace の分析情報] ダッシュボードのビジター グラフの画像。<ept id="p1">](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)</ept>

### <a name="call-to-actions-trend"></a>Call to Action の傾向

この数値は、オファーのリスト ページ (製品詳細ページ) で <bpt id="p1">**</bpt>[Call to Action]<ept id="p1">**</ept> のボタンがクリックが実行された回数を表します。 <bpt id="p1">_</bpt>Calls to Action<ept id="p1">_</ept> は、ユーザーが <bpt id="p2">**</bpt>[今すぐ入手 (無料)]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[無料試用版]<ept id="p3">**</ept> 、 <bpt id="p4">**</bpt>[問い合わせ]<ept id="p4">**</ept> 、 <bpt id="p5">**</bpt>[体験版]<ept id="p5">**</ept> の各ボタンを選択したときにカウントされます。 *[Consent given]\(得た同意\)* は、Microsoft またはパートナーに対してお客様から得た同意のクリック数の合計を表し、あなたのプランで獲得したお客様の数と同じになります。 次の 2 つの例は、 *[Consent given]\(得た同意\)* のクリック数が表示される場所を示しています。

:::image type="content" source="./media/insights-dashboard/consent-screen.png" alt-text="同意ボタンが選択されている場所の画像。":::

次のグラフは、*CTA* と *[Consent given]\(得た同意\)* のメトリックを示しています。

:::image type="content" source="./media/insights-dashboard/consent-given-graph.png" alt-text="[Calls to Action]\(行動の呼びかけ) と [Consent given]\(得た同意\) のサンプル グラフを示しています。":::

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>オファー、参照ドメイン、キャンペーン ID に対するページ アクセス数および Call to Action

#### <a name="offers"></a>オファー

特定のプランを選択すると、ページのアクセス数、行動の呼びかけ、得た同意のクリック数の月ごとの傾向がグラフで表示されます。

:::image type="content" source="./media/insights-dashboard/offers-funnel-graph.png" alt-text="プランのじょうごグラフのサンプルを示しています。":::

#### <a name="referral-domains"></a>参照ドメイン

特定の参照ドメインを選択すると、右側のグラフに、ページ アクセス、行動の呼びかけ、同意のクリック数の月ごとの傾向が表示されます。 さらに、プラットフォーム (Web サイトとクライアント) の列があり、AppSource プランの場合にのみ表示されます。 じょうごビューには、ページ ビュー、行動の呼びかけ、得た同意のクリック数の返還率がグラフに表示されます。

:::image type="content" source="./media/insights-dashboard/referral-domains-funnel-graph.png" alt-text="参照ドメインのじょうごグラフのサンプルを示します。":::

#### <a name="campaign-ids"></a>キャンペーン ID

特定のキャンペーン ID を選択すると、キャンペーンの成功を理解できるようになります。 キャンペーンごとに、ページ アクセス、行動の呼びかけ、得た同意のクリック数の月ごとの傾向がグラフに表示されます。

<bpt id="p1">:::image type="content" source="./media/insights-dashboard/campaign-id-funnel-graph.png" alt-text="</bpt>[Marketplace の分析情報] ダッシュボードのキャンペーン グラフの画像。<ept id=&quot;p1&quot;>":::</ept>

### <a name="marketplace-insights-by-geography"></a>地域ごとの Marketplace の分析情報

ヒートマップには、選択した計算期間のページ アクセス、ユニーク ビジター、および Call to Action (CTA) の数が表示されます。 マップ上の明るい色はユニーク ビジターが少ないことを表し、暗い色はユニーク ビジターが多いことを表します。 テーブル内のレコードを選択すると、1 つの国や地域が拡大されます。

[ ![[Marketplace Insights]\(Marketplace の分析情報\) ダッシュボードの地理的分布グラフの画像。](./media/insights-dashboard/geographical-spread.png) ](./media/insights-dashboard/geographical-spread.png#lightbox)

次のことを考慮してください。

- マップを動かして正確な場所を見ることができます。
- 特定の場所にズーム インできます。
- ヒートマップには、特定の場所での顧客数、注文数、正規化された使用量の詳細を表示するための補助グリッドがあります。
- グリッドで国またはリージョンを検索して選択すると、マップ内の場所にズームできます。 地図内の **[ホーム]** ボタンを選択すると、元のビューに戻ります。

### <a name="marketplace-insights-details-table"></a>Marketplace の分析情報の詳細テーブル

このテーブルには、選択したオファーのページのページ アクセスと Call to Action の一覧が日付順に表示されます。

- レコード数が 1,000 未満の場合、.TSV または .CSV ファイルにデータを抽出できます。
- レコード数が 1,000 を超える場合、エクスポートされたデータは今後 30 日間、ダウンロード ページに非同期で配置されます。
- オファー名およびキャンペーン名でデータをフィルター処理して、関心のあるデータを表示します。

> [!TIP]
> ウィジェットの右上隅にあるダウンロード アイコンを使用して、データをダウンロードできます。 "サムズアップ" アイコンまたは "サムズダウン" アイコンをクリックすることで、各ウィジェットに関するフィードバックを提供できます。

| 列名<br>ユーザー インターフェイス | 属性名 | 定義 | プログラムによるアクセス<br>レポートの列名 |
| ------------ | ------------- | ------------- | ------------- |
| Date | アクセスした日 | Azure Marketplace や AppSource のプランのページにアクセスした日付、またはそのページで CTA クリック イベントが生成された日付。 | Date |
| プラン名 | プラン名 | コマーシャル マーケットプレース オファリングの名前。 | OfferName |
| 参照ドメイン | 参照ドメイン | ページ アクセスが発生した参照ドメインの名前。 ページ アクセス用にキャプチャされた参照ドメインがない場合、対応するエントリは "参照ドメインが存在しません" です。 |  ReferralDomain |
| 国名 | 国名 | ページ アクセスが発生した国の名前。 | CountryName |
| ページ アクセス | ページ アクセス | 特定の日付のプラン名に関連付けられているページ アクセスの数。 | PageVisits |
| 今すぐ入手する | 今すぐ入手する | 特定の日付について、プランのページの "今すぐ入手する" CTA のクリック回数。 | GetItNow |
| お問い合わせ | お問い合わせ | 特定の日付について、プランのページの "お問い合わせ" CTA のクリック回数。 | ContactMe |
| 体験版 | 体験版 | 特定の日付について、プランのページの "体験版" CTA のクリック回数。 | TestDrive |
| 無料試用版 | 無料試用版 | 特定の日付について、プランのページの "無料試用版" CTA のクリック回数。 | FreeTrial |
| キャンペーン | キャンペーンの名前 | キャンペーン名に照らして Web テレメトリ (ページ アクセスと CTA クリック) を把握する機能。 | キャンペーン |
| 得た同意 | 得た同意 | Microsoft またはパートナーに対してお客様から得た同意のクリック数の合計カウント | consentGivenCount |
| プラットフォーム | プラットフォーム | ページ ビュー、CTA、または同意のクリック数のソースとして Web サイトまたはクライアント (製品内ストア) を示します。 | プラットフォーム |
| 該当なし | サイト | ページ アクセスまたは CTA クリックの発生元となったネットショップの名前。 次の値を指定できます。<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | サイト |
|

## <a name="next-steps"></a>次のステップ

- コマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析レポートにアクセスする](analytics.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の使用量ダッシュボード<ept id="p1">](usage-dashboard.md)</ept>」を参照してください。
- 成長傾向など、顧客の詳細については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の顧客ダッシュボード<ept id="p1">](customer-dashboard.md)</ept>」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](downloads-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「<bpt id="p1">[</bpt>パートナー センターの評価とレビューの分析ダッシュボード<ept id="p1">](ratings-reviews.md)</ept>」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](analytics-faq.yml)」を参照してください。
