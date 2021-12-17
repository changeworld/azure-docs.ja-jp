---
title: Azure Data Factory のコストを管理するための計画
description: Azure portal のコスト分析を使用して、Azure Data Factory のコストを計画および管理する方法について説明します。
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.subservice: pricing
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 11/01/2021
ms.openlocfilehash: 51f71bcf39f0b54bf0f7fb389fbbb9797fbb2151
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469466"
---
# <a name="plan-to-manage-costs-for-azure-data-factory"></a>Azure Data Factory のコストを管理するための計画

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコストを計画および管理する方法について説明します。 

リソースをサービスに追加してコストを見積もる前に、まず、ETL プロジェクトの開始時に、Azure 料金計算ツールと、パイプラインごとの使用量および料金計算ツールを組み合わせて使用して、Azure Data Factory のコストを計画します。 次に、Azure リソースを追加するときに、推定コストを確認します。 Azure Data Factory リソースの使用を開始した後、コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Azure Data Factory のコストは、Azure で課金される月額料金の一部でしかありません。 この記事では、データ ファクトリのコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。

## <a name="prerequisites"></a>[前提条件]

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="estimate-costs-before-using-azure-data-factory"></a>Azure Data Factory を使用する前にコストを見積もる
 
[ADF 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=data-factory)を使用して、Azure Data Factory で ETL ワークロードを実行するコストを見積もります。  計算ツールを使用するには、アクティビティの実行回数、データ統合単位時間数、Data Flow に使用されるコンピューティングの種類、コア数、インスタンス数、実行期間などの詳細を入力する必要があります。

料金計算ツールについてよくある質問の 1 つが、どの値を入力として使用する必要があるかというものです。  概念実証フェーズでは、サンプル データセットを使用した試験を行って、さまざまな ADF メーターの使用量を把握できます。  続いて、サンプル データセットの使用量に基づいて、完全なデータセットおよび運用化スケジュールの使用量を算定できます。

> [!NOTE]
> 次の例で使用されている価格は仮定的なものであり、実際の価格を暗示することを意図するものではありません。

たとえば、毎日 1 TB のデータを AWS S3 から Azure Data Lake Gen2 に移動する必要があるとします。  100 GB のデータを移動して、データ インジェストのスループットを測定し、それに応じて課金される使用料金を測定するという概念実証を実行できます。

コピー アクティビティの実行の詳細のサンプルを次に示します (実際のマイレージは、特定のデータセットの形状、ネットワーク速度、S3 アカウントの送信制限、ADLS Gen2 の受信制限、その他の要因によって異なります)。

:::image type="content" source="media/plan-manage-costs/s3-copy-run-details.png" alt-text="S3 コピーの実行":::

