---
title: Microsoft Sentinel のコストを計画して管理する
description: Azure portal のコスト分析および他の方法を使用して、Microsoft Sentinel のコストと請求を計画、把握、管理する方法について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.custom: subject-cost-optimization, ignite-fall-2021
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: fd74a320633e1858b46b7ffbafee8df6449bf0c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725445"
---
# <a name="plan-and-manage-costs-for-microsoft-sentinel"></a>Microsoft Sentinel のコストを計画して管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel のコストを計画して管理する方法について説明します。 サービスにリソースを追加する前に、まず、Microsoft Sentinel のコストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。

Microsoft Sentinel のリソースを使い始めた後、Cost Management の機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。この記事では、Microsoft Sentinel のコストを管理して最適化するためのいくつかの方法について説明します。

Microsoft Sentinel のコストは、Azure で課金される月額料金の一部でしかありません。 この記事では、Microsoft Sentinel のコストを計画して管理する方法について説明しますが、パートナーのサービスを含め、課金は、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して行われます。

## <a name="prerequisites"></a>前提条件

- Cost Management でコスト データを表示し、コスト分析を実行するには、サポートされている種類の Azure アカウントと、少なくとも読み取りアクセス権が必要です。

    Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされていますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。

    Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

- データ ソースに関する詳細が必要です。 Microsoft Sentinel を使用すると、1 つ以上のデータ ソースからデータを取り込むことができます。 これらのデータ ソースには、無料のものと、料金が発生するものがあります。 詳細については、「[無料データ ソース](#free-data-sources)」を参照してください。

## <a name="estimate-costs-before-using-microsoft-sentinel"></a>Microsoft Sentinel を使用する前にコストを見積もる

Microsoft Sentinel をまだ使用していない場合は、[Microsoft Sentinel 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel)を使用して、予想コストを見積もることができます。 検索ボックスに「*Microsoft Sentinel*」と入力し、結果の Microsoft Sentinel タイルを選択します。 料金計算ツールを使用すると、予想されるデータの取り込みと保持期間に基づいて、考え得るコストを見積もることができます。

たとえば、Microsoft Sentinel に取り込むことが予想される 1 日当たりの GB 数と、ワークスペースのリージョンを入力できます。 計算ツールにより、これらのコンポーネント全体の 1 か月の総コストが提供されます。

- Log Analytics のデータ インジェスト
- Microsoft Sentinel のデータ分析
- Log Analytics のデータ保持

> [!NOTE]
> この画像で示されているコストは、例示だけを目的としたものです。 実際のコストを反映したものではありません。

:::image type="content" source="media/billing/pricing-calculator.png" alt-text="Azure 料金計算ツールでの推定コストを示す例のスクリーンショット。" lightbox="media/billing/pricing-calculator.png" :::

## <a name="understand-the-full-billing-model-for-microsoft-sentinel"></a>Microsoft Sentinel の詳細な課金モデルを理解する

Microsoft Sentinel には、柔軟で予測可能な価格モデルが用意されています。 詳細については、[Microsoft Sentinel の価格ページ](https://azure.microsoft.com/pricing/details/azure-sentinel/)を参照してください。 関連する Log Analytics の料金については、[Azure Monitor Log Analytics の価格](https://azure.microsoft.com/pricing/details/log-analytics/)に関する記事を参照してください。

Microsoft Sentinel は、新しいリソースをデプロイするときにコストが発生する Azure インフラストラクチャ上で実行されます。 その他のインフラストラクチャ コストが追加で発生する可能性があることを理解しておくことが重要です。

### <a name="how-youre-charged-for-microsoft-sentinel"></a>Microsoft Sentinel での課金方法

Microsoft Sentinel サービスには、**従量課金制レベル** と **コミットメント レベル** という 2 つの支払い方法があります。

- **従量課金制** は既定のモデルであり、、格納されている実際のデータ量に基づき、必要に応じて 90 日より長くデータを保持できます。 データ ボリュームは GB 単位 (10^9 バイト) で測定されます。

- Log Analytics と Microsoft Sentinel には **コミットメント レベル** の価格 (旧称、容量予約) も用意されています。これは、予測可能であり、従量課金制の価格と比較して最大で 65% も節約されます。

    コミットメント レベルの価格では、100 GB/日からコミットメントを購入できます。 コミットメント レベルを超える使用量には、お客様が選択したコミットメント レベル レートで課金されます。 たとえば、コミットメント レベルが 100 GB の場合、コミットされた 100 GB のデータ量に加えて、そのレベルの割引料金で GB/日の追加分が請求されます。

    コミットメント レベルは、増やすのはいつでも、減らすのは 31 日ごとに可能で、データ量の増減に合わせてコストを最適化できます。 Microsoft Sentinel の現在の価格レベルを確認するには、Microsoft Sentinel の左側のナビゲーションで **[設定]** を選択してから、 **[価格]** タブを選択します。現在の価格レベルには、 **[現在のレベル]** と表示されます。

    コミットメント レベルの設定と変更については、「[価格レベルを設定または変更する](#set-or-change-pricing-tier)」を参照してください。

### <a name="understand-your-microsoft-sentinel-bill"></a>Microsoft Sentinel の課金内容を確認する

課金対象の測定は、請求書に記載されるサービスの個々のコンポーネントであり、サービスのコスト分析にも表示されます。 請求期間終了時に、各測定の料金が合計されます。 請求書では、Microsoft Sentinel のすべてのコストに関するセクションが示されます。 測定ごとに個別の行項目があります。

Azure の請求書を表示するには、**Cost Management + Billing** の左側のナビゲーションで **[コスト分析]** を選択します。 **[コスト分析]** 画面で、 **[表示]** フィールドのドロップダウン キャレットを選択し、 **[請求書の詳細]** を選択します。

> [!NOTE]
> この画像で示されているコストは、例示だけを目的としたものです。 実際のコストを反映したものではありません。

![サンプルの Azure 請求書の Microsoft Sentinel セクションを示すスクリーンショット。](media/billing/sample-bill.png)

Azure の請求書には、Microsoft Sentinel と Log Analytics の料金が、選択されている価格プランに基づく個別の明細項目として表示されます。 特定の月についてワークスペースのコミットメント レベルの使用量を超過した場合、Azure の請求書には、コミットメント レベルとそれに関連する固定費が示された 1 つの明細項目と、コミットメント レベルを超えたインジェストについて、同じコミットメント レベルの料金で請求された別の明細項目が表示されます。

次の表に、Azure 請求書の **[サービス名]** 列と **[測定]** 列に Microsoft Sentinel と Log Analytics のコストがどのように表示されるかを示します。

| [コスト] | [サービス名] | 測定 |
|--|--|--|
| Microsoft Sentinel コミットメント レベル | `sentinel` | **`n` gb コミットメント レベル** |
| Log Analytics コミットメント レベル | `azure monitor` | **`n` gb コミットメント レベル** |
| Microsoft Sentinel のコミットメント レベル超過分または従量課金制| `sentinel` |**analysis**|
| Log Analytics のコミットメント レベル超過分または従量課金制| `log analytics` |**data ingestion**|

Azure 請求書の表示とダウンロードの詳細については、[Azure のコストと課金の情報](../cost-management-billing/understand/download-azure-daily-usage.md)に関する記事を参照してください。

### <a name="costs-for-other-services"></a>その他のサービスのコスト

Microsoft Sentinel は、強化された機能を提供するため、他の多くの Azure サービスと統合されます。 これらのサービスには、Azure Logic Apps、Azure Notebooks、機械学習持ち込み (BYOML) モデルが含まれます。 一部のサービスには追加料金がかかる場合があります。 Microsoft Sentinel のデータ コネクタとソリューションの一部では、データ インジェストに Azure Functions が使用され、それに関連して別途コストがかかります。

これらのサービスの価格の詳細については、以下を参照してください。

- [Automation Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Notebooks の価格](https://azure.microsoft.com/pricing/details/machine-learning/)
- [BYOML の価格](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)

使用するその他のサービスで、関連するコストが発生する可能性があります。

### <a name="data-retention-costs"></a>データ保持コスト

Log Analytics ワークスペースで Microsoft Sentinel を有効にすると、ワークスペースに取り込まれたすべてのデータを、最初の 90 日間は無料で保持できます。 90 日を超えて保持すると、標準の [Log Analytics 保持価格](https://azure.microsoft.com/pricing/details/monitor/)に従って課金されます。

個々のデータの種類に異なる保持設定を指定できます。 詳細については、「[データの種類別のリテンション期間](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type)」を参照してください。

### <a name="other-cef-ingestion-costs"></a>他の CEF インジェスト コスト

CEF は、Microsoft Sentinel でサポートされている Syslog イベント形式です。 CEF を使用して、さまざまなソースから Microsoft Sentinel ワークスペースに重要なセキュリティ情報を取り込むことができます。 CEF ログは Microsoft Sentinel の CommonSecurityLog テーブルに格納され、これにはすべての標準的な最新の CEF フィールドが含まれます。

多くのデバイスとデータ ソースでは、標準の CEF スキーマを超えるフィールドのログを記録できます。 これらの追加フィールドは、AdditionalExtensions テーブルに格納されます。 これらのフィールド内のイベント コンテンツは可変でもかまわないため、これらのフィールドは標準の CEF フィールドよりインジェスト量が多くなる可能性があります。

### <a name="costs-that-might-accrue-after-resource-deletion"></a>リソースの削除後に発生する可能性があるコスト

Microsoft Sentinel を削除しても、Microsoft Sentinel がデプロイされた Log Analytics ワークスペース、またはそのワークスペースで発生した可能性がある個別の料金は削除されません。

### <a name="free-trial"></a>無料試用版

最初の 31 日間無料で Microsoft Sentinel をお試しください。 Microsoft Sentinel は、Azure Monitor Log Analytics ワークスペースで、以下に示す制限に従って、追加コストなしで有効にできます。

- **新しい Log Analytics ワークスペース** では、最初の 31 日間、最大で 1 日 10 GB のログ データを無料で取り込むことができます。 新しいワークスペースには、3 日を経過していないワークスペースが含まれます。

   31 日間の試用期間中、Log Analytics のデータ インジェストと Microsoft Sentinel の料金が免除されます。 この無料試用版には、Azure テナントごとに 20 個のワークスペースという制限が適用されます。

- **既存の Log Analytics ワークスペース** では、追加のコストなしで Microsoft Sentinel を有効にできます。 既存のワークスペースには、作成後の経過時間が 3 日を超えているワークスペースが含まれます。

   31 日間の試用期間中、Microsoft Sentinel の料金のみが免除されます。

これらの制限を超える使用量については、[Microsoft Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel)に関するページに記載されている価格に従って課金されます。 [自動化](automation.md)と[独自の機械学習](bring-your-own-ml.md)用の追加機能に関連する料金は、無料試用中であっても適用されます。

> [!TIP]
> 無料試用中は、Microsoft Sentinel の **[ニュースとガイド] > [無料試用版]** タブで、コスト管理やトレーニングなどのリソースを利用できます。
>
> このタブには、無料試用版の日付と、有効期限が切れるまでの残り日数に関する詳細も表示されます。
>
### <a name="free-data-sources"></a>無料データ ソース

次のデータ ソースは、Microsoft Sentinel では無料です。

- Azure アクティビティ ログ。
- Office 365 監査ログ (すべての SharePoint アクティビティ、Exchange 管理者アクティビティ、Teams を含む)。
- Microsoft Defender for Cloud、Microsoft 365 Defender、Microsoft Defender for Office 365、Microsoft Defender for Identity、Microsoft Defender for Endpoint からのアラートなどのセキュリティ アラート。
- Microsoft Defender for Cloud と Microsoft Defender for Cloud Apps のアラート。 ただし、Microsoft 365 Defender、Defender for Cloud Apps、Azure Active Directory (Azure AD)、Azure Information Protection (AIP) の一部のデータの種類の生ログは有料です。

次の表は、Microsoft Sentinel で有効にできる無料のデータ ソースの一覧です。 Microsoft 365 Defender や Defender for Cloud Apps などの一部のデータ コネクタには、無料と有料両方のデータの種類が含まれます。

| Microsoft Sentinel データ コネクタ   | データ型 | 無料または有料 |
|-------------------------------------|--------------------------------|------------------|
| **Azure Activity Logs**         | AzureActivity                  | Free             |
| **Azure AD Identity Protection**         | SecurityAlert (IPC)                  | Free             |
| **Office 365**                     | OfficeActivity (SharePoint)    | Free|
|| OfficeActivity (Exchange)|Free|
|| OfficeActivity (Teams)          | Free|
| **Microsoft Defender for Cloud**                  | SecurityAlert (Defender for Cloud)             | Free             |
| **Microsoft Defender for IoT**          | SecurityAlert (Defender for IoT)     | Free             |
| **Microsoft 365 Defender**          | SecurityIncident | Free|
||SecurityAlert| Free|
||DeviceEvents                    | 有料|
||DeviceFileEvents                | 有料|
||DeviceImageLoadEvents           | 有料|
||DeviceInfo                      | 有料|
||DeviceLogonEvents               | 有料|
||DeviceNetworkEvents             | 有料|
||DeviceNetworkInfo               | 有料|
||DeviceProcessEvents             | 有料|
||DeviceRegistryEvents            | 有料|
||DeviceFileCertificateInfo       | 有料|
| **Microsoft Defender for Endpoint** | SecurityAlert (MDATP)          | Free             |
| **Microsoft Defender for Identity** | SecurityAlert (AATP)           | Free             |
| **Microsoft Defender for Cloud Apps**   | SecurityAlert (Defender for Cloud Apps)           | Free             |
||MCASShadowITReporting           | 有料|

無料と有料両方のデータの種類が含まれるデータ コネクタの場合は、有効にするデータの種類を選択できます。

![無料のセキュリティ アラートが選択され、有料の MCASShadowITReporting が選択されていない、Defender for Cloud Apps の [データ コネクタ] ページを示すスクリーンショット。](media/billing/data-types.png)

無料および有料のデータ ソースとコネクタの詳細については、「[データ ソースの接続](connect-data-sources.md)」を参照してください。

> [!NOTE]
> 一覧でパブリック プレビューとして示されているデータ コネクタには、コストは発生しません。 データ コネクタでは、一般提供 (GA) になった後でのみコストが発生します。
>

## <a name="estimate-microsoft-sentinel-costs"></a>Microsoft Sentinel のコストを見積もる

Microsoft Sentinel をまだ使用していない場合は、[Microsoft Sentinel 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel)を使用して、Microsoft Sentinel 使用時の予想コストを見積もることができます。 検索ボックスに「*Microsoft Sentinel*」と入力し、結果の Microsoft Sentinel タイルを選択します。 料金計算ツールを使用すると、予想されるデータの取り込みと保持期間に基づいて、考え得るコストを見積もることができます。

たとえば、Microsoft Sentinel に取り込むことが予想される 1 日当たりの GB 数と、ワークスペースのリージョンを入力できます。 計算ツールにより、これらのコンポーネント全体の 1 か月の総コストが提供されます。

- Log Analytics のデータ インジェスト
- Microsoft Sentinel のデータ分析
- Log Analytics のデータ保持

## <a name="manage-and-monitor-microsoft-sentinel-costs"></a>Microsoft Sentinel のコストを管理および監視する

Microsoft Sentinel で Azure リソースを使用する場合、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数など) やユニット使用量 (バイト、メガバイトなど) によって異なります。 Microsoft Sentinel を使い始めるとすぐにコストが発生し、[コスト分析](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、Microsoft Sentinel のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

[Azure Cost Management + Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md) ハブには便利な機能があります。 Azure portal で **Cost Management + Billing** を開いた後、左側のナビゲーションで **[コスト管理]** を選択し、Azure サブスクリプションやリソース グループなど、調査する [スコープ](..//cost-management-billing/costs/understand-work-scopes.md)またはリソースのセットを選択します。

**[コスト分析]** 画面には、Azure の使用状況とコストの詳細なビューと、さまざまな制御やフィルターを適用するためのオプションが表示されます。

たとえば、特定の期間の日単位のコストのグラフを表示するには:

1. **[表示]** フィールドのドロップダウン キャレットを選択し、 **[累積コスト]** または **[1 日あたりのコスト]** を選択します。
1. 日付フィールドのドロップダウン キャレットを選択して、日付範囲を選択します。
1. **[細分性]** の横にあるドロップダウン キャレットを選択し、 **[日次]** を選択します。

> [!NOTE]
> ポータルの一部の使用状況グラフの **[Security Insights]\(セキュリティ分析情報\)** には、Microsoft Sentinel のデータ インジェスト量が表示されます。

Microsoft Sentinel の価格レベルには、Log Analytics の料金は含まれません。 Log Analytics の価格レベルのコミットメントを変更するには、「[価格レベルの変更](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier)」を参照してください。

詳細については、「[予算を作成する](#create-budgets)」および「[Microsoft Sentinel のコストを管理して削減するその他の方法](#other-ways-to-manage-and-reduce-microsoft-sentinel-costs)」を参照してください。

### <a name="using-azure-prepayment-with-microsoft-sentinel"></a>Microsoft Sentinel での Azure 前払いの使用

Microsoft Sentinel の料金は、Azure 前払いのクレジットで支払うことができます。 ただし、Azure 前払いクレジットを使用して、サードパーティ組織の製品やサービス、または Azure Marketplace の製品に対する請求を支払うすることはできません。

### <a name="run-queries-to-understand-your-data-ingestion"></a>クエリを実行して、データ インジェストを把握する

Microsoft Sentinel では、広範なクエリ言語を使用して、膨大な量の運用データの分析、操作、分析情報の抽出が数秒で行われます。 データ インジェストの量を把握するために使用できるいくつかの Kusto クエリを次に示します。

ソリューション別にデータ インジェストの量を表示するには、次のクエリを実行します。

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

データの種類別にデータ インジェストの量を表示するには、次のクエリを実行します。

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```

ソリューションとデータの種類の両方ごとにデータ インジェストの量を表示するには、次のクエリを実行します。

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>ブックをデプロイして、データ インジェストを視覚化する

**ワークスペース使用状況レポート ブック** により、ワークスペースのデータ消費量、コスト、使用統計が提供されます。 ブックでは、ワークスペースのデータ インジェストの状態と、無料データと課金対象データの量が示されます。 ブックのロジックを使用して、データ インジェストとコストを監視し、カスタム ビューやルールベースのアラートを作成することができます。

また、このブックではインジェストの詳細も提供されます。 ブックにより、ワークスペース内のデータがデータ テーブル別に分割され、テーブルとエントリごとの量が提供されて、インジェスト パターンの理解を深めるのに役立ちます。

ワークスペース使用状況レポート ブックを有効にするには:

1. Microsoft Sentinel の左側のナビゲーションで、 **[脅威の管理]**  >  **[ブック]** を選択します。
1. 検索バーに「*ワークスペース使用状況*」と入力し、 **[Workspace Usage Report]\(ワークスペース使用状況レポート\)** を選択します。
1. ブックをそのまま使用するには **[テンプレートの表示]** を選択し、ブックの編集可能なコピーを作成するには **[保存]** を選択します。 コピーを保存する場合は、 **[保存されたブックの表示]** を選択します。
1. ブックで、表示する **サブスクリプション** と **ワークスペース** を選択し、**TimeRange** に表示する時間枠を設定します。 **[ヘルプの表示]** トグルを **[はい]** に設定すると、ブックにその場で説明が表示されます。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 コスト データのエクスポートは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。

さらに細かく監視したい場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときに使用可能なフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

### <a name="use-a-playbook-for-cost-management-alerts"></a>コスト管理のアラートにプレイブックを使用する

Microsoft Sentinel の予算を管理するために、コスト管理のプレイブックを作成できます。 Microsoft Sentinel ワークスペースが特定の期間について定義した予算を超えた場合、プレイブックによってアラートが送信されます。

Microsoft Sentinel GitHub コミュニティから、GitHub で [`Send-IngestionCostAlert`](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) コスト管理プレイブックが提供されています。 このプレイブックは繰り返しトリガーによってアクティブ化され、高い柔軟性を備えています。 要件に基づいて、実行頻度、インジェスト量、トリガーするメッセージを制御できます。

### <a name="define-a-data-volume-cap-in-log-analytics"></a>Log Analytics でデータ量の上限を定義する

Log Analytics では、ワークスペースの 1 日のインジェストを制限する 1 日の量の上限を有効にできます。 1 日の上限は、データ量の予期しない増加を管理し、制限内に留め、計画外の料金を制限するのに役立ちます。

1 日の量の上限を定義するには、Log Analytics ワークスペースの左側のナビゲーションで **[使用とコストの見積もり]** を選択し、 **[日次上限]** を選択します。 **[オン]** を選択し、1 日の量の上限を入力して、 **[OK]** を選択します。

![[使用とコストの見積もり] 画面と [日次上限] ウィンドウを示すスクリーンショット。](media/billing/daily-cap.png)

**[使用とコストの見積もり]** 画面には、過去 31 日間に取り込まれたデータ量の傾向と、保持されている合計データ量も示されます。

> [!IMPORTANT]
> 1 日あたりの上限では、すべてのデータの種類の収集が制限されることはありません。 セキュリティ データは上限から除外されます。 Log Analytics での 1 日の上限の管理に関する詳細については、「[ご自分のデータの 1 日の最大ボリュームを管理する](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume)」を参照してください。

## <a name="other-ways-to-manage-and-reduce-microsoft-sentinel-costs"></a>Microsoft Sentinel のコストを管理して削減するその他の方法

データ インジェストと保持のコストを管理するするには:

- データ インジェストの量に基づき、[コミットメント レベルの価格を使用してコストを最適化](#set-or-change-pricing-tier)します。
- [セキュリティ以外のデータを別のワークスペースに分離します](#separate-non-security-data-in-a-different-workspace)。
- [専用クラスターを使用して Log Analytics のコストを最適化します](#optimize-log-analytics-costs-with-dedicated-clusters)。
- [Azure Data Explorer (ADX) を使用して、長期のデータ保持コストを削減します](#reduce-long-term-data-retention-costs-with-adx)。
- [Windows セキュリティ イベントにデータ収集ルールを使用します](#use-data-collection-rules-for-your-windows-security-events)。

### <a name="set-or-change-pricing-tier"></a>価格レベルを設定または変更します

節約が最大になるように最適化するには、インジェスト量を監視して、インジェスト量のパターンと最もよく一致するコミットメント レベルを使用するようにします。 コミットメント レベルを増やしたり減らしたりして、データ量の変化に合わように調整できます。

いつでもコミットメント レベルを増やすことができ、31 日間のコミットメント期間が再開されます。 ただし、従量課金制またはより低いコミットメント レベルに戻すには、31 日のコミットメント期間が終了するまで待つ必要があります。 コミットメント レベルに対する課金は 1 日単位です。

Microsoft Sentinel の現在の価格レベルを確認するには、Microsoft Sentinel の左側のナビゲーションで **[設定]** を選択してから、 **[価格]** タブを選択します。現在の価格レベルには、 **[現在のレベル]** と表示されます。

価格レベルのコミットメントを変更するには、価格ページで他のいずれかのレベルを選択して、 **[適用]** を選択します。 価格レベルを変更するには、Microsoft Sentinel での **共同作成者** または **所有者** ロールが必要です。

![現在の価格レベルとして従量課金制が示されている、Microsoft Sentinel の設定の価格ページのスクリーンショット。](media/billing/pricing.png)

> [!NOTE]
> ポータルの一部の使用状況グラフの **[Security Insights]\(セキュリティ分析情報\)** には、Microsoft Sentinel のデータ インジェスト量が表示されます。

Microsoft Sentinel の価格レベルには、Log Analytics の料金は含まれません。 Log Analytics の価格レベルのコミットメントを変更するには、「[価格レベルの変更](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier)」を参照してください。

### <a name="separate-non-security-data-in-a-different-workspace"></a>セキュリティ以外のデータを別のワークスペースに分離する

Microsoft Sentinel により、Microsoft Sentinel が有効な Log Analytics ワークスペースに取り込まれたすべてのデータが分析されます。 セキュリティ以外の運用データ用に別のワークスペースを用意し、Microsoft Sentinel のコストが発生しないようにするのが最善です。

Microsoft Sentinel で脅威をハンティングまたは調査するときは、これらのスタンドアロン Azure Log Analytics ワークスペースに格納されている運用データにアクセスすることが必要な場合があります。 このデータには、ログ探索エクスペリエンスとブックでクロスワークスペース クエリを使用してアクセスできます。 ただし、すべてのワークスペースで Microsoft Sentinel が有効になっていない限り、クロスワークスペースの分析ルールとハンティング クエリは使用できません。

### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>専用クラスターを使用して Log Analytics のコストを最適化する

少なくとも 500 GB を同じリージョン内の 1 つまたは複数の Microsoft Sentinel ワークスペースに取り込む場合は、コストを削減するために Log Analytics 専用クラスターへの移行を検討してください。 Log Analytics 専用クラスターのコミットメント レベルにより、まとめると合計 500 GB 以上を取り込むすべてのワークスペースのデータ量が集約されます。

Log Analytics 専用クラスターは、Microsoft Sentinel のコミットメント レベルには適用されません。 Microsoft Sentinel のコストは、専用クラスター内のワークスペースごとに引き続き適用されます。

複数の Microsoft Sentinel ワークスペースを Log Analytics 専用クラスターに追加できます。 Microsoft Sentinel に Log Analytics 専用クラスターを使用すると、次のような利点があります。

- クエリに含まれるすべてのワークスペースが専用クラスター内にある場合、クロスワークスペース クエリの実行速度が向上します。 それでも、環境内のワークスペースは可能な限り少なくするのが最善であり、専用クラスターで 1 つのクロスワークスペース クエリに含めることができる[ワークスペースには 100 の制限](../azure-monitor/logs/cross-workspace-query.md)がやはりあります。

- 専用クラスター内のすべてのワークスペースで、クラスターに設定されている Log Analytics コミットメント レベルを共有できます。 ワークスペースごとに個別の Log Analytics コミットメント レベルにコミットする必要がないと、コストを削減して効率化できます。 専用クラスターを有効にすると、1 日あたり 500 GB のインジェストの最小 Log Analytics コミットメント レベルにコミットすることになります。

コストの最適化のために専用クラスターに移行する場合の、その他の考慮事項を次に示します。

- リージョンおよびサブスクリプションごとのクラスターの最大数は 2 です。
- クラスターにリンクされているすべてのワークスペースは、同じリージョンに存在する必要があります。
- クラスターにリンクされるワークスペースの最大数は 1000 です。
- クラスターからリンクされたワークスペースのリンクを解除することができます。 特定のワークスペースでのリンク操作の回数は、30 日間に 2 回に制限されます。
- 既存のワークスペースをカスタマー マネージド キー (CMK) クラスターに移動することはできません。 クラスター内にワークスペースを作成する必要があります。
- 別のリソース グループまたはサブスクリプションへのクラスターの移動は、現時点ではサポートされていません。
- ワークスペースが別のクラスターにリンクされている場合、クラスターへのワークスペースのリンクは失敗します。

専用クラスターの詳細については、「[Log Analytics 専用クラスター](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters)」を参照してください。

### <a name="reduce-long-term-data-retention-costs-with-adx"></a>ADX を使用して長期のデータ保持コストを削減する

Microsoft Sentinel のデータ保持は、最初の 90 日間は無料です。 Log Analytics でデータ保持期間を調整するには、左側のナビゲーションで **[使用とコストの見積もり]** を選択してから、 **[データ保持]** を選択し、スライダーを調整します。

Microsoft Sentinel のセキュリティ データの価値は、数か月後には多少失われる可能性があります。 セキュリティ オペレーション センター (SOC) のユーザーは、古いデータには新しいデータほど頻繁にアクセスする必要がない場合がありますが、それでも散発的な調査や監査のためにデータにアクセスすることが必要になる場合があります。 Microsoft Sentinel データの保持コストを削減するには、低コストでの長期データ保持に Azure Data Explorer を使用できます。 Microsoft Sentinel のセキュリティ インテリジェンスを必要としない古いデータに対しては、ADX によってコストと使いやすさの適切なバランスが提供されます。

ADX を使用すると、より低い価格でデータを格納できますが、引き続き Microsoft Sentinel と同様に Kusto クエリ言語 (KQL) のクエリを使用してデータを探索できます。 また、ADX のプロキシ機能を使用して、クロスプラットフォーム クエリを実行できます。 これらのクエリでは、ADX、Application Insights、Microsoft Sentinel、Log Analytics に分散されたデータが集計されて関連付けられます。

詳細については、「[長期的なログ保持のために Azure Data Explorer を統合する](store-logs-in-azure-data-explorer.md)」を参照してください。

### <a name="use-data-collection-rules-for-your-windows-security-events"></a>Windows セキュリティ イベントにデータ収集ルールを使用する

[Windows セキュリティ イベント コネクタ](connect-windows-security-events.md?tabs=LAA)を使用すると、Windows Server が実行されていて、Microsoft Sentinel ワークスペースに接続されている任意のコンピューター (物理サーバー、仮想サーバー、オンプレミス サーバー、任意のクラウド内など) から、セキュリティ イベントをストリーミングできます。 このコネクタによってサポートされている Azure Monitor エージェントでは、データ収集ルールを使用して、各エージェントから収集するデータが定義されています。

データ収集ルールにより、大規模な収集設定の管理が可能になる一方、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 詳細については、「[Azure Monitor エージェント用のデータ収集の構成](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)」を参照してください。

すべてのイベント、最小、共通など、あらかじめ定義されているイベントのセットを選択して取り込むこともできますが、データ収集ルールを使用すると、カスタム フィルターを作成して、取り込む特定のイベントを選択することができます。 Azure Monitor エージェントにより、これらのルールを使用してソースのデータがフィルター処理されてから、選択したイベントだけが取り込まれ、その他はすべて残されます。 特定のイベントを選択して取り込むと、コストを最適化し、いっそう節約するのに役立ちます。

> [!NOTE]
> この画像で示されているコストは、例示だけを目的としたものです。 実際のコストを反映したものではありません。

![Cost Management + Billing のコスト分析画面を示すスクリーンショット。](media/billing/cost-management.png)

さらに、制御を適用することもできます。 たとえば、Microsoft Sentinel に関連するコストのみを表示するには、 **[フィルターの追加]** を選択し、 **[サービス名]** を選択して、サービス名として **Sentinel**、**log analytics**、**azure monitor** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
- Log Analytics のデータ量を減らすためのその他のヒントについては、「[データ量の削減のためのヒント](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume)」を参照してください。
