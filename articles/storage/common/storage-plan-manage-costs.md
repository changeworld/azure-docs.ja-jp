---
title: Azure Blob Storage のコストを計画および管理する
description: Azure portal でコスト分析を使用して、Azure Blob Storage のコストを計画および管理する方法を学習します。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 8cb7f667f2898d1508df2f58a3aea2ced68582e1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474801"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Azure Blob Storage のコストを計画および管理する

この記事は、Azure Blob Storage のコストを計画および管理するのに役立ちます。 まず、Azure 料金計算ツールを使用して、コストを見積もります。 ストレージ アカウントを作成したら、必要な分だけ支払うようにアカウントを最適化します。 コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予測コストを確認し、支出の傾向を監視して、対処した方がよい領域を識別することもできます。

Blob Storage のコストは、Azure の請求にある月額料金の一部でしかないことにご注意ください。 この記事では、Blob Storage のコストを見積もり、管理する方法について説明しますが、サード パーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。 Blob Storage のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理できます。

## <a name="estimate-costs"></a>コストの見積もり

Azure Storage アカウントを作成し、データの転送を開始する前にコストを見積もるには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/) を使用します。

1. [[Azure 料金計算ツール]](https://azure.microsoft.com/pricing/calculator/) ページで、 **[ストレージ アカウント]** タイルを選択します。

2. ページを下方向にスクロールして、見積りの **[ストレージ アカウント]** セクションを見つけます。

3. ドロップダウン リストからオプションを選択します。

   これらのドロップダウン リストの値を変更すると、コストの見積り額が変化します。 その見積り額は、上部の隅と見積りの下部に表示されます。

   ![見積りを示すスクリーンショット](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **[種類]** ドロップダウン リストの値を変更すると、このワークシートに表示されている他のオプションも変更されます。 **[詳細]** セクションのリンクを使用して、各オプションの意味とこれらのオプションがストレージ関連の操作の価格にどのように影響するかを確認します。

4. 残りのオプションを変更して、それらが見積もりに与える影響を確認します。

## <a name="understand-the-full-billing-model-for-azure-blob-storage"></a>Azure Blob Storage の詳細な課金モデルを理解する

Azure Blob Storage は、新しいリソースをデプロイする際にコストが発生する Azure インフラストラクチャ上で実行されます。 その他のインフラストラクチャ コストが追加で発生する可能性があることを理解しておくことが重要です。

### <a name="how-youre-charged-for-azure-blob-storage"></a>Azure Blob Storage の料金のしくみ

Blob Storage リソースを作成または使用すると、以下のように料金が発生します。

| 測定 | 単位 |
|---|---|
| データ ストレージ | GB/月|
| Operations | トランザクションあたり |
| データ転送 | GB あたり |
| メタデータ | GB あたり/1 か月あたり<sup>1 |
| BLOB インデックス タグ | タグあたり<sup>2  |
| Change Feed | ログに記録された変更 1 件あたり<sup>2 |
| 暗号化スコープ | 1 か月あたり<sup>2 |
| クエリの高速化 | スキャン 1 GB あたり & 返された結果 1 GB あたり |

<sup>1</sup> 階層型名前空間があるアカウントにのみ適用されます。<br />
<sup>2</sup> この機能を有効にした場合のみ適用されます。<br />

このほか、データ トラフィックによりネットワーク関連のコストが発生する可能性があります。 「[帯域幅の価格](https://azure.microsoft.com/pricing/details/data-transfers/)」を参照してください。

請求期間終了時に、各測定の料金が合計されます。 請求書には、Azure Blob Storage に関して発生したすべてのコストが 1 つのセクションに表示されます。 測定ごとに個別の行項目があります。

データ ストレージとメタデータは、1 か月ごとに GB 単位で課金されます。 格納期間が 1 か月未満のデータとメタデータについては、1 日あたりの GB 数のコストを計算することによって、月ごとの請求額に対する影響を試算することができます。 使用する期間が 1 か月未満の暗号化スコープのコストを試算する際にも、これとほぼ同じアプローチが使えます。 日数は月ごとに異なります。 そのため、月額のコストを月ごとの日数で割るようにすると、その月のコストを最大限正確に見積もることができます。

### <a name="finding-the-unit-price-for-each-meter"></a>各測定の単価を把握する

単価を確認するには、正しいページを開く必要があります。 アカウントで階層型名前空間機能を有効にしている場合には、[Azure Data Lake Storage Gen2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)に関するページを参照してください。 この機能を有効にしていない場合には、[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。

価格ページでは、冗長性、リージョン、通貨に適切なフィルターを設定してください。 各測定の価格が、表形式で表示されます。 価格は、アカウントの他の設定 (データの冗長性オプション、アクセス層、パフォーマンス レベルなど) に応じて異なります。

### <a name="flat-namespace-accounts-and-transaction-pricing"></a>フラット型名前空間アカウントとトランザクションの価格

クライアントでは、アカウントの Blob Storage エンドポイントと Data Lake Storage エンドポイントのいずれかを使用して要求を実行できます。 ストレージ アカウント エンドポイントの詳細については、「[ストレージ アカウント エンドポイント](storage-account-overview.md#storage-account-endpoints)」を参照してください。

[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページに記載されているトランザクション価格は、Blob Storage エンドポイント (例: `https://<storage-account>.blob.core.windows.net`) を使用している要求にのみ適用されます。 一覧に記載されている価格は、Data Lake Storage Gen2 エンドポイント (例: `https://<storage-account>.dfs.core.windows.net`) を使用している要求には適用されません。 そのような要求のトランザクションの価格については、[Azure Data Lake Storage Gen2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)に関するページを開き、**フラット型名前空間** のオプションを選択してください。

> [!div class="mx-imgBorder"]
> ![フラット型名前空間のオプション](media/storage-plan-manage-costs/select-flat-namespace.png)

Data Lake Storage Gen2 エンドポイントを対象とした要求を実行できるソースは次のとおりです。

- Azure Blob File System ドライバー ([ABFS ドライバー](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)) を使用するワークロード。

- [Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2) を使用する REST 呼び出し

- Azure Storage クライアント ライブラリから Data Lake Storage Gen2 API を使用するアプリケーション。

### <a name="using-azure-prepayment-with-azure-blob-storage"></a>Azure Blob Storage で Azure 前払いを使用する

Azure Blob Storage の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="optimize-costs"></a>コストを最適化する

コストを削減するには、これらのオプションを使用することを検討してください。

- ストレージ容量を予約する

- データをアクセス層にまとめる

- アクセス層間でデータを自動的に移動する

このセクションでは、各オプションについて詳しく説明します。

#### <a name="reserve-storage-capacity"></a>ストレージ容量を予約する

Azure Storage の予約容量を使用して BLOB データのストレージ コストを節約できます。 Azure Storage の予約容量により、1 年間または 3 年間の予約を行うと、Standard ストレージ アカウントのブロック BLOB および Azure Data Lake Storage Gen2 データの容量を割引できます。 予約を行うと、予約期間に一定量のストレージ容量が提供されます。 Azure Storage の予約容量により、ブロック BLOB と Azure Data Lake Storage Gen2 データの容量コストを大幅に削減できます。

詳細については、「[予約容量を使用して BLOB ストレージのコストを最適化する](../blobs/storage-blob-reserved-capacity.md)」を参照してください。

#### <a name="organize-data-into-access-tiers"></a>データをアクセス層にまとめる

コスト効率に優れたアクセス層に BLOB データを配置することで、コストを削減できます。 データの使用に関するコストを最適化するように設計された 3 つの層から選択します。 たとえば、"*ホット*" 層のストレージ コストは高くなりますが、アクセス コストは低くなります。 そのため、データに頻繁にアクセスする場合は、ホット層が最もコスト効率の高い選択肢になる可能性があります。 データにアクセスする頻度が少ない場合は、データを格納するコストを削減すると同時に、データにアクセスするコストが発生するため、"*コールド*" または "*アーカイブ*" 層が最も意味がある可能性があります。

詳細については、「[BLOB データのホット、クールおよびアーカイブ アクセス層](../blobs/access-tiers-overview.md?tabs=azure-portal)」を参照してください。

#### <a name="automatically-move-data-between-access-tiers"></a>アクセス層間でデータを自動的に移動する

ライフサイクル管理ポリシーを使用して、コストを最も節約するために定期的に階層間でデータを移動します。 これらのポリシーによって、指定したルールを使用してデータをに移動できます。 たとえば、BLOB が 90 日以内に変更されていない場合、BLOB をアーカイブ層に移動するルールを作成することができます。 データのアクセス層を調整するポリシーを作成することによって、ニーズに最もコストのかからないストレージ オプションを設計できます。

詳細については、[Azure Blob Storage のライフサイクルの管理](../blobs/lifecycle-management-overview.md?tabs=azure-portal)に関するページを参照してください

## <a name="create-budgets"></a>予算を作成する

[予算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成してコストを管理し、異常な支出や超過出費のリスクについて関係者に自動的に通知するアラートを作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 ただし、これらはより高いレベルでコストを追跡するように設計されているため、Azure Storage のコストなど、個々の Azure サービスのコストを管理する機能が限定されていることがあります。

## <a name="monitor-costs"></a>コストを監視する

Azure Storage で Azure リソースを使用する場合、コストが発生します。 リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Storage の使用を開始するとただちにコストが発生します。 コストは、Azure portal の [[コスト分析]](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ペインで確認できます。

コスト分析を使用すると、さまざまな期間について、Azure Storage のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 長期にわたるより長い期間のビューに切り替えると、支出の傾向を特定し、超過出費が発生した可能性のある時期を確認するのに役立ちます。 予算を作成したら、それを超えた場合を簡単に確認できます。

> [!NOTE]
> コスト分析では、さまざまな種類の Azure アカウントがサポートされています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

Azure Storage のコストをコスト分析で表示するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** ウィンドウを開き、メニューから **[Cost management] (コスト管理)** を選択してから、 **[コスト分析]** を選択します。 その後、特定のサブスクリプションのスコープを **[スコープ]** ドロップダウンから変更できます。

   ![スコープを示すスクリーンショット](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure Storage のコストのみを表示するには、 **[フィルターの追加]** 、 **[サービス名]** の順に選択します。 次に、リストから **[ストレージ]** を選択します。

   Azure Storage のみのコストを表示する例を次に示します。

   ![ストレージによるフィルターを示すスクリーンショット](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

前の例では、サービスの現在のコストが表示されます。 また、Azure リージョン (場所) 別およびリソース グループ別のコストも表示されます。 他のフィルターを追加することもできます (例: 特定のストレージ アカウントのコストを表示するフィルター)。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="faq"></a>よく寄せられる質問

**Azure Storage を 1 か月間に数日しか使わなかった場合には、コストが日割り計算になるのでしょうか?**

ストレージ容量は、1 か月間の格納データの総量から 1 日あたりの平均量 (ギガバイト単位) を算出し、それを基準に請求されます。 たとえば、ストレージを月の前半には 10 GB 使用し、後半には一切使用しなかった場合には、平均使用量 5 GB として請求されます。

## <a name="next-steps"></a>次のステップ

- Azure Storage での価格のしくみについて詳しく説明します。 「[Azure Storage の価格の概要](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。
- [予約容量を使用して Blob Storage のコストを最適化する](../blobs/storage-blob-reserved-capacity.md)。
- [Azure Cost Management を使用してクラウドへの投資を最適化する](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
