---
title: 購入すべき Azure の予約を判断する
description: この記事は、購入すべき予約を判断するのに役立ちます。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1b639da3494c0527141347ca61e77980d29a59ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80135557"
---
# <a name="determine-what-reservation-to-purchase"></a>購入する予約を判断する

Azure Databricks を除くすべての予約は、1 時間単位で適用されます。 基準となる一定の使用量に基づいて予約を購入する必要があります。 購入するものを判断する方法は複数あります。この記事は、購入すべき予約を判断するのに役立ちます。

過去の使用量を超える容量を購入しても、予約を十分に活用できません。 十分に活用されない状態は、可能な限り避ける必要があります。 時間内に使用されなかった予約容量が次の時間に引き継がれることはありません。  予約済みの量を超える使用に対しては、より高額な従量課金制料金が適用されます。

## <a name="analyze-usage-data"></a>使用量データを分析する

以降の各セクションを参考に、日単位の使用量データを分析してベースライン使用量を見極め、購入すべき予約を判断してください。

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>VM 予約インスタンスを購入するにあたって使用量を分析する

購入の対象となる適切な VM サイズを見極めましょう。 たとえば、ES シリーズの VM について購入した予約は、E シリーズの VM には適用されません。その逆も同様です。

Promo シリーズの VM は予約割引が適用されないので、分析対象から除外してください。

候補となる VM の使用量を絞り込むには、使用量データに次のフィルターを適用します。

- **MeterCategory** から **Virtual Machines** をフィルターで抽出します。
- **AdditionalInfo** から **ServiceType** 情報を取得します。 この情報から適切な VM サイズを推測できます (例: Standard E32)。
- **ResourceLocation** フィールドを使用して、使用量データの拠点を特定します。

1 日の使用量が 24 時間未満のリソースは無視してください。

インスタンス サイズ ファミリー レベルで分析したい場合は、インスタンス サイズの柔軟性の値を [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv) から取得できます。 その値とご自身のデータとを結合して分析を行ってください。 インスタンス サイズの柔軟性の詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)」を参照してください。

### <a name="analyze-usage-for-a-sql-database-reserved-instance-purchase"></a>SQL Database 予約インスタンスを購入するにあたって使用量を分析する

予約容量は、SQL Database の仮想コアのコンピューティング価格に適用されます。 DTU ベースの価格や SQL ライセンス コストなど、コンピューティング以外のコストには適用されません。

候補となる SQL の使用量を絞り込むには、使用量データに次のフィルターを適用します。


- **MeterCategory** から **SQL Database** をフィルターで抽出します。
- **MeterName** から **vCore** をフィルターで抽出します。
- **MeterSubCategory** から、名前に _Compute_ が含まれるすべての使用量レコードをフィルターで抽出します。

**AdditionalInfo** から **vCores** の値を取得します。 これは使用された仮想コアの数を示します。 この数量は、**仮想コア**の数にデータベースの使用時間数を乗じたものです。

これらのデータから、次の項目についての一定の使用量を把握することができます。

- データベースの種類の組み合わせ (例: 単一データベースあたりのマネージド インスタンスまたはエラスティック プール)。
- サービス レベル (例: General Purpose、Business Critical)。
- 世代 (例: Gen 5)。
- リソースの場所

### <a name="analysis-for-sql-data-warehouse"></a>SQL Data Warehouse の分析

予約容量は、SQL Data Warehouse の DWU の使用量に適用され、購入は 100 DWU 単位となります。 候補となる SQL の使用量を絞り込むには、使用量データに次のフィルターを適用します。

- **MeterName** から **100 DWU** をフィルターで抽出します。
- **Meter Sub-Category** から **Compute Optimized Gen2** をフィルターで抽出します。

**Resource Location** フィールドを使用して、リージョンにおける SQL DW の使用量を特定します。

SQL Data Warehouse の使用量は、一日を通してスケールアップしたりスケールダウンしたりする可能性があります。 SQL Data Warehouse インスタンスを管理していたチームと話し合って、基準となる使用量を把握してください。

Azure portal の [予約] に移動し、100 DWU の倍数単位で SQL Data Warehouse の予約容量を購入します。

## <a name="reservation-purchase-recommendations"></a>予約購入の推奨量

予約購入の推奨量は、過去 7 日、30 日、60 日にわたる時間単位の使用量データを分析することで計算されます。 予約を使用していた場合のコストが Azure によって計算され、当該期間に発生した実際の従量課金制のコストと比較されます。 この計算は、当該期間に使用された各数量について実行されます。 コスト削減効果が最大となる数量が提案されます。 

たとえば、使用する VM の数が大半の時間は 500 台であるものの、状況によっては 700 台にまで急増することがあるとします。 このケースでは、500 台と 700 台の両方の VM 数についてコスト削減効果が計算されます。 700 台の VM が使用される状況は散発的にしか生じないため、推奨量の計算では、500 台を予約購入したときにコスト削減効果が最大になると判断され、数量 500 台についての推奨量が提示されます。

以下の点に注意してください。

- 予約の推奨量は、ご自身に適用されるオンデマンドの使用率を使用して計算されます。
- 推奨量は、インスタンス サイズ ファミリーではなく個々のサイズについて計算されます。
- スコープに対する推奨数量は、そのスコープの予約購入日と同日に減じられます。
    - ただし複数のスコープにまたがる予約の推奨数量については、更新に最大 25 日かかることがあります。 たとえば共有スコープの推奨量に基づいて予約を購入した場合、単一サブスクリプション スコープの推奨量が調整されて減じられるまでに最大 25 日かかる場合があります。

## <a name="recommendations-in-the-azure-portal"></a>Azure portal における推奨情報

レコメンデーション エンジンによって計算される予約購入は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs) の **[Recommended]\(推奨\)** タブに表示されます。 以下に例の画像を示します。

![推奨量を示す画像](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Cost Management Power BI アプリにおける推奨情報

マイクロソフト エンタープライズ契約および Microsoft 顧客契約のお客様は、VM RI カバレッジ レポートを利用して VM と購入の推奨情報を得ることができます。 カバレッジ レポートには、合計使用量と、予約インスタンスによってカバーされる使用量が表示されます。

1. [Cost Management アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)を入手します。
2. VM RI カバレッジ レポートに移動します。購入するスコープに応じて共有スコープまたは単一スコープを選択してください。
3. リージョン、使用量の表示対象となるインスタンス サイズ ファミリー、RI カバレッジ、選択したフィルターに対する購入推奨情報を選択します。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor における推奨事項

予約購入の推奨量は、[Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview) から入手できます。

- Advisor で提供されるのは、単一サブスクリプションのスコープでの推奨事項のみです。
- Advisor の推奨量は、30 日間のルックバック期間で計算されます。 推定コスト削減効果は、予約期間として 3 年を想定しています。
- 共有スコープの予約を購入した場合、Advisor の予約購入に関する推奨事項が非表示になるまでに最大で 30 日間かかることがあります。

## <a name="recommendations-using-apis"></a>API を使用した推奨情報

推奨情報をプログラムで表示するには、[Reservation Recommendations](/rest/api/consumption/reservationrecommendations/list) REST API を使用します。

## <a name="next-steps"></a>次のステップ

- [Azure の予約の管理](manage-reserved-vm-instance.md)
- [従量課金制のサブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)