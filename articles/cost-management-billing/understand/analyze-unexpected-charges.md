---
title: 想定外の Azure 料金を分析する
description: Azure サブスクリプションの想定外の料金を分析する方法について説明します。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: de7787ab9b36bee5bce2f29f79260682289419c7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705784"
---
# <a name="analyze-unexpected-charges"></a>想定外の料金を分析する

組織向けに構築されたクラウド リソース インフラストラクチャは、複雑になりがちです。 Azure にはさまざまな種類のリソースがあり、その多くはそれぞれ異なる料金が設定されています。 Azure リソースは、組織内のさまざまなチームによって所有され、各種リソースに適用される課金モデルの種類も多様です。 そうした料金について理解を深めるために、まず、以降のセクションで取り上げる 1 つまたは複数の戦略を使用して分析を行ってみましょう。

## <a name="review-invoice-for-resource-responsible-for-charge"></a>料金の対象となるリソースの請求書を確認する

料金に関連付けられているリソースを特定する際に使用できる手法やツールは、Azure サービスの購入方法から明らかにすることができます。 自分に該当する手法を明らかにするには、まず[自分の Azure プランの種類を特定](../costs/understand-cost-mgt-data.md#determine-your-offer-type)します。 次に、[サポートされている Azure プラン](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers)の一覧で、該当する顧客カテゴリを特定します。

該当する顧客タイプに基づいて請求書を確認する方法を示した詳細な手順が、以下の各記事で提供されています。 それぞれの記事には、特定の請求期間における使用状況とコスト明細を含んだ CSV ファイルのダウンロード手順が記載されています。

- [従量課金制請求書の確認プロセス](review-individual-bill.md#charges)
- [マイクロソフト エンタープライズ契約の請求書の確認プロセス](review-enterprise-agreement-bill.md)
- [Microsoft 顧客契約の確認プロセス](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft Partner Agreement の確認プロセス](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Azure の請求書では、月の料金が "_メーター_" ごとに集計されます。 メーターは、リソースの使用状況を経時的に追跡したり、請求書の計算を行ったりする目的で使用されます。 仮想マシンなど、Azure リソースを 1 つ作成すると、そのリソースに対して 1 つまたは複数のメーター インスタンスが作成されます。

分析対象の請求書に記載されている _MeterName_ に基づいて、使用状況の CSV ファイルをフィルター処理すると、そのメーターに該当するすべての明細項目が表示されます。 その明細項目の _InstanceID_ は、料金の発生元となった実際の Azure リソースに対応します。

該当するリソースが見つかったら、Cost Management のコスト分析を使用して、リソースに関連するコストを詳しく分析できます。 コスト分析の使用について詳しくは、[コスト分析の開始](../costs/quick-acm-cost-analysis.md)に関するページを参照してください。

## <a name="review-invoiced-charges-in-cost-analysis"></a>[コスト分析] で請求された料金を確認する

Azure portal で請求書の詳細を表示するには、分析する請求書に関連付けられているスコープの [コスト分析] に移動します。 **[請求書の詳細]** ビューを選択します。 請求書の詳細には、請求書に記載される料金が表示されます。

[![請求書の詳細を示す例](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

請求書の詳細を表示すると、予期しないコストがかかるサービスを特定することや、[コスト分析] のリソースに直接関連付けられているリソースを確認することができます。 たとえば、Virtual Machines サービスの料金を分析する場合は、 **[累積コスト]** ビューに移動します。 次に、細分性を **[Daily]\(日単位\)** に設定し、 **[Service name: Virtual machines]\(サービス名: 仮想マシン\)** で料金をフィルター処理し、 **[リソース]** で料金をグループ化します。

[![仮想マシンの累積コストを示す例](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>一定期間におけるコストの急増を特定する

最近発生したどのコストによって請求料金に変化が生じたのかが、明らかでない場合もあります。 どのような変化が起こったのかは、コスト分析を使用して、[日単位または月単位の経時的なコスト内訳を確認](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month)することで把握できます。 ビューの作成後、 **[サービス]** または **[リソース]** で料金をグループして変化を特定します。 データを見やすくするために、ビューを **折れ線** グラフに変更することもできます。

![コスト分析でコストの経時変化を示している例](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>リソースの価格と課金モデルを特定する

1 つのリソースから、Azure の複数の製品やサービスにまたがって料金が発生することもあります。 Azure サービスごとの価格について詳しくは、[製品別の Azure の価格](https://azure.microsoft.com/pricing/#product-pricing)に関するページをご覧ください。 たとえば、Azure に作成された 1 つの仮想マシン (VM) に対して、その使用状況を追跡するために次のメーターが作成される場合があります。 価格はそれぞれ異なる場合があります。

- コンピューティング時間
- IP アドレス時間
- データ転送 (受信)
- データ転送 (送信)
- Standard マネージド ディスク
- Standard マネージド ディスク操作
- Standard IO - ディスク
- Standard IO - ブロック BLOB (読み取り)
- Standard IO - ブロック BLOB (書き込み)
- Standard IO - ブロック BLOB (削除)

VM が作成されると、各メーターが使用状況レコードの出力を開始します。 この使用状況とメーターの価格が Azure の測定システムで追跡されます。 請求の計算に使用されたメーターは、使用状況 CSV ファイルで確認できます。

## <a name="find-people-responsible-for-the-resource-and-engage"></a>リソースの担当者を探して協力を促す

リソースに対して行われた変更については、特定のリソースを担当するチームが把握していることが少なくありません。 料金が発生した理由を特定する際には、そうしたチームに協力を促すことが効果的です。 たとえば所有チームが最近、リソースを作成したり、その SKU を更新 (それによってリソースのレートが変化) したり、コードの変更によってリソースに対する負荷を増やしたりした可能性があります。 リソースの所有者を特定する詳しい方法については、引き続き以降のセクションをお読みください。

### <a name="analyze-the-audit-logs-for-the-resource"></a>リソースの監査ログを分析する

リソースを表示するアクセス許可があれば、その監査ログにアクセスすることができます。 そのログを確認して、リソースへの直近の変更を担当したユーザーを見つけてください。 詳細については、「[Azure アクティビティ ログ イベントを表示して取得する](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)」を参照してください。

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>リソースの親スコープに対するユーザーのアクセス許可を分析する

通常、サブスクリプションまたはリソース グループへの書き込みアクセス権限を持つ人物は、作成されたリソースについての情報を把握しています。 その人物ならリソースの目的を説明したり、事情を知っている担当者を紹介したりできるはずです。 サブスクリプション スコープのアクセス許可を持つユーザーを特定する方法については、[Azure リソースに対するユーザーのアクセス権の確認](../../role-based-access-control/check-access.md)に関する記事をご覧ください。 リソース グループについても同じようなプロセスを使用できます。

## <a name="get-help-to-identify-charges"></a>料金を特定するためにサポートを要請する

前述の戦略を使用しても、料金を請求された理由がわからない場合や、課金の問題について別途サポートが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Cost Management を使用してクラウドへの投資を最適化する](../costs/cost-mgt-best-practices.md)方法を確認します。