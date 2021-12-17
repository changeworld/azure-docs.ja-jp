---
title: Azure Synapse Analytics のコストの管理を計画する
description: Azure portal でコスト分析を使用して、Azure Synapse Analytics のコストを計画および管理する方法について説明します。
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: bd1e55c216a314bfc6e132979d5a4999d4f363eb
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850624"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Azure Synapse Analytics のコストを計画して管理する

この記事では、Azure Synapse Analytics のコストを計画して管理する方法について説明します。 コストを見積もるサービスに対してリソースを追加する前に、まず、Azure Synapse Analytics のコストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 Azure Synapse Analytics リソースの使用を開始した後、コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Azure Synapse Analytics のコストは、Azure で課金される月額料金の一部でしかありません。 この記事では、Azure Synapse Analytics のコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。

## <a name="prerequisites"></a>前提条件

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用する前にコストを見積もる

Azure Synapse Analytics を追加する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。

Azure Synapse には、次のコストの見積もりで確認できるように、料金が異なるさまざまなリソースがあります。 

![Azure 料金計算ツールの推定コストを示す例](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Azure Synapse Analytics の詳細な課金モデルを理解する

Azure Synapse は、新しいリソースをデプロイすると Azure Synapse と併せてコストが発生する Azure インフラストラクチャ上で実行されます。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。 

### <a name="how-youre-charged-for-azure-synapse-analytics"></a>Azure Synapse Analytics に対する課金方法

Azure Synapse Analytics リソースを作成または使用すると、次の測定に基づいて課金される場合があります。

- データ探索とデータ ウェアハウス 
    - 専用 SQL プール - DWU ブロックの数と実行時間に基づいて課金されます。
    - ストレージ - 格納されている TB の数に基づいて課金されます。
    - サーバーレス SQL プール - 処理済みデータの TB によって課金されます。
- Apache Spark プール - インスタンスの数と実行時間に対して課金されます。
- データ統合 
    - オーケストレーション アクティビティの実行 - アクティビティの実行回数に基づいて課金されます。
    - データ移動 - Azure Integration Runtime で実行されるコピー アクティビティについて、DIU 使用数と実行時間に基づいて課金されます。
    - Data Flow 仮想コア時間 - データ フローの実行とデバッグについて、コンピューティングの種類、仮想コアの数、実行時間に基づいて課金されます。

請求期間終了時に、各測定の料金が合計されます。 請求書には、Azure Synapse Analytics に対して発生したすべてのコストに関するセクションが表示されています。 測定ごとに個別の行項目があります。

### <a name="other-costs-that-might-accrue-with-azure-synapse-analytics"></a>Azure Synapse Analytics で発生する可能性がある他のコスト

Azure Synapse のリソースを作成すると、他の Azure サービスのリソースも作成されます。 具体的な内容を次に示します。

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>リソースの削除後にコストが発生する可能性がある

Azure Synapse リソースを削除しても、次のリソースが引き続き存在する可能性があります。 これらを削除するまで、これらのコストは発生し続けます。

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure Synapse で Azure 前払いクレジットを使用する 

Azure Synapse Analytics の料金は、Azure 前払いのクレジットで支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

### <a name="pre-purchase-plan-for-azure-synapse"></a>Azure Synapse の事前購入プラン

Azure Synapse コミット ユニット (SCU) を 1 年間事前購入すると、Azure Synapse Analytics のコストを節約できます。 事前購入済みの SCU は、購入期間中はいつでも使用できます。 詳しくは、「[事前購入プランを使用して Azure Synapse Analytics のコストを最適化する](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md)」を参照してください


## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

Azure Synapse Analytics のリソースを作成すると、推定コストが表示されます。 ワークスペースには、ワークスペースと共に作成されたサーバーレス SQL プールがあります。 サーバーレス SQL プールでは、クエリを実行するまで料金は発生しません。 ワークスペース内に、専用 SQL プールやサーバーレス Apache Spark プールなどのその他のリソースを作成する必要があります。

Azure Synapse Analytics ワークスペースを作成し、推定価格を表示するには:

1. Azure portal でサービスに移動します。
2. リソースを作成します。
3. 概要に表示される推定料金を確認します。
4. リソースの作成を完了します。

![リソース作成時の推定コストの表示例](./media/plan-manage-costs/create-workspace-cost.png)


Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限を取り除くことは可能です。 使用制限の詳細については、「[Azure の使用制限](../cost-management-billing/manage/spending-limit.md)」を参照してください。

## <a name="monitor-costs"></a>コストを監視する

Azure Synapse リソースを使用すると、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Synapse でリソースの使用を開始すると、すぐにコストが発生します。[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でそのコストを確認できます。

コスト分析を使用すると、異なる時間間隔の Azure Synapse Analytics のコストを、グラフと表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

コスト分析で Aure Synapse のコストを表示するには:

1. Azure portal にサインインします。
2. Azure portal で、サブスクリプションまたはリソース グループのスコープを開き、メニューの **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。
3. 既定では、最初のドーナツ グラフにサービスのコストが表示されます。 グラフの [Azure Synapse] というラベルが付いた領域を選択します。

最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

![サブスクリプションの累積コストを示す例](./media/plan-manage-costs/actual-monthly-costs.png)

- Azure Synapse などの 1 つのサービスのコストに絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[Azure Synapse Analytics]** を選択します。

Azure Synapse のみのコストの表示例を次に示します。

![ServiceName の累積コストを示す例](./media/plan-manage-costs/filtered-monthly-costs.png)

前の例では、サービスの現在のコストが表示されます。 Azure リージョン (場所) 別およびリソース グループ別の Azure Synapse のコストも表示されます。 ここでは、コストを自分で調べることができます。

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときに使用可能なフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Azure Synapse のコストを管理して削減するその他の方法 

### <a name="serverless-sql-pool"></a>サーバーレス SQL プール

サーバーレス SQL プールのコストの詳細については、「[Azure Synapse Analytics でのサーバーレス SQL プールのコスト管理](./sql/data-processed.md)」を参照してください。

### <a name="dedicated-sql-pool"></a>専用 SQL プール

リソースが使用されていないときに一時停止することで、専用 SQL プールのコストを制御できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 詳細については、「[Azure portal を使用して専用 SQL プールのコンピューティングを一時停止および再開する](./sql-data-warehouse/pause-and-resume-compute-portal.md?context=/azure/synapse-analytics/context/context)」を参照してください

### <a name="serverless-apache-spark-pool"></a>サーバーレス Apache Spark プール

サーバーレス Apache Spark プールのコストを制御するには、サーバーレス Apache Spark 自動一時停止機能を有効にし、必要に応じてタイムアウト値を設定します。  開発に Synapse Studio を使用する場合、セッションをアクティブなままにするために、Studio によりキープ アライブ メッセージが送信されます。これも構成可能なため、自動一時停止の短いタイムアウト値を設定します。  終了すると、セッションが閉じ、タイムアウト値に達すると Apache Spark プールが自動的に一時停止します。
 
開発時には、さまざまなサイズの複数の Apache Spark プール定義を作成します。  Apache Spark プール定義の作成は無料であり、使用量に対してのみ課金されます。  Azure Synapse での Apache Spark 使用量は、仮想コア時間単位で課金され、分単位で比例配分されます。  たとえば、コードの開発と検証には小さいプール サイズを使用し、パフォーマンス テストにはより大きなプール サイズを使用します。

### <a name="data-integration---pipelines-and-data-flows"></a>データ統合 - パイプラインとデータ フロー 

データ統合のコストの詳細については、[Azure Data Factory のコストの計画と管理](../data-factory/plan-manage-costs.md)に関するページをご覧ください

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
- [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md) のコストの計画と管理について確認します。