[パイプライン実行レベルでの使用量の監視](#monitor-consumption-at-pipeline-run-level)を利用することで、対応するデータ移動メーター使用量を確認できます。

:::image type="content" source="media/plan-manage-costs/s3-copy-pipeline-consumption.png" alt-text="S3 コピー パイプラインの使用量":::

そのため、1 日に 1 TB を移動するために必要な DIU 時間の 1 か月の合計は次のようになります。

1.2667 (DIU 時間数) * (1 TB / 100 GB) * 30 (1 か月の日数) = 380 DIU 時間

次に、30 回のアクティビティの実行と 380 DIU 時間を、ADF 料金計算ツールに入力し、月額の見積もりを得ることができます。

:::image type="content" source="media/plan-manage-costs/s3-copy-pricing-calculator.png" alt-text="S3 コピー料金計算ツール":::

## <a name="understand-the-full-billing-model-for-azure-data-factory"></a>Azure Data Factory の詳細な課金モデルを理解する

Azure Data Factory は、新しいリソースをデプロイする際にコストが発生する Azure インフラストラクチャ上で実行されます。 その他のインフラストラクチャ コストが追加で発生する可能性があることを理解しておくことが重要です。

### <a name="how-youre-charged-for-azure-data-factory"></a>Azure Data Factory に対する課金方法

Azure Data Factory は、クラウド規模用に構築されたサーバーレスのエラスティック データ統合サービスです。  これは、ピーク時の負荷に対して計画する必要がある固定サイズのコンピューティングがないことを意味します。代わりに、必要に応じて操作ごとに割り当てるリソースの量を指定します。これにより、非常にスケーラブルに ETL プロセスを設計できます。 さらに、ADF は使用量ベースのプランに対して課金されます。これは、使用した分だけを支払うことを意味します。

Azure Data Factory リソースを作成または使用すると、次の測定に基づいて課金される場合があります。

- オーケストレーション アクティビティの実行 - アクティビティでオーケストレーションを実行する回数に基づいて課金されます。
- データ統合単位 (DIU) 時間 – Azure Integration Runtime で実行されるコピー アクティビティについて、DIU 使用数と実行時間に基づいて課金されます。
- 仮想コア時間 – データ フローの実行とデバッグについて、コンピューティングの種類、仮想コアの数、実行時間に基づいて課金されます。


請求期間終了時に、各測定の料金が合計されます。 請求書には、Azure Data Factory に対して発生したすべてのコストに関するセクションが表示されています。 測定ごとに個別の行項目があります。

### <a name="other-costs-that-might-accrue-with-azure-data-factory"></a>Azure Data Factory で発生する可能性があるその他のコスト

Azure Data Factory (ADF) のリソースを作成すると、他の Azure サービスのリソースも作成されます。 これには次のようなものがあります。

- パイプライン アクティビティの実行
- 外部パイプライン アクティビティの実行
- データ ファクトリ成果物の作成/編集/取得/監視
- インスタンスの種類と期間に基づいた SSIS Integration Runtime (IR) の期間

> [!NOTE]
> ADF とその他の Azure リソースに同じタグを割り当てて、それらを同じカテゴリにまとめ、統合された課金を表示できます。 ADF タグは、その中のすべての SSIS IR に継承されます。 ADF タグを変更する場合は、新しいタグを継承できるようにその中のすべての SSIS IR を停止して再起動する必要があります。[SSIS IR の再構成](manage-azure-ssis-integration-runtime.md#to-reconfigure-an-azure-ssis-ir)に関するセクションを参照してください。

### <a name="using-azure-prepayment-with-azure-data-factory"></a>Azure Data Factory での Azure 前払いの使用

Azure Data Factory の料金は、Azure 前払いのクレジットで支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="monitor-costs"></a>コストを監視する

Azure Data Factory のコストは、ファクトリ、パイプライン実行、アクティビティ実行の各レベルで監視できます。

### <a name="monitor-costs-at-factory-level"></a>ファクトリ レベルでコストを監視する

Data Factory で Azure リソースを使用する場合、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Data Factory の使用が開始されるとすぐにコストが発生し、[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、Data Factory のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

コスト分析で Data Factory のコストを表示するには、次の手順を実行します。

1. Azure portal にサインインします。
2. Azure portal でスコープを開き、メニューから **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。
3. 既定では、最初のドーナツ グラフにサービスのコストが表示されます。 グラフの [Azure Data Factory v2] というラベルが付いた領域を選択します。

最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

:::image type="content" source="media/all-costs.png" alt-text="サブスクリプションの累積コストを示す例":::

- Data Factory などの 1 つのサービスのコストに絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[Azure Data Factory v2]** を選択します。

Data Factory のみのコストを表示する例を次に示します。

:::image type="content" source="media/service-specific-cost.png" alt-text="ServiceName の累積コストを示す例":::

前の例では、サービスの現在のコストが表示されます。 Azure リージョン (場所) 別およびリソース グループ別の Data Factory のコストも表示されます。 ここでは、コストを自分で調べることができます。

### <a name="monitor-consumption-at-pipeline-run-level"></a>パイプライン実行レベルでの使用量を監視する

パイプラインに含まれているアクティビティの種類、移動および変換しているデータの量、変換の複雑さに応じて、パイプラインを実行すると Azure Data Factory でのさまざまな課金メーターが動作します。

個々のパイプラインの実行に関するさまざまなメーターの使用量は、Azure Data Factory のユーザー エクスペリエンスで確認できます。 監視エクスペリエンスを開くには、[Azure portal](https://portal.azure.com/) の [データ ファクトリ] ブレードで **[監視と管理]** タイルを選択します。 ADF UX を既に使用している場合は、左側のサイドバーにある **[監視]** アイコンをクリックします。 既定の監視ビューはパイプラインの実行の一覧です。

パイプライン名の横にある **[消費]** ボタンをクリックすると、パイプライン内のすべてのアクティビティに対して集計されたパイプライン実行の使用量を示すポップアップ ウィンドウが表示されます。

:::image type="content" source="media/plan-manage-costs/pipeline-run-consumption.png" alt-text="パイプライン実行の使用量":::

:::image type="content" source="media/plan-manage-costs/pipeline-consumption-details.png" alt-text="パイプライン使用量の詳細":::

[pipeline run consumption]\(パイプラインの実行使用量\) ビューには、特定のパイプライン実行の ADF メーターごとに消費された量は表示されますが、課金される実際の料金は表示されません。課金される金額は、所有している Azure アカウントの種類と使用されている通貨の種類によって異なります。  サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md)」を参照してください。

### <a name="monitor-consumption-at-activity-run-level"></a>アクティビティ実行レベルでの使用量を監視する
パイプライン実行レベルでの集計された使用量を把握した後、シナリオによっては、さらにドリルダウンして、パイプライン内で最もコストのかかるアクティビティを特定する必要があります。

アクティビティ実行レベルでの使用量を表示するには、データ ファクトリの **[作成と監視]** UI に移動します。 パイプライン実行の一覧が表示される **[監視]** タブから、**パイプライン名** のリンクをクリックして、パイプライン実行でのアクティビティの実行の一覧にアクセスします。  アクティビティ名の横にある **[出力]** ボタンをクリックし、JSON 出力で **billableDuration** プロパティを探します。

コピー アクティビティの実行のサンプルを次に示します。

:::image type="content" source="media/plan-manage-costs/copy-output.png" alt-text="出力をコピーする":::

マッピング データ フロー アクティビティの実行のサンプルを次に示します。

:::image type="content" source="media/plan-manage-costs/dataflow-output.png" alt-text="データフローの出力":::

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときに使用可能なフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
- [Azure Data Factory の価格ページ](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Azure Data Factory を実例から理解する](./pricing-concepts.md)
- [Azure Data Factory の領域計算ツール](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
