---
title: Azure Cosmos DB のコストを計画および管理する
description: Azure portal でコスト分析を使用して、Azure Cosmos DB のコストを計画および管理する方法を説明します。
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152586"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure Cosmos DB のコストを計画および管理する

この記事では、Azure Cosmos DB のコストを計画および管理できる方法について説明します。 リソースを追加する前に、まず、コストの計画に役立つ Azure Cosmos DB Capacity Calculator を使用してください。 次に、Azure リソースを追加するときに推定コストを確認できます。 Azure Cosmos DB リソースの使用を開始したら、コスト管理機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処した方がよい領域を識別することができます。

Azure Cosmos DB のコストは、Azure の請求に記載された月額料金の一部でしかないことを理解してください。 Azure の他のサービスを使用している場合は、サードパーティのサービスを含め、お使いの Azure サブスクリプションで使用されている Azure のすべてのサービスとリソースに対して課金されます。 この記事では、Azure Cosmos DB のコストを計画および管理する方法について説明します。 Azure Cosmos DB のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理できます。

## <a name="prerequisites"></a>前提条件

コスト分析では、さまざまな種類の Azure アカウントがサポートされています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md)に関するページを参照してください。

## <a name="review-estimated-costs-with-capacity-calculator"></a>Capacity Calculator で推定コストを確認する

Azure Cosmos アカウント内にリソースを作成する前に、[Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) を使用してコストを見積もります。 Capacity Calculator は、ワークロードに必要なスループットとコストの見積もりを取得するために使用されます。 コストとパフォーマンスを最適化するうえで、ワークロードにとって適切な量のプロビジョニング済みスループット、つまり[要求ユニット (RU/秒)](request-units.md) を使用して Azure Cosmos データベースとコンテナーを構成することが不可欠です。 コスト見積もりを取得するには、API の種類、リージョンの数、項目のサイズ、1 秒あたりの読み取り/書き込み要求数、合計格納データなどの詳細を入力する必要があります。 Capacity Calculator の詳細については、[見積もり](estimate-ru-with-capacity-planner.md)に関する記事を参照してください。

次のスクリーンショットは、Capacity Calculator の利用によるスループットとコストの見積もりを示しています。

![Azure Cosmos DB Capacity Calculator でのコスト見積もり](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Azure portal から推定コストを確認する

Azure portal から Azure Cosmos DB リソースを作成するときに、推定コストを確認できます。 次のステップに従ってコスト見積もりを確認します。

1. Azure portal にサインインし、Azure Cosmos アカウントに移動します。
1. **Data Explorer** に移動します。
1. グラフ コンテナーなどの新しいコンテナーを作成します。
1. 400 RU/秒など、ワークロードのために必要なスループットを入力します。 スループット値を入力すると、次のスクリーンショットに示すように、料金見積もりを表示できます。

   ![Azure portal でのコスト見積り](./media/plan-manage-costs/cost-estimate-portal.png)

Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限の削除は可能です。 使用制限の詳細については、「[Azure の使用制限](../billing/billing-spending-limit.md)」を参照してください。

## <a name="use-budgets-and-cost-alerts"></a>予算とコストのアラートを使用する

[予算](../cost-management/tutorial-acm-create-budgets.md)を作成してコストを管理し、異常な支出や超過出費のリスクについて関係者に自動的に通知するアラートを作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 ただし、より高いレベルでコストを追跡するように設計されているため、Azure Cosmos DB のコストなど、個々の Azure サービスのコストを管理する機能が限定されていることがあります。

## <a name="monitor-costs"></a>コストを監視する

Azure Cosmos DB でリソースを使用するとコストが発生します。 リソース使用のユニット コストは、期間 (秒、分、時間、日) や要求ユニット使用量によって異なります。 Azure Cosmos DB の使用が始まるとすぐに、コストが発生して、Azure portal の[コスト分析](../cost-management/quick-acm-cost-analysis.md)のウィンドウでそれを参照することができます。

コスト分析を使用すると、さまざまな期間について、Azure Cosmos DB のコストをグラフや表で表示できます。 日単位、現在の分、以前の月、年単位などです。 予算や予想コストを基準としてコストを表示することもできます。 長期にわたるより長い期間のビューに切り替えると、支出の傾向を特定し、超過出費が発生した可能性のある時期を確認するのに役立ちます。 予算を作成した場合は、どこで予算を超過したかも容易に確認できます。コスト分析で Azure Cosmos DB コストを表示するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[コストの管理と請求]** ウィンドウを開き、メニューから **[Cost management] (コスト管理)** を選択してから、 **[コスト分析]** を選択します。 その後、特定のサブスクリプションのスコープを **[スコープ]** ドロップダウンから変更できます。

1. 既定では、最初のドーナツ グラフにすべてのサービスのコストが表示されます。 グラフ内の "Azure Cosmos DB" というラベルが付いた領域を選択します。

1. Azure Cosmos DB など、1 つのサービスのコストを絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、一覧から **[Azure Cosmos DB]** を選択します。 次に、Azure Cosmos DB だけのコストを示す例を示します。
 
   ![[コスト分析] ペインを使用してコストを監視する](./media/plan-manage-costs/cost-analysis-pane.png)

前の例では、2月について、Azure Cosmos DB の現在のコストが表示されています。グラフには、場所別とリソース グループ別の Azure Cosmos DB コストも含まれています。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB での課金のしくみの詳細については、以下の記事を参照してください。

* [Azure Cosmos DB の価格モデル](how-pricing-works.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](optimize-cost-queries.md)
* [Azure Cosmos DB でのストレージ コストを最適化する](optimize-cost-storage.md)