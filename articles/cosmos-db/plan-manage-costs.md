---
title: Azure Cosmos DB のコストを計画および管理する
description: Azure portal でコスト分析を使用して、Azure Cosmos DB のコストを計画および管理する方法を説明します。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 98e849791acd71ea8bf3ac9cb1949da9f562e749
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490830"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure Cosmos DB のコストを計画および管理する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、Azure Cosmos DB のコストを計画および管理できる方法について説明します。 リソースを作成する前に、まず、Azure Cosmos DB Capacity Calculator を使用してワークロード コストを見積もります。 その後、推定コストを確認し、リソースの作成を開始できます。

Azure Cosmos DB リソースの使用を開始したら、Cost Management 機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Azure Cosmos DB のコストは、Azure の請求に記載された月額料金の一部でしかありません。 この記事では、Azure Cosmos DB のコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。

## <a name="prerequisites"></a>前提条件

### <a name="provisioned-throughput-or-serverless"></a>プロビジョニングされたスループットまたはサーバーレス

Azure Cosmos DB では、[プロビジョニングされたスループット](set-throughput.md)および[サーバーレス](serverless.md)という 2 種類の容量モードがサポートされています。 Azure Cosmos DB の使用量に対する課金方法は、この 2 つのモード間で大きく異なるので、ご利用のワークロードに最適なものを選択することが重要です。 この選択方法についてのガイダンスと推奨事項については、「[プロビジョニングされたスループットとサーバーレスのどちらかを選択する方法](throughput-serverless.md)」を参照してください。

### <a name="cost-analysis"></a>コスト分析

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="estimate-costs-before-using-azure-cosmos-db"></a>Azure Cosmos DB を使用する前にコストを見積もる

Azure Cosmos DB は、プロビジョニングされたスループットとサーバーレスの 2 つの異なる容量モードで利用できます。 どちらのモードでもまったく同じデータベース操作を実行できますが、これらの操作に対する課金方法は異なります。

### <a name="estimate-provisioned-throughput-costs"></a>プロビジョニングされたスループット コストを見積もる

プロビジョニングされたスループット モードで Azure Cosmos DB を使用する予定の場合は、Azure Cosmos アカウントでリソースを作成する前に、[Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) を使用してコストを見積もってください。 Capacity Calculator は、ワークロードに必要なスループットとコストの見積もりを取得するために使用されます。 コストとパフォーマンスを最適化するうえで、ワークロードにとって適切な量のプロビジョニング済みスループット、つまり[要求ユニット (RU/秒)](request-units.md) を使用して Azure Cosmos データベースとコンテナーを構成することが不可欠です。 コスト見積もりを取得するには、API の種類、リージョンの数、項目のサイズ、1 秒あたりの読み取り/書き込み要求数、合計格納データなどの詳細を入力する必要があります。 Capacity Calculator の詳細については、[見積もり](estimate-ru-with-capacity-planner.md)に関する記事を参照してください。

次のスクリーンショットは、Capacity Calculator の利用によるスループットとコストの見積もりを示しています。

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Azure Cosmos DB Capacity Calculator でのコスト見積もり":::

### <a name="estimate-serverless-costs"></a><a id="estimating-serverless-costs"></a> サーバーレスのコストを見積もる

サーバーレス モードで Azure Cosmos DB を使用する予定である場合は、月単位で使用する可能性のある[要求ユニット](request-units.md)の数とストレージの GB を見積もる必要があります。 月単位で発行されていたデータベース操作の数を評価することで必要とされる要求ユニットの数を見積もることができ、その数に、対応する RU コストを乗算することもできます。 次の表に、一般的なデータベース操作に対する推定 RU コストを示します。

| 操作 | 推定コスト | メモ |
| --- | --- | --- |
| 項目を作成する | 5 RU | インデックスを作成するプロパティが 5 つ未満の 1 KB 項目の平均コスト |
| 項目を更新する | 10 RU | インデックスを作成するプロパティが 5 つ未満の 1 KB 項目の平均コスト |
| 個々の項目をその ID とパーティション キーによって読み取る (ポイント読み取り) | 1 RU | 1 KB 項目の平均コスト |
| 項目を削除する | 5 RU | |
| クエリを実行する | 10 RU | [インデックス作成](index-overview.md)を最大限に活用し、返す結果が 100 個以下であるクエリの平均コスト |

> [!IMPORTANT] 
> 上の表のメモに注意してください。 操作にかかる実際のコストをより正確に見積もるために、[Azure Cosmos DB Emulator](local-emulator.md) を使用して、[該当する操作の正確な RU コストを測定](find-request-unit-charge.md)できます。 Azure Cosmos DB Emulator はサーバーレスをサポートしていませんが、データベース操作について標準の RU 料金を報告するため、この見積もりに使用できます。

1 か月の間に使用する可能性のある要求ユニットの合計数とストレージの GB を計算したら、次の式からコストの見積もりが返されます: **([要求ユニット数]/1,000,000 * $0.25) + ([ストレージの GB] * $0.25)**

> [!NOTE]
> 前の例で示したコストは、デモンストレーションのみを目的としています。 最新の価格情報については、[価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。

## <a name="understand-the-full-billing-model"></a>完全な課金モデルを理解する

Azure Cosmos DB は、新しいリソースをデプロイする際にコストが発生する Azure インフラストラクチャ上で実行されます。 その他のインフラストラクチャ コストが追加で発生する可能性があることを理解しておくことが重要です。

### <a name="how-youre-charged-for-azure-cosmos-db"></a>Azure Cosmos DB に対する課金方法

Azure Cosmos DB リソースを作成または使用すると、次の測定に基づいて課金される場合があります。

* **データベースの操作** - プロビジョニングまたは消費した要求ユニット (RU/秒) に基づいて課金されます。
  * プロビジョニングされた Standard (手動) スループット - コンテナーまたはデータベースでプロビジョニングされた RU/秒に対する時間料金が課金されます。
  * プロビジョニングされたスループットの自動スケーリング - システムが 1 時間にスケールアップした RU/秒の最大数に基づいて課金されます。

* **使用したストレージ** - 特定の時間にデータとインデックスによって消費されたストレージの合計容量 (GB 単位) に基づいて課金されます。

バックアップ ストレージ、分析ストレージ、可用性ゾーン、複数リージョンの書き込みなどの Azure Cosmos DB 機能を使用している場合は、追加料金が発生します。 請求期間終了時に、各測定の料金が合計されます。 請求書には、Azure Cosmos DB に対して発生したすべてのコストに関するセクションが表示されています。 測定ごとに個別の行項目があります。 詳細については、[価格モデル](how-pricing-works.md)に関する記事を参照してください。

### <a name="using-azure-prepayment"></a>Azure 前払いを使用する

Azure Cosmos DB の料金は、Azure 前払いのクレジットで支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

Azure portal から Azure Cosmos DB リソースの使用を開始するときに、推定コストを確認できます。 次のステップに従ってコスト見積もりを確認します。

1. Azure portal にサインインし、Azure Cosmos アカウントに移動します。
1. **[概要]** セクションに移動します。
1. 下部にある **[コスト]** グラフを確認します。 このグラフは、構成可能な期間における現在のコストの推定値を示しています。
1. グラフ コンテナーなどの新しいコンテナーを作成します。
1. 400 RU/秒など、ワークロードのために必要なスループットを入力します。 スループット値を入力すると、次のスクリーンショットに示すように、料金見積もりを表示できます。

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure portal でのコスト見積もり":::

Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限を取り除くことは可能です。 使用制限の詳細については、「[Azure の使用制限](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。

Azure Cosmos DB の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="monitor-costs"></a>コストを監視する

Azure Cosmos DB でリソースを使用するとコストが発生します。 リソース使用のユニット コストは、期間 (秒、分、時間、日) や要求ユニット使用量によって異なります。 Azure Cosmos DB の使用が始まるとすぐに、コストが発生して、Azure portal の[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)のウィンドウでそれを参照することができます。

コスト分析を使用すると、さまざまな期間について、Azure Cosmos DB のコストをグラフや表で表示できます。 日単位、現在の分、以前の月、年単位などです。 予算や予想コストを基準としてコストを表示することもできます。 長期にわたるより長い期間のビューに切り替えると、支出の傾向を特定し、超過出費が発生した可能性のある時期を確認するのに役立ちます。 予算を作成したら、それを超えた場所も簡単に確認できます。

Azure Cosmos DB のコストをコスト分析で表示するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal でスコープを開き、メニューから **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。

1. 既定では、最初のドーナツ グラフにすべてのサービスのコストが表示されます。 グラフ内の "Azure Cosmos DB" というラベルが付いた領域を選択します。

1. Azure Cosmos DB など、1 つのサービスのコストを絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、一覧から **[Azure Cosmos DB]** を選択します。 次に、Azure Cosmos DB だけのコストを示す例を示します。

   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="[コスト分析] ペインを使用してコストを監視する":::

前の例では、2月について、Azure Cosmos DB の現在のコストが表示されています。グラフには、場所別とリソース グループ別の Azure Cosmos DB コストも含まれています。

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="other-ways-to-manage-and-reduce-costs"></a>コストを管理および削減するその他の方法

コストを削減するために使用できるベストプラクティスを一部紹介します。

* [プロビジョニング スループット コストの最適化](optimize-cost-throughput.md) - この記事では、スループット コストを最適化するためのベストプラクティスについて詳しく説明します。 ここでは、ワークロードの種類に基づいて、コンテナーレベルでスループットをプロビジョニングする場合と、データベースレベルでスループットをプロビジョニングする場合について説明します。

* [要求コストの最適化](optimize-cost-reads-writes.md) - この記事では、読み取りと書き込みの要求が要求ユニットに変換される方法と、これら要求のコストを最適化する方法について説明します。

* [ストレージ コストの最適化](optimize-cost-storage.md) - ストレージ コストは従量制で課金されます。 変更フィードや有効期間などの機能と、項目サイズ、インデックス作成ポリシーを使用してストレージ コストを最適化する方法について説明します。

* [マルチリージョンのコストの最適化](optimize-cost-regions.md) - 利用率が低い読み取りリージョンが 1 つ以上ある場合は、読み取りリージョンの変更フィードを使用する、または利用率が高い場合は別のセカンダリに移動することで、読み取りリージョンの RU を最大限活用するための手順を実行できます。

* [開発とテストのコストの最適化](optimize-dev-test.md) - ローカル エミュレーター、Azure Cosmos DB の Free サービス レベル、Azure 無料アカウント、およびその他のオプションを使用して、開発コストを最適化する方法について説明します。

* [予約容量によるコストの最適化](cosmos-db-reserved-capacity.md) - 予約容量を使用し、Azure Cosmos DB リソースに対する 1 年または 3 年間のコミットを予約してコストを削減する方法について説明します。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB での課金のしくみの詳細については、以下の記事を参照してください。

* [Azure Cosmos DB の価格モデル](how-pricing-works.md)
* [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
* [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
* [予期しないコストを回避](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
* [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。